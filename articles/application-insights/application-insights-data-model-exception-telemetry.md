---
title: "Модель данных телеметрии Azure Application Insights — телеметрия исключений | Документы Майкрософт"
description: "Модель данных Application Insights для телеметрии исключений"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 17a39660fce598610ff9a95e886282e6b3faffe4
ms.lasthandoff: 04/22/2017


---
# <a name="exception-telemetry-application-insights-data-model"></a>Телеметрия исключений: модель данных Application Insights

Экземпляр исключения представляет обработанное или необработанное исключение, возникшее во время выполнения отслеживаемого приложения.

## <a name="problem-id"></a>Идентификатор проблемы

Идентификатор места в коде, где возникло исключение. Используется для группирования исключений. Обычно это сочетание типа исключения и функции из стека вызовов.

Максимальная длина: 1024 символа

## <a name="severity-level"></a>Уровень серьезности

Уровень серьезности трассировки. Возможные значения: `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Сведения об исключении

Работа над этим разделом все еще ведется...

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Пользовательские измерения

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Дальнейшие действия

- В [этой статье](/application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- Вы можете научиться [диагностировать исключения в веб-приложениях с помощью Application Insights](/app-insights-asp-net-exceptions.md).
- Ознакомление с [платформами](/app-insights-platforms.md), поддерживаемыми Application Insights.

