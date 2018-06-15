---
title: Программирование серверных компонентов на JavaScript для Azure Cosmos DB | Документация Майкрософт
description: Узнайте, как с помощью Azure Cosmos DB писать хранимые процедуры, триггеры базы данных и определяемые пользователем функции (UDF) на JavaScript. Здесь вы найдете советы по программированию баз данных и многое другое.
keywords: Триггеры базы данных, хранимая процедура, хранимая процедура, программа базы данных, хранимая процедура, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: andrl
ms.openlocfilehash: 42acc1ca00e6805df0bce0ee4fc59180b5beb6db
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34614668"
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Программирование Azure Cosmos DB на стороне сервера: хранимые процедуры, триггеры баз данных и определяемые пользователем функции

Узнайте, каким образом интегрированное транзакционное выполнение JavaScript в Azure Cosmos DB позволяет разработчикам создавать **хранимые процедуры**, **триггеры** и **определяемые пользователем функции (UDF)** непосредственно на платформе JavaScript [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/). Интеграция с JavaScript позволяет писать логику приложения, которую можно отправить и выполнить непосредственно в разделе хранения базы данных. 

Прежде чем приступить к работе, просмотрите следующий видеоролик, в котором Эндрю Лю (Andrew Liu) описывает модель программирования баз данных на стороне сервера Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/s0cXdHNlVI0]
>
> 

Затем вернитесь в эту статью, из которой вы узнаете ответы на следующие вопросы:  

* Как написать хранимую процедуру, триггер или определяемую пользователем функцию на JavaScript?
* Каким образом Cosmos DB предоставляет гарантию выполнения принципа ACID?
* Каков принцип работы транзакций в Cosmos DB?
* Что такое пред- и пост-триггеры, и как их написать?
* Как зарегистрировать и выполнить хранимую процедуру, триггер или определяемую пользователем функции RESTful-образом по протоколу HTTP?
* Какие пакеты SDK Cosmos DB доступны для создания и выполнения хранимых процедур, триггеров и определяемых пользователем функций?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Введение в программирование хранимых процедур и определяемых пользователем функций
Подход *«JavaScript — это современный T-SQL»* освобождает разработчиков приложений от сложностей системы, связанных с несоответствием типов и объектно-реляционных технологий сопоставления. Он также имеет ряд своих собственных преимуществ, которые могут быть использованы для создания мощных приложений:  

* **Процедурная логика.** Язык программирования высокого уровня JavaScript предоставляет многофункциональный и удобный интерфейс для реализации бизнес-логики. Вы можете выполнять сложные последовательности операций ближе к данным.
* **Атомарные транзакции.** Cosmos DB гарантирует атомарность операций с базой данных, выполняемых внутри одной хранимой процедуры или триггера. Атомарные транзакции позволяют приложению объединить соответствующие операции в один пакет, который будет выполнен успешно полностью или не будет выполнен вовсе. 
* **Производительность.** Тот факт, что JSON неразрывно связан с системой типов языка Javascript, а также является основной единицей хранения в Cosmos DB, позволяет использовать ряд оптимизаций, таких как отложенная материализация документов JSON в буферном пуле и доступ к ним для исполняемого кода по требованию. Есть еще больший выигрыш в производительности, связанный с доставкой бизнес-логики в базу данных:
  
  * Пакеты — разработчики могут группировать операции, такие как вставка, и запускать их в пакетном режиме. Значительно сокращаются затраты на задержку сетевого трафика и накладные расходы для хранения при создании отдельных операций. 
  * Предварительная компиляция — Cosmos DB предварительно компилирует хранимые процедуры, триггеры и пользовательские функции (UDF), чтобы избежать задержки на компиляцию JavaScript при каждом вызове. Накладные расходы на компиляцию байт-кода для процедурной логики амортизируются до минимального значения.
  * Секвенcирование — многим операции нужен побочный эффект («триггер»), что потенциально задействует выполнение одной или множества вторичных операций хранения. Помимо обеспечения атомарности, приводит к повышению производительности при переносе на сервер. 
