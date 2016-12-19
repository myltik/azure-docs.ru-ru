---
title: "Примеры NoSQL на Python для DocumentDB | Документация Майкрософт"
description: "Примеры NoSQL на Python для выполнения общих задач в DocumentDB, включая операции CRUD для документов JSON в базах данных NoSQL, можно найти на сайте GitHub."
keywords: "Примеры Python"
services: documentdb
author: moderakh
manager: jhubbard
editor: monicar
documentationcenter: python
ms.assetid: 7f4f8db3-e9db-4645-92ef-7819d486a349
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2016
ms.author: moderakh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7b555732bad08c86c33ee78344ae9c4e6af7c7b8


---
# <a name="documentdb-python-examples"></a>Примеры Python для DocumentDB
> [!div class="op_single_selector"]
> * [Примеры .NET](documentdb-dotnet-samples.md)
> * [Примеры Node.js](documentdb-nodejs-samples.md)
> * [Примеры Python](documentdb-python-samples.md)
> * [Коллекция примеров кода Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)
> 
> 

Примеры решений, которые выполняют операции CRUD и другие распространенные операции с ресурсами Azure DocumentDB, содержатся в разделе [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) репозитория GitHub. Эта статья содержит:

* Ссылки на задачи в каждом из примеров файлов проектов Python. 
* Ссылки на соответствующие справочные материалы по API.

**Предварительные требования**

1. Для использования этих примеров Python требуется учетная запись Azure.
   * Вы можете [открыть учетную запись Azure бесплатно](https://azure.microsoft.com/pricing/free-trial/)— вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после использования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-сайты. С вашей кредитной карты не будет взиматься плата, если вы явно не измените параметры и не попросите снимать плату.
     * Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)— каждый месяц ваша подписка Visual Studio предоставляет вам кредиты, которые можно использовать для оплаты служб Azure.
2. Вам также необходим [пакет SDK для Python](documentdb-sdk-python.md). 
   
   > [!NOTE]
   > Каждый пример является самодостаточным, он устанавливается самостоятельно и выполняет необходимую очистку после удаления. Поэтому примеры выполняют множественные вызовы метода [document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Каждый раз, когда это происходит, вам будет выставляться счет за 1 час использования каждого уровня производительности создаваемой коллекции. 
   > 
   > 

## <a name="database-examples"></a>Примеры баз данных
С помощью файла [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) вы узнаете, как выполнять следующие задачи.

| Задача | Справочник по API |
| --- | --- |
| [Создание базы данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Запрос учетной записи для базы данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Чтение базы данных по идентификатору](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Получение списка баз данных для учетной записи](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Удаление базы данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |

## <a name="collection-examples"></a>Примеры коллекций
С помощью файла [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) в проекте [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) вы узнаете, как выполнять следующие задачи.

| Задача | Справочник по API |
| --- | --- |
| [Создание коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Получение списка всех коллекций в базе данных](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) |[document_client.ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Получение коллекции по идентификатору](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Получение уровня производительности коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) |[DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Изменение уровня производительности коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) |[document_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Удаление коллекции](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |




<!--HONumber=Nov16_HO3-->


