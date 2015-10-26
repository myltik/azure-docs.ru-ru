<properties 
	pageTitle="Программирование DocumentDB. Хранимые процедуры, триггеры базы данных и определяемые пользователем функции | Microsoft Azure" 
	description="Узнайте, как с помощью DocumentDB писать хранимые процедуры, триггеры базы данных и определяемые пользователем функции (UDF) на JavaScript. Здесь вы найдете советы по программированию баз данных и многое другое." 
	keywords="Database triggers, stored procedure, stored procedure, database program, sproc, documentdb, azure, Microsoft azure"
	services="documentdb" 
	documentationCenter="" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="andrl"/>

# Программирование DocumentDB на стороне сервера: хранимые процедуры, триггеры баз данных и определяемые пользователем функции

Узнайте, каким образом интегрированное транзакционное выполнение JavaScript в DocumentDB позволяет разработчикам писать **хранимые процедуры**, **триггеры** и **определяемые пользователем функции (UDF)** непосредственно на JavaScript. Это позволяет разработчикам писать логику приложения для баз данных, которая может быть отправлена и выполнена непосредственно в разделе хранения базы данных.

Прежде чем приступить к работе, рекомендуется просмотреть следующий видеоролик, где Эндрю Лю (Andrew Liu) кратко представляет модель программирования баз данных на стороне сервера DocumentDB.

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Затем вернитесь в эту статью, из которой вы узнаете ответы на следующие вопросы:

- Как написать хранимую процедуру, триггер или определяемую пользователем Функцию на JavaScript?
- Каким образом DocumentDB предоставляет гарантию выполнения принципа ACID?
- Каков принцип работы транзакций в DocumentDB?
- Что такое пред- и пост-триггеры, и как их написать?
- Как зарегистрировать и выполнить хранимую процедуру, триггер или определяемую пользователем функции RESTful-образом по протоколу HTTP?
- Какие пакеты SDK DocumentDB доступны для создания и выполнения хранимых процедур, триггеров и определяемых пользователем функций?

## Введение в программирование хранимых процедур и определяемых пользователем функций

Подход *"JavaScript — это современный T-SQL"* освобождает разработчиков приложений от сложностей системы, связанных с несоответствием типов и объектно-реляционных технологий сопоставления. Он также имеет ряд своих собственных преимуществ, которые могут быть использованы для создания мощных приложений:

-	**Процедурная логика.** JavaScript как язык программирования высокого уровня, обеспечивает многофункциональный и знакомый интерфейс для реализации бизнес-логики. Вы можете выполнять сложные последовательности операций ближе к данным.

-	**Атомарные транзакции.** DocumentDB гарантирует атомарность операций с базой данных, выполняемых внутри одной хранимой процедуры или триггера. Это позволяет приложению объединить соответствующие операции в пакетном режиме; таким образом, успешно будут выполнены либо все из них, либо ни одна.

-	**Производительность** Тот факт, что JSON неразрывно связан с системой типов языка Javascript, а также является основной единицей хранения в DocumentDB, позволяет использовать ряд оптимизаций, таких как отложенная материализация документов JSON в буферном пуле и доступ к ним для исполняемого кода по требованию. Есть еще больший выигрыш в производительности, связанный с доставкой бизнес-логики в базу данных:
	-	Пакеты — разработчики могут группировать операции, такие как вставка, и запускать их в пакетном режиме. Значительно сокращаются затраты на задержку сетевого трафика и накладные расходы для хранения при создании отдельных операций. 
	-	Предварительная компиляция — DocumentDB предварительно компилирует хранимые процедуры, триггеры и пользовательские функции (UDF), чтобы избежать задержки JavaScript при компиляции для каждого вызова. Накладные расходы на компиляцию байт-кода для процедурной логики амортизируются до минимального значения.
	-	Секвенcирование — многим операции нужен побочный эффект («триггер»), что потенциально задействует выполнение одной или множества вторичных операций хранения. Помимо обеспечения атомарности, приводит к повышению производительности при переносе на сервер. 
