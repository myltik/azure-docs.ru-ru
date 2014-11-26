<properties title="DocumentDB programming: Stored procedures, triggers, and UDFs" pageTitle="DocumentDB programming: Stored procedures, triggers, and UDFs | Azure" description="Find out how for Document DB you can write stored procedures, triggers, and user defined functions (UDFs) natively in JavaScript." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"  services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong" />

# Программирование DocumentDB: Хранимые процедуры, триггеры и определяемые пользователем функции

## Введение

Интегрированное транзакционное выполнение JavaScript в DocumentDB позволяет разработчикам писать **хранимые процедуры**, **триггеры** и **определяемые пользователем функции (UDF)** непосредственно на JavaScript. Это позволяет разработчикам писать логику приложения, которая может быть отправлена и выполнена непосредственно в разделе хранения базы данных.

Подход «*JavaScript — это современный T-SQL*» освобождает разработчиков приложений от сложностей системы, связанных с несоответствием типов и объектно-реляционных технологий сопоставления. Он также имеет ряд своих собственных преимуществ, которые могут быть использованы для создания мощных приложений:

-   **Процедурная логика:** JavaScript как язык программирования высокого уровня, обеспечивает насыщенный и знакомый интерфейс для реализации бизнес-логики. Вы можете выполнять сложные последовательности операций ближе к данным.

-   **Атомарные транзакции:**DocumentDB гарантирует атомарность операций с базой данных, выполняемых внутри одной хранимой процедуры или триггера. Это позволяет приложению объединить соответствующие операции в пакетном режиме; таким образом, либо все из них будут выполнены, либо ни одна.

-   **Производительность:**Тот факт, что JSON неразрывно связан с системой типов языка Javascript, а также является основной единицей хранения в DocumentDB, позволяет использовать ряд оптимизаций, таких как отложенная материализация документов JSON в буферном пуле и доступ к ним исполняемому коду по требованию. Есть еще больший выигрыш в производительности, связанный с доставкой бизнес-логики в базу данных:

    -   Пакеты — разработчики могут группировать операции, такие как вставка, и запускать их в пакетном режиме. Вклад задержек сетевого трафика и накладных расходов для хранения при создании отдельных операций значительно сокращается.
    -   Предварительная компиляция — DocumentDB предварительно компилирует хранимые процедуры, триггеры и пользовательские функции (UDF), чтобы избежать задержки JavaScript при компиляции для каждого вызова. Накладные расходы на компиляцию байт-кода для процедурной логики амортизируются до минимального значения.
    -   Секвенcирование — многим операции нужен побочный эффект («триггер»), что потенциально задействует выполнение одной или множества вторичных операций хранения. Помимо обеспечения атомарности, приводит к повышению производительности при переносе на сервер.
-   **Инкапсуляция:**Хранимые процедуры могут быть использованы для группирования бизнес-логики в одном месте. Это принесет два преимущества:

    -   Добавление уровня абстракции поверх исходных данных, что позволяет архитекторам данных развивать свои приложения независимо от данных. Это особенно выгодно, когда данные не имеют схемы, в связи с предположениями, что, возможно, потребуется реализованная в приложении схема, если им приходится иметь дело с данными напрямую.
    -   Эта абстракция позволяет предприятиям сохранить свои данные в безопасности, упрощая доступ к ним из сценариев.

Создание и выполнение триггеров, хранимых процедур и операторов пользовательских запросов поддерживается через API REST и клиентские пакеты SDK в различных платформах, включая NET, Node.js и JavaScript. **Этот учебник использует пакет Node.js SDK** для иллюстрации синтаксиса и использования хранимых процедур, триггеров и пользовательских функций.

Давайте начнем с простой хранимой процедуры, которая возвращает «Hello World» в ответ.

    var helloWorldStoredProc = {
        id: "helloWorld",
        body: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }

Хранимые процедуры регистрируются в коллекциях и могут работать с любым документом и вложением, существующим в этой коллекции. Следующий фрагмент кода показывает, как зарегистрировать хранимую процедуру HelloWorld в коллекции.

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync(collection._self, helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });

После того как хранимая процедура зарегистрирована, мы можем выполнить ее на коллекции и получить результаты обратно на клиенте.

    // execute the stored procedure
    client.executeStoredProcedureAsync(createdStoredProcedure._self)
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });

Контекст объекта предоставляет доступ ко всем операциям, которые можно выполнить в хранилище DocumentDB, а также доступ к объектам запросов и ответов. В этом случае мы использовали объект ответа, чтобы установить тело ответа, который был отправлен обратно клиенту. Для более подробной информации обратитесь к документации серверного пакета JavaScript для DocumentDB.

