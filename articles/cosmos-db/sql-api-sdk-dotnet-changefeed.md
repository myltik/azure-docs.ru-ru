---
title: API, пакет SDK и ресурсы обработчика канала изменений в .NET для Azure Cosmos DB | Документация Майкрософт
description: Сведения об API и пакете SDK для обработчика канала изменений, включая даты выхода, даты прекращения, а также изменения, внесенные в каждую версию пакета SDK для обработчика канала изменений в .NET.
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kfile
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/16/2018
ms.author: maquaran
ms.openlocfilehash: fe6dd9545be17453be38ce9afd5836aa07882ce6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Пакет SDK для обработчика канала изменений в .NET: скачивание и заметки о выпуске
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Веб-канал изменений в .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

|   |   |
|---|---|
|**Скачивание пакета SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Документация по API**|[Справочная документация по изменению API библиотеки обработчика веб-каналов](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Приступите к работе**|[Начало работы с пакетом SDK для обработчика канала изменений в .NET](change-feed.md)|
|**Текущая поддерживаемая платформа**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Улучшение стабильности.
* Поддержка использования контрольных точек вручную.
* Совместимость с [пакетом SDK .NET для SQL](sql-api-sdk-dotnet.md) версии 1.21 и выше.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Добавляет поддержку .NET Standard 2.0. Теперь пакет поддерживает моникеры платформ `netstandard2.0` и `net451`.
* Совместимость с [пакетом SDK для .NET для SQL](sql-api-sdk-dotnet.md) версии 1.17.0 и более поздних версий.
* Совместимость с [пакетом SDK для .NET Core для SQL](sql-api-sdk-dotnet-core.md) версии 1.5.1 и более поздних версий.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Устраняет проблему при расчете оставшейся работы, если канал изменений был пуст или никакие действия не ожидались.
* Совместимость с [пакетом SDK для .NET для SQL](sql-api-sdk-dotnet.md) версии 1.13.2 и более поздних версий.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Добавлен метод для оценки оставшейся работы для обработки в веб-канале изменений.
* Совместимость с [пакетом SDK для .NET для SQL](sql-api-sdk-dotnet.md) версии 1.13.2 и более поздних версий.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Пакет SDK общей доступности.
* Совместимость с [пакетом SDK для .NET для SQL](sql-api-sdk-dotnet.md) версии 1.14.1 и более ранних версий.

## <a name="release--retirement-dates"></a>Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии. 

Любые запросы к Cosmos DB с помощью выведенного из эксплуатации пакета SDK будут отклонены службой.

<br/>

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.3.1](#1.3.1) |13 марта 2018 г. |--- |
| [1.2.0](#1.2.0) |31 октября 2017 г. |--- |
| [1.1.1](#1.1.1) |29 августа 2017 г. |--- |
| [1.1.0](#1.1.0) |13 августа 2017 г. |--- |
| [1.0.0](#1.0.0) |7 июля 2017 г. |--- |


## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