-	**Инкапсуляция.** Хранимые процедуры могут быть использованы для группировки бизнес-логики в одном месте. Это принесет два преимущества:
	-	Добавление уровня абстракции поверх исходных данных, что позволяет архитекторам данных развивать свои приложения независимо от данных. Это особенно выгодно, когда данные не имеют схемы, в связи с предположениями, что, возможно, потребуется реализованная в приложении схема, если придется работать с данными напрямую.  
	-	Эта абстракция позволяет предприятиям сохранить свои данные в безопасности, упрощая доступ к ним из сценариев.  

Создание и выполнение триггеров баз данных, хранимых процедур и операторов пользовательских запросов поддерживается через [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) и [клиентские пакеты SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) на различных платформах, включая .NET, Node.js и JavaScript. Для иллюстрации синтаксиса и использования хранимых процедур, триггеров и пользовательских функций **в этом учебнике используется [пакет SDK Node.js](http://dl.windowsazure.com/documentDB/nodedocs/)**.

## Хранимые процедуры

### Пример: написание простой хранимой процедуры 
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


Контекст объекта предоставляет доступ ко всем операциям, которые можно выполнить в хранилище DocumentDB, а также доступ к объектам запросов и ответов. В этом случае мы использовали объект ответа, чтобы установить тело ответа, который был отправлен обратно клиенту. Более подробные сведения можно найти в [документации к серверному пакету SDK JavaScript для DocumentDB](http://dl.windowsazure.com/documentDB/jsserverdocs/).

Давайте подробнее остановимся на этом примере и добавим в хранимую процедуру дополнительную функциональность баз данных. Хранимые процедуры могут создавать, обновлять, читать, запрашивать и удалять документы и приложения внутри коллекции.

### Пример: написание хранимой процедуры для создания документа 
В следующем фрагменте показано, как использовать объект контекста для взаимодействия с ресурсами DocumentDB.

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

Описанный пример продемонстрировал, как использовать хранимые процедуры. Мы рассмотрим триггеры и пользовательские функции (UDF) позже в этом уроке.

## Транзакции программы базы данных
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

### Фиксация и откат транзакций
Транзакции глубоко и изначально интегрированы в модель программирования JavaScript в DocumentDB. Внутри функции JavaScript все операции автоматически обернуты в одной транзакции. Если JavaScript завершает выполнение функции без исключения, операции в базе данных фиксируются. По сути, это аналог операторов BEGIN TRANSACTION и COMMIT TRANSACTION в реляционных базах данных, подразумевающихся в DocumentDB.
 
Если есть исключения, которые возникли в сценарии, среда выполнения JavaScript в DocumentDB откатит всю транзакцию. Как показано в предыдущем примере, выбрасывание исключения аналогично оператору ROLLBACK TRANSACTION в DocumentDB.
 
### Согласованность данных
Хранимые процедуры и триггеры всегда выполняются на основной реплике коллекции DocumentDB. Гарантируется, что операции чтения в хранимых процедурах обеспечивают сильную согласованность. Запросы, использующие пользовательские функции, могут быть выполнены на первичной или любой вторичной реплике, но чтобы обеспечить требуемый уровень согласованности, необходимо выбрать соответствующую реплику.

## Ограниченное выполнение
Все операции DocumentDB должны завершаться за определенный на уровне сервера период времени. Это ограничение также относится к функциям JavaScript (хранимые процедуры, триггеры и определяемые пользователем функции). Если операция не завершается в этот период времени, транзакция откатывается. Функции JavaScript должны завершаться в период времени или реализовывать модель продолжения работы для пакета/возобновления выполнения.

Для упрощения разработки хранимых процедур и триггеров и ограничения времени их выполнения все функции в соответствии с объектом коллекции (для создания, чтения, замены и удаления документов и вложений) возвращают логическое значение, которое представляет, будет ли эта операция завершена. Если это значение является ложным, то это означает, что срок выполнения истекает в ближайшее время и что процедура должна завершить выполнение. Операции, предшествующие первой неакцептованной операции сохранения данных, помещаются в очередь и гарантированно будут завершены, если хранимая процедура завершается в срок и очередь пуста.

Функции JavaScript также ограничены в потреблении ресурсов. DocumentDB резервирует пропускную способность для коллекции, основанной на подготовленном размере учетной записи базы данных. Пропускная способность выражается в форме нормализованных единиц потребления ресурсов процессора, памяти и ввода-вывода, именуемых «единица запроса» или RU. Функции JavaScript могут потенциально использовать большое количество RU в течение короткого времени и, возможно, получить ограничение скорости выполнения при достижении предела, выделенного для коллекции. Ресурсоемкие хранимые процедуры также могут быть помещены в режим ожидания, чтобы обеспечить доступность примитивных операций с базами данных.

### Пример. Массовый импорт данных в программу базы данных
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
### Триггеры базы данных со срабатыванием до наступления события
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

### Пост-триггеры базы данных
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

Важно отметить одну вещь, а именно — **транзакционное** выполнение триггеров в DocumentDB. Этот триггер со срабатыванием после наступления события работает как часть той же самой транзакции при создании оригинального документа. Поэтому, если мы формируем исключение из триггера (скажем, если мы не в состоянии обновить метаданные документа), вся транзакция будет прервана и произойдет ее откат. Ни один документ не будет создан, будет возвращено исключение.

##<a id="udf"></a>Функции, определяемые пользователем
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
	
		    var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
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

## API запросов, интегрированный в язык JavaScript
Кроме выдачи запросов с использованием грамматики SQL DocumentDB, серверный пакет SDK позволяет создавать оптимизированные запросы с помощью гибкого интерфейса JavaScript без знания SQL. API запросов в JavaScript позволяет программно создавать запросы, передавая предикатные функции в образующие цепочку вызовы функций. Для этого используется синтаксис, который понимают встроенные элементы массива ECMAScript5 и популярные библиотеки JavaScript, например lodash. Чтобы запросы были эффективно выполнены с помощью индексов DocumentDB, их анализирует среда выполнения JavaScript.

> [AZURE.NOTE]`__` (двойное подчеркивание) является псевдонимом для `getContext().getCollection()`.
> <br/>
> Другими словами, вы можете использовать `__` или `getContext().getCollection()`, чтобы получить доступ к API запросов в JavaScript.

Поддерживаемые функции включают:
<ul>
<li>
<b>chain() ... .value([обратный вызов] [, параметры])</b>
<ul>
<li>
Запускает цепной вызов, который должен быть остановлен атрибутом value().
</li>
</ul>
</li>
<li>
<b>filter(predicateFunction [, параметры] [, обратный вызов])</b>
<ul>
<li>
Фильтрует входные данные с помощью предикатной функции, которая возвращает значения «Истина» или «Ложь» для входящей или исходящей фильтрации входных документов в итоговый набор. Эта функция работает подобно предложению WHERE в SQL.
</li>
</ul>
</li>
<li>
<b>map(transformationFunction [, параметры] [, обратный вызов])</b>
<ul>
<li>
Применяет проекцию с учетом функции преобразования, которая сопоставляет каждый входной элемент с объектом или значением JavaScript. Эта функция работает подобно предложению SELECT в SQL.
</li>
</ul>
</li>
<li>
<b>pluck([имя свойства] [, параметры] [, обратный вызов])</b>
<ul>
<li>
Это ярлык для карты, которая извлекает значение одного свойства из каждого входного элемента.
</li>
</ul>
</li>
<li>
<b>flatten([isShallow] [, параметры] [, обратный вызов])</b>
<ul>
<li>
Объединяет и сводит массивы, полученные от каждого входного элемента, в единый массив. Эта функция работает подобно предложению SelectMany в LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy([предикат] [, параметры] [, обратный вызов])</b>
<ul>
<li>
Используя заданный предикат, создает новый набор документов путем сортировки документов во входном потоке документов по возрастанию. Эта функция работает подобно предложению ORDER BY в SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending([предикат] [, параметры] [, обратный вызов])</b>
<ul>
<li>
Используя заданный предикат, создает новый набор документов путем сортировки документов во входном потоке документов по убыванию. Эта функция работает подобно предложению ORDER BY x DESC в SQL.
</li>
</ul>
</li>
</ul>


Если конструкции JavaScript включены в предикатную функцию и/или селектор, они автоматически оптимизированы для непосредственного выполнения в индексах DocumentDB:

* Простые операторы: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~;
* литералы, включая литерал объекта: {}
* var, return.

Следующие конструкции JavaScript не оптимизированы для использования в индексах DocumentDB:

* поток управления (например, if, for, while);
* вызовы функций.

Дополнительные сведения см. в справочных материалах по [серверному генератору документации JSDoc](http://dl.windowsazure.com/documentDB/jsserverdocs/).

### Пример создания хранимой процедуры с помощью API запросов в JavaScript

В следующем образце кода приведен пример использования API запросов в JavaScript для создания хранимой процедуры. Хранимая процедура вставляет заданный входным параметром документ, после чего обновляет метаданные документа с помощью метода `__.filter()`, а также на основе свойств размера входного документа minSize, maxSize и totalSize.

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

## Таблица соответствия запросов SQL и Javascript
В следующей таблице представлены разные запросы SQL и соответствующие им запросы JavaScript.

Как и запросы SQL, ключи свойств документов (например, `doc.id`) чувствительны к регистру.

<br/>
<table border="1" width="100%">
<colgroup>
<col span="1" style="width: 40%;">
<col span="1" style="width: 40%;">
<col span="1" style="width: 20%;">
</colgroup>
<tbody>
<tr>
<th>SQL</th>
<th>JavaScript Query API</th>
<th>Details</th>
</tr>
<tr>
<td>
<pre>
SELECT *
FROM docs
</pre>
</td>
<td>
<pre>
__.map(function(doc) {
    return doc;
});
</pre>
</td>
<td>Выполняется во всех документах (разбитых на страницы с помощью маркеров продолжения) как есть.</td>
</tr>
<tr>
<td>
<pre>
SELECT docs.id, docs.message AS msg, docs.actions 
FROM docs
</pre>
</td>
<td>
<pre>
__.map(function(doc) {
    return {
        id: doc.id,
        msg: doc.message,
        actions: doc.actions
    };
});
</pre>
</td>
<td>Отображает код (id), сообщение (под псевдонимом msg) и действия во всех документах.</td>
</tr>
<tr>
<td>
<pre>
SELECT * 
FROM docs 
WHERE docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.filter(function(doc) {
    return doc.id === "X998_Y998";
});
</pre>
</td>
<td>Создает запрос на документы с предикатом: id = "X998_Y998".</td>
</tr>
<tr>
<td>
<pre>
SELECT *
FROM docs
WHERE ARRAY_CONTAINS(docs.Tags, 123)
</pre>
</td>
<td>
<pre>
__.filter(function(x) {
    return x.Tags && x.Tags.indexOf(123) > -1;
});
</pre>
</td>
<td>Создает запрос на документы со свойством Tags, которое является массивом, содержащим значение 123.</td>
</tr>
<tr>
<td>
<pre>
SELECT docs.id, docs.message AS msg
FROM docs 
WHERE docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.chain()
    .filter(function(doc) {
        return doc.id === "X998_Y998";
    })
    .map(function(doc) {
        return {
            id: doc.id,
            msg: doc.message
        };
    })
    .value();
</pre>
</td>
<td>Создает запрос на документы с предикатом: id = "X998_Y998", после чего отображает код (id) и сообщение (под псевдонимом msg).</td>
</tr>
<tr>
<td>
<pre>
SELECT VALUE tag
FROM docs
JOIN tag IN docs.Tags
ORDER BY docs._ts
</pre>
</td>
<td>
<pre>
__.chain()
    .filter(function(doc) {
        return doc.Tags && Array.isArray(doc.Tags);
    })
    .sortBy(function(doc) {
    	return doc._ts;
    })
    .pluck("Tags")
    .flatten()
    .value()
</pre>
</td>
<td>Отфильтровывает документы со свойством массива Tags, сокращает полученные документы с помощью системного свойства метки времени \_ts, а затем выводит и сводит массив Tags.</td>
</tr>
</tbody>
</table>

## Поддержка времени выполнения
[Серверный пакет SDK JavaScript для DocumentDB](http://dl.windowsazure.com/documentDB/jsserverdocs/) поддерживает большинство возможностей языка JavaScript в соответствии со стандартами [ECMA-262](documentdb-interactions-with-resources.md).

### Безопасность
Хранимые процедуры и триггеры JavaScript выполняются в изолированной среде, поэтому действия одного сценария не оказывают какого-либо воздействия на другие сценарии, за счет изоляции снимков транзакций на уровне базы данных. Среды выполнения объединяются в пул, но удаляются из контекста после каждого запуска. Следовательно, они гарантированно безопасны от появления непреднамеренных побочных эффектов друг для друга.

### Предварительная компиляция
Хранимые процедуры, триггеры и пользовательские функции неявно прекомпилированы в формате байт-кода, чтобы избежать затрат на компиляцию во время каждого вызова сценария. Это гарантирует высокую скорость и низкие затраты на вызовы хранимых процедур.

## Поддержка клиентских пакетов SDK
Наряду с клиентом [Node.js](http://dl.windowsazure.com/documentDB/nodedocs/) DocumentDB поддерживает пакеты SDK для [.NET](https://msdn.microsoft.com/library/azure/dn783362.aspx), [Java](http://dl.windowsazure.com/documentdb/javadoc/), [JavaScript](http://dl.windowsazure.com/documentDB/jsclientdocs/) и [Python](http://dl.windowsazure.com/documentDB/pythondocs/). Хранимые процедуры, триггеры и пользовательские функции могут быть созданы и выполнены с использованием любого из этих пакетов SDK. В следующем примере показано, как создать и выполнить хранимую процедуру с помощью клиента .NET. Обратите внимание, что типы .NET передаются в хранимую процедуру и возвращаются в виде объектов JSON.

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


В этом примере показано, как использовать пакет [.NET SDK](https://msdn.microsoft.com/library/azure/dn783362.aspx), чтобы создать триггер со срабатыванием до наступления события, а также документ со включенным триггером.

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


А в следующем примере показано, как создать определенную пользователем функцию для последующего использования в [запросе SQL DocumentDB](documentdb-sql-query.md).

	UserDefinedFunction function = new UserDefinedFunction()
	{
	    Id = "LOWER",
	    Body = @"function(input) 
		{
	        return input.toLowerCase();
	    }"
	};
	
	foreach (Book book in client.CreateDocumentQuery(collection.SelfLink,
	    "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
	{
	    Console.WriteLine("Read {0} from query", book);
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


Хранимая процедура регистрируется выполнением команды POST с URI dbs/sehcAA==/colls/sehcAIE2Qy4=/sprocs с телом, содержащим создаваемую хранимую процедуру. Триггеры и функции UDF могут быть зарегистрированы аналогичным образом путем выполнения команды POST с идентификаторами /triggers и /udfs соответственно.
Эту хранимую процедуру затем можно выполнить, отправив запрос POST со ссылкой на ее ресурс.

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

## Пример кода

Дополнительные примеры кода, используемого на сервере (включая [upsert](https://github.com/Azure/azure-documentdb-js/blob/master/server-side/samples/stored-procedures/upsert.js), [bulk-delete](https://github.com/Azure/azure-documentdb-js/blob/master/server-side/samples/stored-procedures/bulkDelete.js) и [update](https://github.com/Azure/azure-documentdb-js/blob/master/server-side/samples/stored-procedures/update.js)), см. в нашем [репозитории Github](https://github.com/Azure/azure-documentdb-js/tree/master/server-side/samples).

Хотите поделиться своей потрясающей хранимой процедурой? Отправьте нам запрос на получение.

## Дальнейшие действия

После создания хранимых процедур, триггеров и определяемых пользователем функций вы сможете загружать их на портале предварительной версии Azure и просматривать их с помощью обозревателя скриптов. Дополнительные сведения см. в статье [Просмотр хранимых процедур, триггеров и определяемых пользователем функций с помощью обозревателя скриптов DocumentDB](documentdb-view-scripts.md).

Кроме того, при изучении программирования DocumentDB на стороне сервера могут оказаться полезными следующие ссылки и ресурсы:

- [Пакеты SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [JSON](http://www.json.org/) 
- [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
-	[JavaScript — система типов JSON](http://www.json.org/js.html) 
-	[Безопасность и расширяемость переносной базы данных](http://dl.acm.org/citation.cfm?id=276339) 
-	[Сервис-ориентированная архитектура баз данных](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
-	[Размещение среды выполнения .NET на Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)  

<!---HONumber=Oct15_HO3-->