* **Инкапсуляция.** Хранимые процедуры могут быть использованы для группировки бизнес-логики в одном месте. Такой подход имеет два преимущества.
  * Добавление уровня абстракции поверх исходных данных, что позволяет архитекторам данных развивать свои приложения независимо от данных. Уровень абстракции особенно удобен, когда данные не имеют схемы, чтобы поддерживать хрупкие взаимозависимости между данными, если приложению придется работать с данными напрямую.  
  * Эта абстракция позволяет предприятиям сохранить свои данные в безопасности, упрощая доступ к ним из сценариев.  

Создание и выполнение триггеров баз данных, хранимых процедур и операторов пользовательских запросов поддерживается с помощью [портала Azure](https://portal.azure.com), [REST API](/rest/api/cosmos-db/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases) и [клиентских пакетов SDK](sql-api-sdk-dotnet.md) на различных платформах, включая .NET, Node.js и JavaScript.

Для иллюстрации синтаксиса и использования хранимых процедур, триггеров и пользовательских функций в этом учебнике используется [пакет SDK для Node.js с обещаниями Q](http://azure.github.io/azure-documentdb-node-q/).   

## <a name="stored-procedures"></a>Хранимые процедуры
### <a name="example-write-a-stored-procedure"></a>Пример: написание хранимой процедуры
Давайте начнем с простой хранимой процедуры, которая возвращает «Hello World» в ответ.

    var helloWorldStoredProc = {
        id: "helloWorld",
        serverScript: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }


Хранимые процедуры регистрируются в коллекциях и могут работать с любым документом и вложением, существующим в этой коллекции. Следующий фрагмент кода показывает, как зарегистрировать хранимую процедуру HelloWorld в коллекции. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


После того, как хранимая процедура зарегистрирована, мы можем выполнить ее для коллекции и получить результаты в клиент. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


Контекст объекта предоставляет доступ ко всем операциям, которые можно выполнить в хранилище Cosmos DB, а также доступ к объектам запросов и ответов. В этом примере вы применяете объект ответа, чтобы создать текст ответа для отправки клиенту. Более подробные сведения см. в [документации к серверному пакету SDK JavaScript для Azure Cosmos DB](http://azure.github.io/azure-documentdb-js-server/).  

Подробнее остановимся на этом примере и добавим в хранимую процедуру дополнительную функциональность для баз данных. Хранимые процедуры могут создавать, обновлять, читать, запрашивать и удалять документы и приложения внутри коллекции.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Пример: написание хранимой процедуры для создания документа
В следующем фрагменте показано, как использовать объект контекста для взаимодействия с ресурсами Cosmos DB.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        serverScript: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Эта хранимая процедура принимает в качестве входного параметра documentToCreate, тело документа, который будет создан в текущей коллекции. Все эти операции являются асинхронными и зависят от функций обратных вызовов JavaScript. Функция обратного вызова имеет два параметра: один для объекта ошибки в случае, если операция не выполнится, и один для созданного объекта. Внутри функции обратного вызова пользователи могут либо обработать исключение, либо вызвать ошибку. Если обратного вызова не предусмотрено и возникла ошибка, то среда выполнения Azure Cosmos DB выдаст ошибку.   

В приведенном выше примере обратный вызов возвращает ошибку, если операция не удалась. В противном случае он устанавливает идентификатор созданного документа в качестве текста ответа клиенту. Таким образом, данная хранимая процедура выполняется с входными параметрами.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;

            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };

            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });


Эту хранимую процедуру можно изменить так, чтобы принимать в качестве входных данных массив текстов документов и создавать их все в одном выполнении хранимой процедуры, не выполняя отдельные сетевые запросы для каждого из них. Такую хранимую процедуру можно использовать для реализации эффективного массового импорта данных в Cosmos DB (обсуждается далее в этой статье).   

Описанный пример продемонстрировал, как использовать хранимые процедуры. Далее в этом руководстве вы изучите триггеры и пользовательские функции (UDF).

