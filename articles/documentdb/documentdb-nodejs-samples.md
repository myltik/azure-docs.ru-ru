<properties 
	pageTitle="Примеры Node.js для DocumentDB в github | Microsoft Azure" 
	description="Найдите в github примеры Node.js для выполнения общих задач в DocumentDB, включая операции CRUD для документов JSON в базах данных NoSQL." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="nodejs"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="mimig"/>


# Примеры DocumentDB для Node.js

> [AZURE.SELECTOR]
- [.NET Samples](documentdb-dotnet-samples.md)
- [Node.js Samples](documentdb-nodejs-samples.md)
- [Azure Code Sample Gallery](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Примеры решений, которые выполняют операции CRUD и другие распространенные операции с ресурсами DocumentDB, содержатся в разделе [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) GitHub. Эта статья содержит:

- Ссылки на задачи в каждом из примеров файлов проектов Node.js. 
- Ссылки на соответствующие справочные материалы по API.

**Предварительные требования**

1. Для использования этих примеров требуется учетная запись Azure.
    - Вы можете [открыть учетную запись Azure бесплатно](https://azure.microsoft.com/pricing/free-trial/) — вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после использования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-сайты. С вашей кредитной карты не будет взиматься плата, если вы явно не измените параметры и не попросите снимать плату.
   - Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) — каждый месяц ваша подписка Visual Studio предоставляет вам кредиты, которые можно использовать для оплаты служб Azure.
2. Вам также необходим [пакет SDK для Node.js](documentdb-sdk-node.md). 

    > [AZURE.NOTE]Каждый пример является самодостаточным, он устанавливается самостоятельно и выполняет необходимую очистку после удаления. Поэтому примеры выполняют множественные вызовы метода [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html). Каждый раз, когда это происходит, вам будет выставляться счет за 1 час использования каждого уровня производительности создаваемой коллекции.

## Примеры баз данных

Файл [App.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.CollectionManagement/app.js) проекта [DocumentDB.Samples.CollectionManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.CollectionManagement) показывает, как выполнить следующие задачи.

Задача | Справочник по API
--- | ---
[Создание базы данных](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[Запрос учетной записи для базы данных](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabase)
[Чтение базы данных по идентификатору](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Получение списка баз данных для учетной записи](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Удаление базы данных](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## Примеры коллекций 

Файл [App.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.CollectionManagement/app.js) проекта [DocumentDB.Samples.CollectionManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.CollectionManagement) показывает, как выполнить следующие задачи.

Задача | Справочник по API
--- | ---
[Создание коллекции](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[Получение списка всех коллекций в базе данных](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.listCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#listCollections)
[Получение коллекции с помощью \_self](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Получение коллекции по идентификатору](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Получение уровня производительности коллекции](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers)
[Изменение уровня производительности коллекции](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer)
[Удаление коллекции](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## Примеры документов

Файл [App.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.DocumentManagement/app.js) проекта [DocumentDB.Samples.DocumentManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.DocumentManagement) показывает, как выполнить следующие задачи.

Задача | Справочник по API
--- | ---
[Создание документов](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[Чтение потока документа для коллекции](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Чтение документа по идентификатору](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Запрос документов](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) 
[Обновление документа](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) | [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[Удаление документа](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## Примеры индексирования

Файл [App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.IndexManagement/app.js) проекта [DocumentDB.Samples.IndexManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.IndexManagement) показывает, как выполнить следующие задачи.

Задача | Справочник по API
--- | ---
Создание коллекции с пространственным индексированием | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[Индексирование определенного документа вручную](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective: 'include'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[Исключение определенного документа из индекса вручную](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Использование отложенного индексирования для массового импорта или чтения больших коллекций](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#indexingmode)
[Включение указанных путей документов в индексирование](https://github.com/mimig1/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy) 
[Исключение указанных путей из индексирования](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#indexingpolicy)
[Разрешение проверки строкового пути во время операции диапазона](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[Создание индекса диапазона строкового пути](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[Создание коллекции с политикой индексирования (indexPolicy) по умолчанию, а затем ее обновление через Интернет](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

Дополнительные сведения об индексации см. в статье [Политики индексации DocumentDB](documentdb-indexing-policies.md).

## Примеры программирования на стороне сервера

Файл [App.js](https://github.com/mimig1/azure-documentdb-node/blob/master/samples/DocumentDB.Samples.ServerSideScripts/app.js) проекта [DocumentDB.Samples.IndexManagement](https://github.com/mimig1/azure-documentdb-node/tree/master/samples/DocumentDB.Samples.ServerSideScripts) показывает, как выполнить следующие задачи.

Задача | Справочник по API
--- | ---
[Создание хранимой процедуры](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[Выполнение хранимой процедуры](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

Дополнительные сведения о программировании на стороне сервера см. в разделе [Программирование DocumentDB на стороне сервера: хранимые процедуры, триггеры баз данных и определяемые пользователем функции](documentdb-programming.md).

<!---HONumber=AcomDC_0114_2016-->