---
title: "Руководство по настройке глобального распределения Azure Cosmos DB с помощью API таблицы | Документация Майкрософт"
description: "Сведения о настройке глобального распределения Azure Cosmos DB с помощью API таблицы."
services: cosmos-db
keywords: "глобальное распределение, таблица"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: e9cb4b5b886cec46c0483287460c720855867f38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Как настроить глобальное распределение Azure Cosmos DB с помощью API таблицы

В этой статье показано, как настроить глобальное распределение базы данных Azure Cosmos DB и подключиться к ней с помощью API таблицы (предварительная версия) на портале Azure.

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * настройка глобального распределения на портале Azure;
> * настройка глобального распределения с помощью [API таблицы](table-introduction.md).

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Подключение к предпочтительному региону с помощью API таблицы

Чтобы воспользоваться преимуществами [глобального распределения](distribute-data-globally.md), клиентские приложения могут указать упорядоченный список предпочитаемых регионов, который будет использоваться для операций с документами. Это можно сделать, задав значение `TablePreferredLocations` в конфигурации приложения для пакета SDK для службы хранилища Azure (предварительная версия). Для операций записи и чтения с помощью пакета SDK для службы хранилища Azure выбирается наиболее оптимальная конечная точка на основании текущих данных о региональной доступности и списка предпочтений, указанного в конфигурации учетной записи Azure Cosmos DB.

Свойство `TablePreferredLocations` должно содержать разделенный запятыми список предпочтительных расположений (множественная адресация) для операций чтения. Каждый экземпляр клиента может указать подмножество этих регионов в порядке предпочтения, чтобы обеспечить низкую задержку операций чтения. Регионам необходимо присвоить их [отображаемые имена](https://msdn.microsoft.com/library/azure/gg441293.aspx), например `West US`.

Все операции чтения будут отправляться в первый доступный регион из списка `TablePreferredLocations`. Если запрос завершится неудачей, клиент перейдет к следующему региону дальше по списку и так далее.

Пакеты SDK будут пытаться выполнять чтение данных из регионов, указанных в `TablePreferredLocations`. Например, если учетная запись базы данных доступна в трех регионах, но клиент указывает в `TablePreferredLocations` только два региона не для записи, то операции чтения из региона записи не будут обслуживаться даже в случае отработки отказа.

Пакет SDK будет автоматически отправлять все операции записи в текущий регион записи.

Если свойство `TablePreferredLocations` не задано, будут обслуживаться все запросы из текущего региона записи.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

На этом руководство завершено. Сведения об управлении согласованностью глобально реплицируемой учетной записи Azure Cosmos DB см. в [этой статье](consistency-levels.md). Сведения о том, как функционирует репликация глобальной базы данных в Azure Cosmos DB, см. в статье о [глобальном распределении данных в Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * настроили глобальное распределение на портале Azure;
> * настроили глобальное распределение с помощью API-интерфейсов DocumentDB.

Перейдите к следующему руководству, чтобы узнать о разработке в локальной среде с помощью локального эмулятора Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Разработка в локальной среде с помощью эмулятора](local-emulator.md)