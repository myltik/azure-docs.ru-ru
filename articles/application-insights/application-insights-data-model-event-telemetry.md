---
title: Модель данных телеметрии Azure Application Insights — телеметрия событий | Документы Майкрософт
description: Модель данных Application Insights для телеметрии событий
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 4f6b5c35b65b4aff2dbe8dafbb2eb07d75c2382a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
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
