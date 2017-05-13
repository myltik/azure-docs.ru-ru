---
title: "Модель данных телеметрии Azure Application Insights | Документы Майкрософт"
description: "Обзор модели данных Application Insights"
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
ms.openlocfilehash: 7dd240c4e1a6fcc9c89bf4418e635e7ef8ef0617
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="application-insights-telemetry-data-model"></a>Модель данных телеметрии Application Insights

[Azure Application Insights](app-insights-overview.md) отправляет данные телеметрии из веб-приложения на портал Azure. Благодаря этому вы можете анализировать производительность и параметры использования приложения. Модель телеметрии стандартизирована. Это позволяет создавать сценарии мониторинга, независимые от платформы и языка. 

Данные, собранные Application Insights, моделируют типичный шаблон выполнения приложения:

![Модель приложений Application Insights](./media/application-insights-data-model/application-insights-data-model.png)

Приведенные ниже типы телеметрии позволяют отслеживать выполнение приложения. Пакет SDK для Application Insights автоматически собирает на платформе веб-приложений следующие три типа телеметрии:

* [**Запрос.**](application-insights-data-model-request-telemetry.md) Используется для регистрации запроса, полученного приложением. Например, веб-пакет SDK для Application Insights автоматически создает элемент телеметрии запроса для каждого HTTP-запроса, полученного веб-приложением. 

    **Операция** — это поток выполнений, которые обрабатывают запрос. Вы также можете [написать код](app-insights-api-custom-events-metrics.md#trackrequest) для мониторинга других типов операций, таких как "пробуждение" в веб-заданиях, или функций, которые периодически обрабатывают данные.  Каждая операция имеет идентификатор, который можно использовать, чтобы группировать другие данные телеметрии, созданные во время обработки запроса приложением. Каждая операция выполняется в течение определенного периода времени, после чего может завершиться успешно или сбоем.
* [**Исключение.**](application-insights-data-model-exception-telemetry.md) Обычно представляет исключение, вызывающее сбой операции.
* [**Зависимость.**](application-insights-data-model-dependency-telemetry.md) Представляет собой вызов из приложения к внешней службе или хранилищу, например REST API или SQL. В ASP.NET вызовы зависимостей SQL определяются `System.Data`. Вызовы конечных точек HTTP определяются `System.Net`. 

Application Insights предоставляет три дополнительных типа данных пользовательской телеметрии:

* [Трассировка.](application-insights-data-model-trace-telemetry.md) Реализует ведение журнала диагностики напрямую или через адаптер с помощью привычной платформы инструментирования, такой как `Log4Net` или `System.Diagnostics`.
* [Событие.](application-insights-data-model-event-telemetry.md) Обычно используется для записи взаимодействия пользователя со службой для анализа шаблона использования.
* [Метрика.](application-insights-data-model-metric-telemetry.md) Используется для создания отчета о периодических скалярных измерениях.

Модель телеметрии Application Insights определяет способ [корреляции](application-insights-correlation.md) телеметрии с операцией, частью которой она является. Например, запрос может выполнять вызовы базы данных SQL и записывать сведения о диагностике. Вы можете задать контекст корреляции для этих элементов телеметрии, которые попадут обратно в телеметрию запросов.

## <a name="schema-improvements"></a>Усовершенствования схемы

Модель данных Application Insights представляет собой простой и эффективный способ моделирования телеметрии вашего приложения. Мы стремимся сохранить простоту модели, чтобы поддержать ключевые сценарии и обеспечить возможность расширения схемы для углубленного использования.

Чтобы сообщить о проблемах или предложениях, связанных с моделью данных или схемой, используйте репозиторий [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) GitHub.

## <a name="next-steps"></a>Дальнейшие действия

- [API Application Insights для пользовательских событий и метрик](app-insights-api-custom-events-metrics.md)
- Вы можете узнать, как [расширять и фильтровать данные телеметрии](app-insights-api-filtering-sampling.md).
- Используйте [выборку](app-insights-sampling.md), чтобы минимизировать объем данных телеметрии на основе модели данных.
- Ознакомление с [платформами](app-insights-platforms.md), поддерживаемыми Application Insights.

