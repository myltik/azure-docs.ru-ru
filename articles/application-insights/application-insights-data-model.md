---
title: "Модель данных телеметрии Azure Application Insights | Документы Майкрософт"
description: "Обзор модели данных Application Insights"
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
ms.openlocfilehash: 52f700bd18da87a9a38e0a791d0ec3496f201e0a
ms.lasthandoff: 04/22/2017


---
# <a name="application-insights-telemetry-data-model"></a>Модель данных телеметрии Application Insights

Application Insights определяет модель данных телеметрии для управления производительностью приложений (APM). Эта модель стандартизирует сбор данных и позволяет создать сценарии мониторинга, независимые от платформы и языка. Данные, собранные Application Insights, моделируют типичный шаблон выполнения приложения:

![Модель приложений Application Insights](./media/application-insights-data-model/application-insights-data-model.png)

Существует два типа приложений — приложения с конечной точкой, принимающие внешние ***запросы*** (веб-приложения), и приложения, которые периодически "пробуждаются" для обработки данных, хранимых где-либо еще (веб-задания или функции). В обоих случаях мы называем уникальное выполнение ***операцией***. Об успехе или неудаче операции сообщает ***исключение***, кроме того, ее бизнес-логика может находиться в других службах и хранилищах. Чтобы отразить эти концепции, модель данных Application Insights определяет три типа телеметрии: [запрос](./application-insights-data-model-request-telemetry.md), [исключение](/application-insights-data-model-exception-telemetry.md) и [зависимость](/application-insights-data-model-dependency-telemetry.md).

Обычно эти типы определяются исполняющей средой и автоматически собираются пакетом SDK. `ASP.NET MVC` определяет нотацию выполнения запроса в его магистрали "модель-представление-контроллер" и помечает запуск и остановку запроса. Вызовы зависимостей SQL определяются `System.Data`. Вызовы конечных точек HTTP определяются `System.Net`. Вы можете расширить типы телеметрии, собираемые определенной платформой, с помощью пользовательских свойств и измерений. Однако в некоторых случаях нужна настраиваемая телеметрия. Вам может потребоваться реализовать ведение журналов диагностики с помощью привычной платформы инструментирования, такой как `Log4Net` или `System.Diagnostics`. Либо может потребоваться записать взаимодействие пользователя со службой для анализа характера использования. Application Insights распознает три дополнительных типа данных: [трассировка](/application-insights-data-model-trace-telemetry.md), [событие](/application-insights-data-model-event-telemetry.md) и [метрика](/application-insights-data-model-metric-telemetry.md) — для моделирования этих сценариев.

Модель телеметрии Application Insights определяет способ [корреляции](/correlation.md) телеметрии с операцией, частью которой она является. Например, запрос может выполнять вызовы базы данных SQL и записывать сведения о диагностике. Можно задать контекст корреляции для этих элементов телеметрии, которые попадут обратно в телеметрию запросов.

## <a name="schema-improvements"></a>Усовершенствования схемы

Модель данных Application Insights представляет собой простой и эффективный способ моделирования телеметрии вашего приложения. Мы стремимся сохранить простоту модели, чтобы поддержать ключевые сценарии и обеспечить возможность расширения схемы для углубленного использования.

Чтобы сообщить о проблемах или предложениях, связанных с моделью данных или схемой, используйте репозиторий [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) GitHub.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомление с [платформами](/app-insights-platforms.md), поддерживаемыми Application Insights.
- Вы можете узнать, как [расширять и фильтровать данные телеметрии](/app-insights-api-filtering-sampling.md).
- Используйте [выборку](/app-insights-sampling.md), чтобы минимизировать объем данных телеметрии на основе модели данных.

