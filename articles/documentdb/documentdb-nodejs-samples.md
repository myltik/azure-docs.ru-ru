<properties 
	pageTitle="Примеры NoSQL Node.js для DocumentDB | Microsoft Azure" 
	description="Примеры NoSQL Node.js для выполнения общих задач в DocumentDB, включая операции CRUD для документов JSON в базах данных NoSQL, можно найти на сайте GitHub." 
	keywords="примеры node.js"
	services="documentdb" 
	authors="AndrewHoh" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="nodejs"/>

.<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="anhoh"/>


# Примеры DocumentDB для Node.js

> [AZURE.SELECTOR]
- [Примеры .NET](documentdb-dotnet-samples.md)
- [Примеры Node.js](documentdb-nodejs-samples.md)
- [Примеры Python](documentdb-python-samples.md)
- [Коллекция примеров кода Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Примеры решений, которые выполняют операции CRUD и другие распространенные операции с ресурсами Azure DocumentDB, содержатся в разделе [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) репозитория GitHub. Эта статья содержит:

- Ссылки на задачи в каждом из примеров файлов проектов Node.js.
- Ссылки на соответствующие справочные материалы по API.

**Предварительные требования**

1. Для использования этих примеров Node.js требуется учетная запись Azure.
    - Вы можете [открыть учетную запись Azure бесплатно](https://azure.microsoft.com/pricing/free-trial/) — вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после использования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-сайты. С вашей кредитной карты не будет взиматься плата, если вы явно не измените параметры и не попросите снимать плату.
   - Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) — каждый месяц ваша подписка Visual Studio предоставляет вам кредиты, которые можно использовать для оплаты служб Azure.
2. Вам также необходим [пакет SDK для Node.js](documentdb-sdk-node.md).

    > [AZURE.NOTE] Каждый приведенный здесь пример самодостаточный. Он устанавливается самостоятельно и выполняет необходимую очистку после удаления. Поэтому примеры выполняют множественные вызовы метода [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html). Каждый раз, когда это происходит, вам будет выставляться счет за 1 час использования каждого уровня производительности создаваемой коллекции.

## Примеры баз данных

С помощью файла [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) вы узнаете, как выполнять следующие задачи.

Задача | Справочник по API
--- | ---
[Создание базы данных](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[Запрос учетной записи для базы данных](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabase)
[Чтение базы данных по идентификатору](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Получение списка баз данных для учетной записи](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Удаление базы данных](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## Примеры коллекций 

С помощью файла [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) в проекте [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) вы узнаете, как выполнять следующие задачи.

Задача | Справочник по API
--- | ---
[Создание коллекции](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[Получение списка всех коллекций в базе данных](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.listCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#listCollections)
[Получение коллекции с помощью \_self](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Получение коллекции по идентификатору](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Получение уровня производительности коллекции](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers)
[Изменение уровня производительности коллекции](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer)
[Удаление коллекции](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## Примеры документов

С помощью файла [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) в проекте [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) вы узнаете, как выполнять следующие задачи.

Задача | Справочник по API
--- | ---
[Создание документов](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[Чтение потока документа для коллекции](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Чтение документа по идентификатору](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Чтение документа только в том случае, если он был изменен](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Запрос документов](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) 
[Замена документа](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) | [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[Замена документа с помощью условной проверки ETag](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) | [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Удаление документа](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## Примеры индексирования

С помощью файла [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) в проекте [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) вы узнаете, как выполнять следующие задачи.

Задача | Справочник по API
--- | ---
Создание коллекции с пространственным индексированием | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[Индексирование определенного документа вручную](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective: 'include'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[Исключение определенного документа из индекса вручную](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Использование отложенного индексирования для массового импорта или чтения больших коллекций](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#indexingmode)
[Включение указанных путей документов в индексирование](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy) 
[Исключение указанных путей из индексирования](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy)
[Разрешение проверки строкового пути во время операции диапазона](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[Создание индекса диапазона строкового пути](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[Создание коллекции с политикой индексирования (indexPolicy) по умолчанию, а затем ее обновление через Интернет](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

Дополнительные сведения об индексации см. в статье [Политики индексации DocumentDB](documentdb-indexing-policies.md).

## Примеры программирования на стороне сервера

С помощью файла [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) в проекте [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) вы узнаете, как выполнять следующие задачи.

Задача | Справочник по API
--- | ---
[Создание хранимой процедуры](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[Выполнение хранимой процедуры](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

Дополнительные сведения о программировании на стороне сервера см. в статье [Программирование DocumentDB на стороне сервера: хранимые процедуры, триггеры баз данных и определяемые пользователем функции](documentdb-programming.md).

## Примеры секционирования

С помощью файла [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) в проекте [Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) вы узнаете, как выполнять следующие задачи.

Задача | Справочник по API
--- | ---
[Использование HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) | [HashPartitionResolver](http://documentdb.com)

Дополнительные сведения о секционировании данных в DocumentDB см. в статье [Секционирование и масштабирование данных в DocumentDB](documentdb-partition-data.md).

<!---HONumber=AcomDC_0810_2016-->