### <a name="known-issues"></a>Известные проблемы

При определении хранимой процедуры с помощью портала Azure входные параметры всегда отправляются в хранимую процедуру виде строки. Даже если передать массив строк в качестве входных данных, массив преобразуется в строку и отправляются в хранимую процедуру. Чтобы обойти эту проблему, можно определить функцию в хранимой процедуре для анализа строки как массива. Ниже приведен пример кода для анализа строки как массива. 

``` 
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);
    
    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

## <a name="database-program-transactions"></a>Транзакции программы базы данных
Транзакции в типичной базе данных могут быть определены как последовательность операций, выполняемых в одной логической единице работы. Каждая транзакция предоставляет **гарантию выполнения принципа ACID**. Широко известный акроним ACID обозначает четыре свойства: Atomicity (атомарность), Consistency (согласованность), Isolation (изолированность) и Durability (надежность).  

Вкратце, атомарность гарантирует, что все действия, проделанные в транзакции, рассматриваются как единое целое, они могут быть либо выполнены все, либо не выполнены вообще. Согласованность гарантирует, что данные всегда находятся в правильном внутреннем состоянии между транзакциями. Изолированность гарантирует, что никакие две транзакции не будут мешать друг другу. Как правило, большинство коммерческих систем обеспечивают несколько уровней изолированности, которые могут быть использованы в зависимости от потребностей приложений. Надежность гарантирует, что любое изменение, которое было совершено в базе данных, всегда будет присутствовать.   

В Cosmos DB JavaScript размещается в том же пространстве памяти, что и базы данных. Следовательно, запросы, хранимые процедуры и триггеры выполняются в том же пространстве базы данных. Эта возможность позволяет Cosmos DB соблюдать принцип ACID для всех операций, которые являются частью одной хранимой процедуры или триггера. Рассмотрим следующее определение хранимой процедуры:

    // JavaScript source code
    var exchangeItemsSproc = {
        id: "exchangeItems",
        serverScript: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            var player1Document, player2Document;

            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);

                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];

                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });

            if (!accept) throw "Unable to read player details, abort ";

            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;

                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";

                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });

                        if (!accept2) throw "Unable to update player 2, abort";
                    });

                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }

    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Эта хранимая процедура использует транзакции внутри игрового приложения, позволяющего торговать товарами между двумя игроками в одной операции. Хранимая процедура пытается прочитать два документа, каждый из которых соответствует идентификаторам игроков, поступивших в качестве аргументов. Если оба документа игрока будут найдены, то хранимая процедура обновляет документы, меняя их элементы. При обнаружении ошибок вызывается исключение JavaScript, которое неявно отменяет транзакцию.

Если коллекция, в которой регистрируется хранимая процедура, является коллекцией с одной секцией, транзакция действует для всех документов в коллекции. Если коллекция секционирована, то хранимые процедуры выполняются в области транзакции для ключа одной секции. Поэтому каждая хранимая процедура должна включать значение ключа секции, соответствующего области применения транзакции. Ознакомьтесь с дополнительными сведениями о [ценах на секционирование Azure Cosmos DB](partition-data.md).

### <a name="commit-and-rollback"></a>Фиксация и откат транзакций
Транзакции глубоко и изначально интегрированы в модель программирования JavaScript в Cosmos DB. Внутри функции JavaScript все операции автоматически обернуты в одной транзакции. Если JavaScript завершает выполнение функции без исключения, операции в базе данных фиксируются. По сути, это аналог операторов BEGIN TRANSACTION и COMMIT TRANSACTION в реляционных базах данных, подразумевающихся в Cosmos DB.  

Если есть исключения, которые возникли в сценарии, среда выполнения JavaScript в Cosmos DB откатит всю транзакцию. Как показано в предыдущем примере, выбрасывание исключения аналогично оператору ROLLBACK TRANSACTION в Cosmos DB.

### <a name="data-consistency"></a>Согласованность данных
Хранимые процедуры и триггеры всегда выполняются на основной реплике контейнера Azure Cosmos DB. Гарантируется, что операции чтения в хранимых процедурах обеспечивают сильную согласованность. Запросы, использующие пользовательские функции, можно выполнять на первичной или любой вторичной реплике, но при выборе реплики вам следует учитывать требуемый уровень согласованности.

## <a name="bounded-execution"></a>Ограниченное выполнение
Все операции Cosmos DB должны завершаться за определенный на уровне сервера период времени. Это ограничение также относится к функциям JavaScript (хранимые процедуры, триггеры и определяемые пользователем функции). Если операция не завершается в этот период времени, транзакция откатывается. Функции JavaScript должны завершаться за определенный период времени или поддерживать модель продолжения работы для пакетной или поэтапной обработки.  

Для упрощения разработки хранимых процедур и триггеров и ограничения времени их выполнения все функции в соответствии с объектом коллекции (для создания, чтения, замены и удаления документов и вложений) возвращают логическое значение, которое представляет, будет ли эта операция завершена. Если это значение является ложным, то это означает, что срок выполнения истекает в ближайшее время и что процедура должна завершить выполнение.  Операции, предшествующие первой неакцептованной операции сохранения данных, помещаются в очередь и гарантированно будут завершены, если хранимая процедура завершается в срок и очередь пуста.  

Функции JavaScript также ограничены в потреблении ресурсов. Cosmos DB резервирует пропускную способность для коллекции или для набора контейнеров. Пропускная способность выражается в форме нормализованных единиц потребления ресурсов процессора, памяти и ввода-вывода, именуемых «единица запроса» или RU. Функции JavaScript могут потенциально использовать большое количество RU в течение короткого времени и, возможно, получить ограничение скорости выполнения при достижении предела, выделенного для коллекции. Ресурсоемкие хранимые процедуры также могут быть помещены в режим ожидания, чтобы обеспечить доступность примитивных операций с базами данных.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Пример. Массовый импорт данных в программу базы данных
Ниже приведен пример хранимой процедуры, реализующей массовый импорт документов в коллекцию. Обратите внимание, как хранимая процедура обрабатывает ограниченное выполнение, проверяя логическое значение, возвращаемое из createDocument, а затем использует подсчет документов, помещенных в каждом вызове хранимой процедуры, чтобы отслеживать и возобновить пакетное задание.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();

        // The count of imported docs, also used as current doc index.
        var count = 0;

        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");

        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }

        // Call the create API to create a document.
        tryCreate(docs[count], callback);

        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);

            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }

        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;

            // One more document has been inserted, increment the count.
            count++;

            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a> Триггеры базы данных
### <a name="database-pre-triggers"></a>Триггеры базы данных со срабатыванием до наступления события
Cosmos DB предоставляет триггеры, которые выполняются или запускаются при выполнении операций над документом. Например, можно задать предварительный триггер, когда вы создаете документ, и он будет запускаться перед созданием документа. Следующий пример демонстрирует, как можно использовать предварительные триггеры для проверки свойств создаваемого документа.

    var validateDocumentContentsTrigger = {
        id: "validateDocumentContents",
        serverScript: function validate() {
            var context = getContext();
            var request = context.getRequest();

            // document to be created in the current operation
            var documentToCreate = request.getBody();

            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }

            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


И соответствующий клиентский код Node.js для регистрации триггера:

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };

            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Триггеры со срабатыванием до наступления события не могут иметь никаких входных параметров. Объект запроса может быть использован для манипулирования сообщением запроса, связанного с операцией. Здесь триггеры со срабатыванием до наступления события запускаются до создания документа, и сообщение тела запроса содержит документ, который будет создан в формате JSON.   

При регистрации триггеров пользователи могут указать операции, с которыми они запускаются. Этот триггер был создан с помощью TriggerOperation.Create, а значит вы не можете использовать его в операции замены, как показано в следующем коде.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Пост-триггеры базы данных
Пост-триггеры, как и пред-триггеры, связаны с операцией на документе и не принимают никаких входных параметров. Они запускаются **после** завершения операции и получают доступ к ответному сообщению, которое отправляется клиенту.   

В следующем примере используются триггеры со срабатыванием после наступления события:

    var updateMetadataTrigger = {
        id: "updateMetadata",
        serverScript: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            // document that was created
            var createdDocument = response.getBody();

            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";

            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';

                         var metadataDocument = documents[0];

                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                            
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


Триггер можно зарегистрировать, как показано в следующем примере.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };

            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Этот триггер запрашивает метаданные документа и обновляет их дополнительными сведениями о вновь созданных документах.  

Важно отметить одну вещь, а именно — **транзакционное** выполнение триггеров в Cosmos DB. Этот триггер со срабатыванием после наступления события работает как часть той же самой транзакции при создании оригинального документа. Поэтому, если вы создадите исключение в триггере (скажем, если не удастся обновить документ метаданных), вся транзакция будет прервана и произойдет ее откат. Ни один документ не будет создан, будет возвращено исключение.  

## <a id="udf"></a>Функции, определяемые пользователем
Функции, определяемые пользователем (UDF), позволяют расширить грамматику языка SQL-запросов к Azure Cosmos DB и реализовать пользовательскую бизнес-логику. Функции можно вызывать только из запросов. Они не имеют доступа к объектам контекста и предназначены для использования только в качестве вычислимых JavaScript-сценариев. Таким образом, UDF могут запускаться только на дополнительных репликах службы Cosmos DB.  

Следующий пример создает UDF для расчета налога на прибыль на основе ставок для различных доходов, а затем использует его в запросе, чтобы найти всех людей, которые заплатили более 20 000 долларов США в виде налогов.

    var taxUdf = {
        id: "tax",
        serverScript: function tax(income) {

            if(income == undefined) 
                throw 'no input';

            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


UDF впоследствии могут быть использованы в запросах как в следующем примере:

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);

            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>API запросов, интегрированный в язык JavaScript
Кроме выдачи запросов с использованием грамматики SQL Azure Cosmos DB, серверный пакет SDK позволяет создавать оптимизированные запросы с помощью гибкого интерфейса JavaScript без знания SQL. API запросов в JavaScript позволяет программно создавать запросы, передавая предикатные функции в образующие цепочку вызовы функций. Для этого используется синтаксис, который понимают встроенные элементы массива ECMAScript5 и популярные библиотеки JavaScript, например Lodash. Чтобы запросы были эффективно выполнены с помощью индексов Azure Cosmos DB, их анализирует среда выполнения JavaScript.

> [!NOTE]
> `__` (двойное подчеркивание) является псевдонимом для `getContext().getCollection()`.
> <br/>
> Другими словами, вы можете использовать `__` или `getContext().getCollection()`, чтобы получить доступ к API запросов JavaScript.
> 
> 

Поддерживаемые функции включают:

<ul>
<li>
<b>chain() ... .value([обратный вызов] [, параметры])</b>
<ul>
<li>
Начинает вызов по цепочке, который должен завершаться value().
</li>
</ul>
</li>
<li>
<b>filter(predicateFunction [, параметры] [, обратный вызов])</b>
<ul>
<li>
Фильтрует входные данные с помощью функции предиката, которая возвращает значение true или false для фильтрации входных документов в наборе результатов. Эта функция действует так же, как предложение WHERE в SQL.
</li>
</ul>
</li>
<li>
<b>map(transformationFunction [, параметры] [, обратный вызов])</b>
<ul>
<li>
Применяет проекцию, заданную функцией преобразования, которая сопоставляет каждый входной элемент с объектом или значением JavaScript. Эта функция действует так же, как предложение SELECT в SQL.
</li>
</ul>
</li>
<li>
<b>pluck([propertyName] [, параметры] [, обратный вызов])</b>
<ul>
<li>
Эта функция заменяет собой сопоставление, которое извлекает значение определенного свойства из каждого входного элемента.
</li>
</ul>
</li>
<li>
<b>flatten([isShallow] [, параметры] [, обратный вызов])</b>
<ul>
<li>
Собирает и объединяет массивы для каждого входного элемента в один массив. Эта функция действует так же, как предложение SelectMany в LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy([предикат] [, параметры] [, обратный вызов])</b>
<ul>
<li>
Используя заданный предикат, создает новый набор документов путем сортировки документов во входном потоке документов по возрастанию. Эта функция действует так же, как предложение ORDER BY в SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending([предикат] [, параметры] [, обратный вызов])</b>
<ul>
<li>
Используя заданный предикат, создает новый набор документов путем сортировки документов во входном потоке документов по убыванию. Эта функция действует так же, как предложение ORDER BY x DESC в SQL.
</li>
</ul>
</li>
</ul>


Если конструкции JavaScript включены в предикатную функцию и/или селектор, они автоматически оптимизированы для непосредственного выполнения в индексах Azure Cosmos DB:

* Простые операторы: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* литералы, включая литерал объекта: {};
* var, return.

Следующие конструкции JavaScript не оптимизированы для использования в индексах Azure Cosmos DB:

* Поток управления (например if, for или while)
* вызовы функций.

Чтобы узнать больше, ознакомьтесь с [документацией по JavaScript в серверной части](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Пример создания хранимой процедуры с помощью API запросов в JavaScript
В следующем образце кода приведен пример использования API запросов в JavaScript для создания хранимой процедуры. Хранимая процедура вставляет заданный входным параметром документ, после чего обновляет метаданные документа с помощью метода `__.filter()` , а также на основе свойств размера входного документа minSize, maxSize и totalSize.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>Таблица соответствия запросов SQL и Javascript
В следующей таблице представлены разные запросы SQL и соответствующие им запросы JavaScript.

Как и запросы SQL, ключи свойств документов (например, `doc.id`) чувствительны к регистру.

|SQL| API запроса JavaScript|Описание приведено ниже|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|1|
|SELECT docs.id, docs.message AS msg, docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|SELECT *<br>FROM docs<br>WHERE docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|3|
|SELECT *<br>FROM docs<br>WHERE ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|4.|
|SELECT docs.id, docs.message AS msg<br>FROM docs<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|5|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.Tags && Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|6|

Далее следуют описания, которые объясняют каждый запрос из приведенной выше таблицы.
1. Возвращает все документы (с разбивкой на страницы с отметками "продолжить").
2. Создает проекцию идентификатора, сообщения (msg) и действия для всех документов.
3. Запрашивает документы с предикатом: id = "X998_Y998".
4. Запрашивает все документы, имеющие свойство Tags. Tags является массивом, содержащим значение 123.
5. Запрашивает документы с предикатом id = "X998_Y998" и затем выполняет проекцию идентификатора и сообщения (msg).
6. Выполняет фильтрацию для получения документов, у которых есть свойство массива Tags, и сортирует отобранные документы по системному свойству метки отметки _ts, после чего выполняет проекцию и объединение массива Tags.


## <a name="runtime-support"></a>Поддержка времени выполнения
[Серверный API JavaScript для Azure Cosmos DB](http://azure.github.io/azure-documentdb-js-server/) поддерживает большинство возможностей языка JavaScript в соответствии со стандартами [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Безопасность
Хранимые процедуры и триггеры JavaScript выполняются в изолированной среде, поэтому действия одного сценария не оказывают какого-либо воздействия на другие сценарии, за счет изоляции снимков транзакций на уровне базы данных. Среды выполнения объединяются в пул, но удаляются из контекста после каждого запуска. Следовательно, они гарантированно безопасны от появления непреднамеренных побочных эффектов друг для друга.

### <a name="pre-compilation"></a>Предварительная компиляция
Хранимые процедуры, триггеры и пользовательские функции неявно прекомпилированы в формате байт-кода, чтобы избежать затрат на компиляцию во время каждого вызова сценария. Предварительная компиляция гарантирует высокую скорость и низкие затраты на вызовы хранимых процедур.

## <a name="client-sdk-support"></a>Поддержка клиентских пакетов SDK
В дополнение к API Azure Cosmos DB для [Node.js](sql-api-sdk-node.md) Azure Cosmos DB поддерживает [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/) и [пакеты SDK для Python](sql-api-sdk-python.md) для API SQL. Хранимые процедуры, триггеры и пользовательские функции могут быть созданы и выполнены с использованием любого из этих пакетов SDK. В следующем примере показано, как создать и выполнить хранимую процедуру с помощью клиента .NET. Обратите внимание, что типы .NET передаются в хранимую процедуру и возвращаются в виде объектов JSON.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    

                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }

                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
             }"
    };

    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;

    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "ValidateDocumentAge"), document, 1920);


В этом примере показано, как создать триггер со срабатыванием до наступления события, а также документ со включенным триггером с помощью [API-интерфейса .NET для SQL](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet). 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };

    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


А в следующем примере показано, как создать определенную пользователем функцию для последующего использования в [SQL-запросе](sql-api-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };

    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>ИНТЕРФЕЙС REST API
Все операции Azure Cosmos DB могут быть выполнены в RESTful-образе. Хранимые процедуры, триггеры и пользовательские функции могут быть зарегистрированы в соответствии с коллекцией с помощью команды POST HTTP. Ниже показано, как правильно зарегистрировать хранимую процедуру:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT


    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


Хранимая процедура регистрируется путем выполнения запроса POST с URI "dbs/testdb/colls/testColl/sprocs" с телом, содержащим создаваемую хранимую процедуру. Триггеры и функции UDF могут быть зарегистрированы аналогичным образом путем выполнения команды POST с идентификаторами /triggers и /udfs соответственно.
Эту хранимую процедуру затем можно выполнить, отправив запрос POST со ссылкой на ее ресурс.

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Здесь входные параметры в хранимую процедуру передаются в теле запроса. Входные параметры передаются в виде массива JSON. Хранимая процедура принимает первый параметр как документ, который является телом ответа. Вы получите примерно такой ответ:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: 'Autumn of the Patriarch',
      id: 'V7tQANV3rAkDAAAAAAAAAA==',
      ts: 1407830727,
      self: 'dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/',
      etag: '6c006596-0000-0000-0000-53e9cac70000',
      attachments: 'attachments/',
      Price: 200
    }


Триггеры, в отличие от хранимых процедур, не могут быть выполнены непосредственно. Вместо этого они выполняются как часть операции над документом. С помощью заголовков HTTP вы можете указать триггеры, которые будут выполняться по этому запросу. Ниже приведен код запроса для создания документа.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger

    {
       "name": "newDocument",
       "title": "The Wizard of Oz",
       "author": "Frank Baum",
       "pages": 92
    }


Здесь в заголовке x-ms-documentdb-pre-trigger-include указан триггер со срабатыванием до наступления события, который будет запущен при запросе. Соответственно, любые триггер со срабатыванием после наступления события могут быть приведены заголовке x-ms-documentdb-post-trigger-include. Для данного запроса могут быть определены оба вида триггеров.

## <a name="sample-code"></a>Пример кода
Дополнительные примеры кода, используемого на стороне сервера (включая операции [массового удаления](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js) и [обновления](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)), представлены в [репозитории GitHub](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Вы создали отличную хранимую процедуру и хотите ей поделиться? Внесите ее в репозиторий и создайте запрос на вытягивание! 

## <a name="next-steps"></a>Дополнительная информация
После создания хранимых процедур, триггеров и определяемых пользователем функций вы сможете загружать их на портале Azure и просматривать их с помощью обозревателя данных.

Кроме того, при изучении программирования Azure Cosmos DB на стороне сервера могут оказаться полезными следующие ссылки и ресурсы:

* [Пакет SDK для DocumentDB .NET: скачивание и заметки о выпуске](sql-api-sdk-dotnet.md).
* [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Безопасность и расширяемость переносной базы данных](http://dl.acm.org/citation.cfm?id=276339) 
* [Сервис-ориентированная архитектура баз данных](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Размещение среды выполнения .NET на Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)