Давайте подробнее остановимся на этом примере и добавим в хранимую процедуру дополнительную функциональность баз данных. Хранимые процедуры могут создавать, обновлять, читать, запрашивать и удалять документы и приложения внутри коллекции.

В следующем фрагменте показано, как использовать контекст объекта для взаимодействия с сущностями DocumentDB.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        body: function createMyDocument(documentToCreate) {
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

Эта хранимая процедура принимает в качестве входного параметра documentToCreate, тело документа, который будет создан в текущей коллекции. Все эти операции являются асинхронными и зависят от функций обратных вызовов JavaScript. Функция обратного вызова имеет два параметра: один для объекта ошибки в случае, если операция не выполнится, и один для созданного объекта. Внутри функции обратного вызова пользователи могут либо обработать исключение, либо вызвать ошибку. Если обратного вызова не предусмотрено и возникла ошибка, то среда выполнения DocumentDB выдаст ошибку.

В приведенном выше примере обратный вызов возвращает ошибку, если операция не удалась. В противном случае он устанавливает идентификатор созданного документа в качестве тела ответа клиенту. Таким образом, данная хранимая процедура выполняется с входными параметрами.

    // register the stored procedure
    client.createStoredProcedureAsync(collection._self, createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;

            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };

            return client.executeStoredProcedureAsync(createdStoredProcedure._self,
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });

Обратите внимание, что эта хранимая процедура может быть изменена, чтобы принять массив тел документов в качестве входных данных и создания их все в том же исполнении хранимой процедуры вместо нескольких сетевых запросов для создания каждого из них в отдельности. Это может быть использовано, чтобы внедрить эффективный импорт большого количества данных в DocumentDB (обсуждается далее в этой статье).

Описанный пример продемонстрировал, как использовать хранимые процедуры. Мы рассмотрим триггеры и пользовательские функции (UDF) позже в этом уроке. Во-первых, давайте посмотрим общие характеристики поддержки сценариев в DocumentDB.

## Поддержка времени выполнения

Пакет серверного JavaScript для DocumentDB обеспечивает поддержку большинства возможностей языка JavaScript, стандартизированных в [ECMA-262][ECMA-262].

## Транзакции

Транзакции в типичной базе данных могут быть определены как последовательность операций, выполняемых в одной логической единице работы. Каждая транзакция предоставляет **гарантию выполнения принципа ACID**. Широко известный акроним ACID обозначает четыре свойства: Atomicity (атомарность), Consistency (согласованность), Isolation (изолированность) и Durability (надежность).

Вкратце, атомарность гарантирует, что все действия, проделанные в транзакции, рассматриваются как единое целое, они могут быть либо выполнены все, либо не выполнены вообще. Согласованность гарантирует, что данные всегда находятся в правильном внутреннем состоянии между транзакциями. Изолированность гарантирует, что никакие две транзакции не будут мешать друг другу. Как правило, большинство коммерческих систем обеспечивают несколько уровней изолированности, которые могут быть использованы в зависимости от потребностей приложений. Надежность гарантирует, что любое изменение, которое было совершено в базе данных, всегда будет присутствовать.

