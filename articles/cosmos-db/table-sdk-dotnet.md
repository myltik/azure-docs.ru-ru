---
title: "Пакет SDK для .NET и ресурсы API таблицы для базы данных Azure Cosmos DB | Документация Майкрософт"
description: "Сведения об API таблиц Azure Cosmos DB, в том числе даты выпуска и даты вывода из эксплуатации, а также изменения, внесенные в каждую версию."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/12/2017
ms.author: mimig
ms.openlocfilehash: 02bb5d23ee9468ab1f74396877cdcd6bdd8b8fba
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API таблицы .NET для базы данных Azure Cosmos DB. Скачивание и заметки о выпуске
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Скачивание пакета SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Документация по API**|[Справочная документация по API .NET](https://aka.ms/acdbtableapiref)|
|**Краткое руководство**|[Azure Cosmos DB: Build a .NET application using the Table API](create-table-dotnet.md) (Создание приложения .NET с помощью API таблицы Azure Cosmos DB)|
|**Руководство**|[Разработка с помощью API таблицы базы данных Azure Cosmos DB на языке .NET](tutorial-develop-table-dotnet.md)|
|**Текущая поддерживаемая платформа**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> Если вы создали учетную запись API таблиц на этапе работы с предварительной версией, для работы с общедоступными пакетами SDK для API таблиц создайте [новую учетную запись](create-table-dotnet.md#create-a-database-account).
>

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Выпуск общедоступной версии

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0 (предварительная версия)
* Первоначальный выпуск предварительной версии

## <a name="release-and-retirement-dates"></a>Даты выпуска и вывода из эксплуатации
Корпорация Майкрософт отправляет уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

[WindowsAzure.Storage PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) предварительной версии пакета устарели и заменены [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) пакета. SDK WindowsAzure.Storage PremiumTable будет удалено на 15 ноября 2018, во время которого запросы на пенсию SDK не будут разрешены.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии. 

Любые запросы к базе данных Azure Cosmos DB с помощью выведенного из эксплуатации пакета SDK отклоняются службой.
<br/>

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.0.0](#1.0.0) |15 ноября 2017 г.|--- |
| [0.9.0 (предварительная версия)](#0.9.0-preview) |11 ноября 2017 г. |--- |

## <a name="troubleshooting"></a>Устранение неполадок

Если возникает ошибка 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

при попытке использовать NuGet-пакет Microsoft.Azure.CosmosDB.Table, ее можно устранить двумя способами.

* Установите пакет Microsoft.Azure.CosmosDB.Table и его зависимости с помощью консоли диспетчера пакетов. Для этого введите в консоли диспетчера пакетов такую команду: 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Используя предпочитаемый инструмент управления пакетами Nuget, сначала установите пакет Microsoft.Azure.Storage.Common, а потом — Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Часто задаваемые вопросы

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения об API таблицы для базы данных Azure Cosmos DB см. в [этой статье](table-introduction.md). 
