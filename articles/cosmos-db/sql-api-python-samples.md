---
title: Azure Cosmos DB. Примеры Python для API SQL | Документация Майкрософт
description: Примеры Python на сайте GitHub, позволяющие выполнять общие задачи в Azure Cosmos DB, в том числе операции CRUD.
keywords: Примеры Python
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: python
ms.assetid: 7f4f8db3-e9db-4645-92ef-7819d486a349
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 84b12ff3812803e3c1cb3b10cc939176303f1709
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-python-examples"></a>Примеры Python для Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Примеры .NET](sql-api-dotnet-samples.md)
> * [Примеры Java](sql-api-java-samples.md)
> * [Примеры Node.js](sql-api-nodejs-samples.md)
> * [Примеры Python](sql-api-python-samples.md)
> * [Коллекция примеров кода Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Примеры решений, которые выполняют операции CRUD и другие распространенные операции с ресурсами Azure Cosmos DB, содержатся в разделе [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) репозитория GitHub. Эта статья содержит:

* Ссылки на задачи в каждом из примеров файлов проектов Python. 
* Ссылки на соответствующие справочные материалы по API.

**Предварительные требования**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)— каждый месяц ваша подписка Visual Studio предоставляет вам кредиты, которые можно использовать для оплаты служб Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Вам также необходим [пакет SDK для Python](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Каждый пример является самодостаточным, он устанавливается самостоятельно и выполняет необходимую очистку после удаления. Примеры выполняют множественные вызовы метода [document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). При этом каждый раз на подписку выставляется счет за один час использования. Дополнительные сведения о выставлении счетов в Azure Cosmos DB см. на [странице цен на Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Примеры баз данных
С помощью файла [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) вы узнаете, как выполнять следующие задачи.

| Задача | Справочник по API |
| --- | --- |
| [Создание базы данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDatabase) |
| [Чтение базы данных по идентификатору](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDatabase) |
| [Получение списка баз данных для учетной записи](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Удаление базы данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |

## <a name="collection-examples"></a>Примеры коллекций
С помощью файла [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) в проекте [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) вы узнаете, как выполнять следующие задачи.

| Задача | Справочник по API |
| --- | --- |
| [Создание коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Получение списка всех коллекций в базе данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L209) |[document_client.ReadCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadCollections) |
| [Получение коллекции по идентификатору](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadCollection) |
| [Изменение пропускной способности коллекции](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L168-L172) | [document_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)|
| [Удаление коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#DeleteCollection) |

## <a name="document-examples"></a>Примеры документов
С помощью файла [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) в проекте [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) вы узнаете, как выполнять следующие задачи.

| Задача | Справочник по API |
| --- | --- |
| [Создание документа](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocument](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDocument) |
| [Создание коллекции документов](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[document_client.CreateDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateDocuments) |
| [Чтение документа по идентификатору](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[document_client.ReadDocument](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDocument) |
| [Чтение всех документов в коллекции](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[document_client.ReadDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReadDocuments) |

## <a name="indexing-examples"></a>Примеры индексирования
С помощью файла [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) в проекте [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) вы узнаете, как выполнять следующие задачи.

| Задача | Справочник по API |
| --- | --- |
| [Использование индексирования вручную (вместо автоматического)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) |[IndexingPolicy.Automatic](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Исключение указанных путей к документам из индекса](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) |[IndexingPolicy.ExcludedPaths](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Исключение документа из индекса](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[documents.IndexingDirective.Exclude](http://azure.github.io/azure-documentdb-python/_modules/pydocumentdb/documents.html#IndexingDirective) |
| [Установка режима индексирования](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[documents.IndexingMode](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.documents.html#IndexingMode) |
| [Использование диапазонных индексов в строках](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) |[IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Преобразование индекса](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[document_client.ReplaceCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#ReplaceCollection) |

## <a name="query-examples"></a>Примеры запросов
В примерах проектов также показано, как выполнять следующие задачи запроса:

| Задача | Справочник по API |
| --- | --- |
| [Запрос учетной записи для базы данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Запрос документов](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[document_client.QueryDocuments](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#QueryDocuments) |
| [Принудительное выполнение операции сканирования диапазона в хэш-индексированном пути](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[http_constants.HttpHeaders.EnableScanInQuery](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.http_constants.html#pydocumentdb.http_constants.HttpHeaders.EnableScanInQuery) |
