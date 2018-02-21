---
title: "Модель данных телеметрии Azure Application Insights — телеметрия событий | Документы Майкрософт"
description: "Модель данных Application Insights для телеметрии событий"
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
ms.author: mbullwin
ms.openlocfilehash: dc6ef1771f4f09b274f12606d1278e1f29c23103
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2017
---
# <a name="event-telemetry-application-insights-data-model"></a>Телеметрия событий: модель данных Application Insights

Элементы телеметрии событий, которые можно создавать в [Application Insights](app-insights-overview.md), представляют событие, произошедшее в приложении. Обычно это взаимодействие с пользователем, например нажатие кнопки или оформление заказа. Кроме того, это может быть событие жизненного цикла приложения, такое как инициализация или изменение конфигурации. 

Семантически события могут как коррелировать, так и не коррелировать с запросами. Однако при правильном использовании телеметрия событий важнее, чем запросы или трассировки. События представляют бизнес-телеметрию и должны подвергаться отдельной, менее интенсивной [выборке](app-insights-api-filtering-sampling.md).

## <a name="name"></a>ИМЯ

Имя события. Чтобы обеспечить правильную группировку и значимость метрик, настройте в приложении создание небольшого количества имен отдельных событий. Например, не используйте отдельное имя для каждого созданного экземпляра события.

Максимальная длина: 512 символов

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Пользовательские измерения

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Дополнительная информация

- В [этой статье](application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- [Написание пользовательской телеметрии событий](app-insights-api-custom-events-metrics.md#trackevent)
- Ознакомление с [платформами](app-insights-platforms.md), поддерживаемыми Application Insights.
