---
title: "Модель данных телеметрии Azure Application Insights — телеметрия событий | Документы Майкрософт"
description: "Модель данных Application Insights для телеметрии событий"
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
ms.openlocfilehash: 5b8b803f26dca82b5112568f486e5c347602a409
ms.lasthandoff: 04/22/2017


---
# <a name="event-telemetry-application-insights-data-model"></a>Телеметрия событий: модель данных Application Insights

События представляют момент времени, когда в приложении выполняется действие. Обычно это взаимодействие с пользователем, например нажатие кнопки или оформление заказа. Кроме того, это может быть событие жизненного цикла приложения, такое как инициализация или изменение конфигурации. В качестве имени события ожидается короткая строка с низкой кратностью. 

Семантически события могут как коррелировать, так и не коррелировать с запросами. Однако при правильном использовании телеметрия событий важнее, чем запросы или трассировки. События представляют бизнес-телеметрию и должны подвергаться отдельной, менее интенсивной выборке.

## <a name="name"></a>Имя

Имя события. Сохраните низкую кратность, чтобы обеспечить правильную группировку и значимость метрик.

Максимальная длина: 512 символов

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Пользовательские измерения

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Дальнейшие действия

- В [этой статье](/application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- Вы можете узнать, как использовать [API Application Insights для пользовательских событий и метрик](/app-insights-asp-net-dependencies.md).
- Ознакомление с [платформами](/app-insights-platforms.md), поддерживаемыми Application Insights.

