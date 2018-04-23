---
title: Руководство по настройке глобального распределения Azure Cosmos DB с помощью API MongoDB | Документация Майкрософт
description: Сведения о настройке глобального распределения Azure Cosmos DB с помощью API MongoDB.
services: cosmos-db
keywords: глобальное распределение, MongoDB
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 8bd86c5e66fdf2431e3db12a43e953b022a3770a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>Как настроить глобальное распределение Azure Cosmos DB с помощью API MongoDB

В этой статье показано, как настроить глобальное распределение базы данных Azure Cosmos DB и подключиться к ней с помощью API MongoDB на портале Azure.

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * настроили глобальное распределение на портале Azure;
> * настройка глобального распределения с помощью [API MongoDB](mongodb-introduction.md).

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>Проверка региональных настроек с помощью API MongoDB
Самый простой способом еще раз проверить глобальную конфигурацию в API для MongoDB — это выполнить команду *isMaster()* из оболочки Mongo.

Из оболочки Mongo:

   ```
      db.isMaster()
   ```
   
Пример результатов:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>Подключение к предпочтительному региону с помощью API MongoDB

API MongoDB позволяет указать параметры чтения коллекции для глобально распределенной базы данных. Для выполнения операций чтения с низкой задержкой и обеспечения глобальной высокой доступности рекомендуется задать в параметрах чтения коллекции значение *Nearest* (Ближайший). Параметр *Nearest* означает, что чтение будет выполняться из ближайшего региона.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Для приложений, использующих сценарии с основным регионом для операций чтения и записи и дополнительным регионом для аварийного восстановления, рекомендуется задать в параметрах чтения коллекции значение *SecondaryPreferred* (Предпочтительно дополнительный). Параметр *SecondaryPreferred* означает, что чтение будет выполняться из дополнительного региона при недоступности основного региона.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Наконец, вы можете вручную указать регион для чтения. Для этого задайте в параметрах чтения тег региона.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

На этом руководство завершено. Сведения об управлении согласованностью глобально реплицируемой учетной записи Azure Cosmos DB см. в [этой статье](consistency-levels.md). Сведения о том, как функционирует репликация глобальной базы данных в Azure Cosmos DB, см. в статье о [глобальном распределении данных в Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * настроили глобальное распределение на портале Azure;
> * настроили глобальное распределение с помощью API SQL.

Перейдите к следующему руководству, чтобы узнать о разработке в локальной среде с помощью локального эмулятора Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Разработка в локальной среде с помощью эмулятора](local-emulator.md)
