---
title: Пакет SDK для .NET и ресурсы API таблицы для базы данных Azure Cosmos DB | Документация Майкрософт
description: Сведения об API таблиц Azure Cosmos DB, в том числе даты выпуска и даты вывода из эксплуатации, а также изменения, внесенные в каждую версию.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/26/2018
ms.author: rnagpal
ms.openlocfilehash: 7e012e07b8f93554ea44404c611a7bc0eb64a0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
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
|**Текущая поддерживаемая платформа**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Если вы создали учетную запись API таблиц на этапе работы с предварительной версией, для работы с общедоступными пакетами SDK для API таблиц создайте [новую учетную запись](create-table-dotnet.md#create-a-database-account).
>

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Добавлена проверка тегов сущности неправильного формата в режиме прямого подключения.
* Исправлена ошибка, связанная с запросом LINQ, в режиме шлюза.
* Синхронные API теперь запускаются в пуле потоков с помощью SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Добавлены TableRequestOptions TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism и TableQueryContinuationTokenLimitInKb.
* Исправления ошибок

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Выпуск общедоступной версии

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0 (предварительная версия)
* Первоначальный выпуск предварительной версии

## <a name="release-and-retirement-dates"></a>Даты выпуска и вывода из эксплуатации
Корпорация Майкрософт отправляет уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Предварительная версия пакета [WindowsAzure.Storage PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) отмечена как нерекомендуемая и заменена на пакет [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Поддержка пакета SDK WindowsAzure.Storage PremiumTable будет прекращена 15 ноября 2018 г. В это время запросы к устаревшему SDK будут запрещены.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии. 

Любые запросы к базе данных Azure Cosmos DB с помощью выведенного из эксплуатации пакета SDK отклоняются службой.
<br/>

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.1.1](#1.1.1) |26 марта 2018 г.|--- |
| [1.1.0](#1.1.0) |21 февраля 2018 г.|--- |
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
