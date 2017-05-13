---
title: "Модель данных телеметрии Azure Application Insights — телеметрия трассировки | Документы Майкрософт"
description: "Модель данных Application Insights для телеметрии трассировки"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: sergkanz
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 178b1ec0edce5a3fb20dc5c591e12cfe6e721cf2
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="trace-telemetry-application-insights-data-model"></a>Телеметрия трассировки: модель данных Application Insights

Телеметрия трассировки (в [Application Insights](app-insights-overview.md)) представляет инструкции трассировки стиля `printf`, доступные для текстового поиска. `Log4Net`, `NLog` и другие записи файла журнала на основе текста преобразуются в экземпляры этого типа. Трассировка не имеет измерений в качестве расширения.

## <a name="message"></a>Сообщение

Сообщение трассировки.

Максимальная длина: 32 768 символов

## <a name="severity-level"></a>Уровень серьезности

Уровень серьезности трассировки. Возможные значения: `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Просмотр журналов трассировки .NET в Application Insights](app-insights-asp-net-trace-logs.md).
- [Просмотр журналов трассировки Java в Application Insights](app-insights-java-trace-logs.md).
- В [этой статье](application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- [Написание пользовательской телеметрии трассировки](app-insights-api-custom-events-metrics.md#tracktrace)
- Ознакомление с [платформами](app-insights-platforms.md), поддерживаемыми Application Insights.