В DocumentDB JavaScript размещается в том же пространстве памяти, что и базы данных. Следовательно, запросы, хранимые процедуры и триггеры выполняются в том же пространстве базы данных. Это позволяет DocumentDB гарантировать принцип ACID для всех операций, которые являются частью одной хранимой процедуры/триггера. Рассмотрим следующее определение хранимой процедуры:

    // JavaScript source code
    var exchangeItemsSproc = {
        name: "exchangeItems",
        body: function (playerId1, playerId2) {
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

## Фиксация и откат транзакций

Транзакции глубоко и изначально интегрированы в модель программирования JavaScript в DocumentDB. Внутри функции JavaScript все операции автоматически обернуты в одной транзакции. Если JavaScript завершает выполнение функции без исключения, операции в базе данных фиксируются. По сути, это аналог операторов BEGIN TRANSACTION и COMMIT TRANSACTION в реляционных базах данных, подразумевающихся в DocumentDB.

Если есть исключения, которые возникли в сценарии, среда выполнения JavaScript в DocumentDB откатит всю транзакцию. Как показано в предыдущем примере, выбрасывание исключения аналогично оператору ROLLBACK TRANSACTION в DocumentDB.

### Согласованность данных

Хранимые процедуры и триггеры всегда выполняются на основной реплике коллекции DocumentDB. Гарантируется, что операции чтения в хранимых процедурах обеспечивают сильную согласованность. Запросы, использующие пользовательские функции, могут быть выполнены на первичной или любой вторичной реплике, но чтобы обеспечить требуемый уровень согласованности, необходимо выбрать соответствующую реплику.

## Безопасность

Хранимые процедуры и триггеры JavaScript выполняются в изолированной среде, поэтому действия одного сценария не оказывают какого-либо воздействия на другие сценарии, за счет изоляции снимков транзакций на уровне базы данных. Среды выполнения объединяются в пул, но удаляются из контекста после каждого запуска. Следовательно, они гарантированно безопасны от появления непреднамеренных побочных эффектов друг для друга.

## Предварительная компиляция

Хранимые процедуры, триггеры и пользовательские функции неявно прекомпилированы в формате байт-кода, чтобы избежать затрат на компиляцию во время каждого вызова сценария. Это гарантирует высокую скорость и низкие затраты на вызовы хранимых процедур.

## Триггеры и пользовательские функции

### Триггеры со срабатыванием до наступления события

DocumentDB предоставляет триггеры, которые выполняются или запускаются при выполнении операций над документом. Например, можно задать предварительный триггер, когда вы создаете документ, и он будет запускаться перед созданием документа. Ниже приведен пример того, как предварительные триггеры можно использовать для проверки свойств документа, который создается:

    var validateDocumentContentsTrigger = {
        name: "validateDocumentContents",
        body: function validate() {
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

При регистрации триггеров пользователи могут указать операции, с которыми они запускаются. Этот триггер был создан с TriggerOperation.Create, это означает, что последующее не допускается.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### Триггеры со срабатыванием после наступления события

Пост-триггеры, как и пред-триггеры, связаны с операцией на документе и не принимают никаких входных параметров. Они запускаются **после** завершения операции и получают доступ к ответному сообщению, которое отправляется клиенту.

В следующем примере используются триггеры со срабатыванием после наступления события:

    var updateMetadataTrigger = {
        name: "updateMetadata",
        body: function updateMetadata() {
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
    client.createTriggerAsync(collection.self, updateMetadataTrigger)
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

Важно отметить одну вещь, это **транзакционное** выполнение триггеров в DocumentDB. Этот триггер со срабатыванием после наступления события работает как часть той же самой транзакции при создании оригинального документа. Поэтому, если мы формируем исключение из триггера (скажем, если мы не в состоянии обновить метаданные документа), вся транзакция будет прервана и произойдет ее откат. Ни один документ не будет создан, будет возвращено исключение.

## Функции, определяемые пользователем (UDF)

Функции, определяемые пользователем (UDF), используются для расширения грамматики языка запросов SQL для DocumentDB и реализации пользовательской бизнес-логики. Функции можно вызывать только из запросов. Они не имеют доступа к объектам контекста и предназначены для использования только в качестве вычислимых JavaScript-сценариев. Таким образом, UDF могут запускаться только на дополнительных репликах службы DocumentDB.

Следующий пример создает UDF для расчета налога на прибыль на основе ставок для различных доходов, а затем использует его в запросе, чтобы найти всех людей, которые заплатили более 20 000 долларов США в виде налогов.

    var taxUdf = {
        name: "tax",
        body: function tax(income) {

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
    client.createUserDefinedFunctionAsync(collection.self, taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);

            var query = 'SELECT * FROM TaxPayers t WHERE tax(t.income) > 20000'; 
            return client.queryDocuments(collection.self,
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

## Ограниченное выполнение

Все операции DocumentDB должны завершаться за определенный на уровне сервера период времени. Это ограничение также относится к функциям JavaScript (хранимые процедуры, триггеры и определяемые пользователем функции). Если операция не завершается в этот период времени, транзакция откатывается. Функции JavaScript должны завершаться в период времени или реализовывать модель продолжения работы для пакета/возобновления выполнения.

Для упрощения разработки хранимых процедур и триггеров и ограничения времени их выполнения все функции в соответствии с объектом коллекции (для создания, чтения, замены и удаления документов и вложений) возвращают логическое значение, которое представляет, будет ли эта операция завершена. Если это значение является ложным, то это означает, что срок выполнения истекает в ближайшее время и что процедура должна завершить выполнение. Операции, предшествующие первой неакцептованной операции сохранения данных, помещаются в очередь и гарантированно будут завершены, если хранимая процедура завершается в срок и очередь пуста.

Функции JavaScript также ограничены в потреблении ресурсов. DocumentDB резервирует пропускную способность для коллекции, основанной на подготовленном размере учетной записи базы данных. Пропускная способность выражается в форме нормализованных единиц потребления ресурсов процессора, памяти и ввода-вывода, именуемых «единица запроса» или RU. Функции JavaScript могут потенциально использовать большое количество RU в течение короткого времени и, возможно, получить ограничение скорости выполнения при достижении предела, выделенного для коллекции. Ресурсоемкие хранимые процедуры также могут быть помещены в режим ожидания, чтобы обеспечить доступность примитивных операций с базами данных.

## Массовый импорт данных

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

## Интерфейс REST API

Все операции DocumentDB могут быть выполнены в RESTful-образом. Хранимые процедуры, триггеры и пользовательские функции могут быть зарегистрированы в соответствии с коллекцией с помощью команды POST HTTP. Ниже приведен пример, как зарегистрировать хранимую процедуру:

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

Хранимая процедура регистрируется выполнением команды POST с URI dbs/sehcAA==/colls/sehcAIE2Qy4=/sprocs с телом, содержащим создаваемую хранимую процедуру. Триггеры и функции могут быть зарегистрированы аналогично, путем выполнения команды POST с идентификаторами /triggers и /udfs соответственно.
 Эту хранимую процедуру затем можно выполнить, отправив запрос POST со ссылкой на ее источник:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]

Здесь входные параметры в хранимую процедуру передаются в теле запроса. Обратите внимание, что входные параметры передаются в виде массива JSON входных параметров. Хранимая процедура принимает первый параметр как документ, который является телом ответа. В ответ мы получаем следующее:

    HTTP/1.1 200 OK
     
    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }

Триггеры, в отличие от хранимых процедур, не могут быть выполнены непосредственно. Вместо этого они выполняются как часть операции над документом. Мы можем указать триггеры для запуска, отправив запрос с заголовками HTTP. Ниже приведен запрос для создания документа.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger


    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }

Здесь в заголовке x-ms-documentdb-pre-trigger-include указан триггер со срабатыванием до наступления события, который будет запущен при запросе. Соответственно, любые триггер со срабатыванием после наступления события могут быть приведены заголовке x-ms-documentdb-post-trigger-include. Обратите внимание, что для данного запроса могут быть определены оба вида триггеров.

## Поддержка пакетов SDK

В дополнение к клиенту Node.js, DocumentDB поддерживает пакеты SDK для .NET, JavaScript и Python. Хранимые процедуры, триггеры и пользовательские функции могут быть созданы и выполнены с использованием любого из этих пакетов SDK. В следующем примере показано, как создать и выполнить хранимую процедуру с помощью клиента .NET. Обратите внимание, что типы .NET передаются в хранимую процедуру и возвращаются в виде объектов JSON.

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
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(collection.SelfLink, markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;

    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(createdStoredProcedure.SelfLink, document, 1920);

Этот пример показывает, как использовать пакет SDK .NET для создания триггера со срабатыванием до наступления события и создания документа с включенным триггером.

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

    Document createdItem = await client.CreateDocumentAsync(collection.SelfLink, new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });

И в следующем примере показано, как создать определенную пользователем функцию (UDF) и использовать ее в запросе SQL DocumentDB.

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };

    foreach (Book book in client.CreateDocumentQuery(collection.SelfLink,
        "SELECT * FROM Books b WHERE LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

Ссылки на клиентские пакеты SDK

Ссылки

1.  JSON [][]<http://www.json.org/></a>

2.  JavaScript ECMA-262 [][1]<http://www.ecma-international.org/publications/standards/Ecma-262.htm></a>

3.  JavaScript. Система типов JSON [][2]<http://www.json.org/js.html></a>

4.  Безопасное и переносимое расширение баз данных [][3]<http://dl.acm.org/citation.cfm?id=276339></a>

5.  Архитектура баз данных, ориентированных на службы [][4]<http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE></a>

6.  Размещение среды выполнения .NET на сервере Microsoft SQL [][5]<http://dl.acm.org/citation.cfm?id=1007669></a>

  [ECMA-262]: documentdb-interactions-with-resources
  []: http://www.json.org/
  [1]: http://www.ecma-international.org/publications/standards/Ecma-262.htm
  [2]: http://www.json.org/js.html
  [3]: http://dl.acm.org/citation.cfm?id=276339
  [4]: http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE
  [5]: http://dl.acm.org/citation.cfm?id=1007669
