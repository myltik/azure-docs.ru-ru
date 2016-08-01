<properties 
	pageTitle="Примеры NoSQL на Python для DocumentDB | Microsoft Azure" 
	description="Примеры NoSQL на Python для выполнения общих задач в DocumentDB, включая операции CRUD для документов JSON в базах данных NoSQL, можно найти на сайте GitHub." 
	keywords="Примеры Python"
	services="documentdb" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="python"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="andrl"/>


# Примеры Python для DocumentDB

> [AZURE.SELECTOR]
- [Примеры .NET](documentdb-dotnet-samples.md)
- [Примеры Node.js](documentdb-nodejs-samples.md)
- [Примеры Python](documentdb-python-samples.md)
- [Коллекция примеров кода Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Примеры решений, которые выполняют операции CRUD и другие распространенные операции с ресурсами Azure DocumentDB, содержатся в разделе [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) репозитория GitHub. Эта статья содержит:

- Ссылки на задачи в каждом из примеров файлов проектов Python.
- Ссылки на соответствующие справочные материалы по API.

**Предварительные требования**

1. Для использования этих примеров Python требуется учетная запись Azure.
    - Вы можете [открыть учетную запись Azure бесплатно](https://azure.microsoft.com/pricing/free-trial/) — вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после использования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-сайты. С вашей кредитной карты не будет взиматься плата, если вы явно не измените параметры и не попросите снимать плату.
   - Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) — каждый месяц ваша подписка Visual Studio предоставляет вам кредиты, которые можно использовать для оплаты служб Azure.
2. Вам также необходим [пакет SDK для Python](documentdb-sdk-python.md).

    > [AZURE.NOTE] Каждый пример является самодостаточным, он устанавливается самостоятельно и выполняет необходимую очистку после удаления. Поэтому примеры выполняют множественные вызовы метода [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Каждый раз, когда это происходит, вам будет выставляться счет за 1 час использования каждого уровня производительности создаваемой коллекции.

## Примеры баз данных

С помощью файла [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) вы узнаете, как выполнять следующие задачи.

Задача | Справочник по API
--- | ---
[Создание базы данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document\_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Запрос учетной записи для базы данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document\_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Чтение базы данных по идентификатору](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document\_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Получение списка баз данных для учетной записи](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document\_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Удаление базы данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document\_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## Примеры коллекций 

С помощью файла [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) в проекте [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) вы узнаете, как выполнять следующие задачи.

Задача | Справочник по API
--- | ---
[Создание коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Получение списка всех коллекций в базе данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document\_client.ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Получение коллекции по идентификатору](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document\_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Получение уровня производительности коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Изменение уровня производительности коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document\_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Удаление коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document\_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)

<!---HONumber=AcomDC_0720_2016-->