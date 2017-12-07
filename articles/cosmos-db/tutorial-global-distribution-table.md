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
ms.date: 11/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: a1682ef88760da39d33fac2cc9f34e0e66ea19fb
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Как настроить глобальное распределение Azure Cosmos DB с помощью API таблицы

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * настройка глобального распределения на портале Azure;
> * настройка глобального распределения с помощью [API таблицы](table-introduction.md).

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Подключение к предпочтительному региону с помощью API таблицы

Чтобы воспользоваться преимуществами [глобального распределения](distribute-data-globally.md), клиентские приложения могут указать упорядоченный список предпочитаемых регионов, который будет использоваться для операций с документами. Это можно сделать, задав значение `TablePreferredLocations` в конфигурации приложения для пакета SDK для API таблиц в Azure Cosmos DB. Пакет SDK для API таблиц в Azure Cosmos DB выберет лучшую конечную точку для взаимодействия на основе конфигурации учетной записи, текущей доступности региона и указанного списка предпочтений.

Свойство `TablePreferredLocations` должно содержать разделенный запятыми список предпочтительных расположений (множественная адресация) для операций чтения. Каждый экземпляр клиента может указать подмножество этих регионов в порядке предпочтения, чтобы обеспечить низкую задержку операций чтения. Регионам необходимо присвоить их [отображаемые имена](https://msdn.microsoft.com/library/azure/gg441293.aspx), например `West US`.

Все операции чтения отправляются в первый доступный регион из списка `TablePreferredLocations`. Если запрос завершится неудачей, клиент перейдет к следующему региону дальше по списку и так далее.

Пакет SDK пытается читать данные из регионов, указанных в `TablePreferredLocations`. Например, если учетная запись базы данных доступна в трех регионах, но клиент указывает в `TablePreferredLocations` только два региона не для записи, то операции чтения из региона записи не будут обслуживаться даже в случае отработки отказа.

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
> * настроили глобальное распределение с помощью API таблиц в Azure Cosmos DB.

