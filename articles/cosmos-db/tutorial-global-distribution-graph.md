---
title: Руководство по настройке глобального распределения Azure Cosmos DB с помощью API Graph | Документация Майкрософт
description: Сведения о настройке глобального распределения Azure Cosmos DB с помощью API Graph.
services: cosmos-db
keywords: глобальное распределение, graph, gremlin
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 273b5aeafbf67016259da787f4dfef078ec0a669
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Как настроить глобальное распределение Azure Cosmos DB с помощью API Graph

В этой статье показано, как настроить глобальное распределение базы данных Azure Cosmos DB и подключиться к ней с помощью API Graph на портале Azure.

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * настроили глобальное распределение на портале Azure;
> * настроили глобальное распределение с помощью [API-интерфейсов Graph](graph-introduction.md).

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Подключение к предпочтительному региону с помощью API Graph и пакета SDK для .NET

API Graph предоставляется как библиотека расширений на базе пакета SDK для SQL.

Чтобы воспользоваться преимуществами [глобального распределения](distribute-data-globally.md), клиентские приложения могут указать упорядоченный список предпочитаемых регионов, который будет использоваться для операций с документами. Это можно сделать, настроив политику подключения. Для операций записи и чтения с помощью пакета SDK выбирается наиболее оптимальная конечная точка на основании текущих данных о региональной доступности и списка предпочтений, указанного в конфигурации учетной записи Azure Cosmos DB.

Этот список предпочтений указывается при инициализации подключения с помощью пакетов SDK. Пакеты SDK принимают необязательный параметр PreferredLocations, представляющий собой упорядоченный список регионов Azure.

* **Операции записи.** Пакет SDK автоматически отправляет все операции записи в текущий регион записи.
* **Операции чтения.** Все операции чтения отправляются в первый доступный регион из списка PreferredLocations. Если запрос завершается неудачей, клиент переходит к следующему региону дальше по списку и т. д. Пакеты SDK пытаются выполнять чтение данных из регионов, указанных в PreferredLocations. Например, если учетная запись Cosmos DB доступна в трех регионах, но клиент указывает в PreferredLocations только два региона не для записи, операции чтения из региона записи не обслуживаются даже в случае отработки отказа.

Приложение может проверить текущие конечную точку записи и конечную точку чтения, выбранные пакетом SDK, просмотрев два свойства, WriteEndpoint и ReadEndpoint, доступные в SDK 1.8 и более поздней версии. Если свойство PreferredLocations не задано, обслуживаются все запросы из текущего региона записи.

### <a name="using-the-sdk"></a>Использование пакета SDK

Например, в пакете SDK для .NET параметр `ConnectionPolicy` для конструктора `DocumentClient` имеет свойство `PreferredLocations`. В качестве значения этого свойства можно задать список имен регионов. Отображаемые имена [регионов Azure][regions] можно указать в качестве значений `PreferredLocations`.

> [!NOTE]
> Не следует считать URL-адреса конечных точек долговременными константами. Служба может обновить их в любой момент. Пакет SDK обрабатывает это изменение автоматически.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
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

[regions]: https://azure.microsoft.com/regions/

