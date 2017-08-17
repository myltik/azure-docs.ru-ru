---
title: "Аналитика — мощный инструмент поиска Azure Application Insights | Документация Майкрософт"
description: "Обзор аналитики — мощного инструмента поиска Application Insights. "
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: a2dc351bd0346f5ca46f1eaafeff678c3339c8c9
ms.contentlocale: ru-ru
ms.lasthandoff: 08/02/2017

---
# <a name="analytics-in-application-insights"></a>Аналитика в Application Insights
[Аналитика](app-insights-analytics.md) — это мощный инструмент поиска [Application Insights](app-insights-overview.md). На этих страницах описан язык запросов Log Analytics. 

* **[Просмотрите видео с вводной информацией](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Протестируйте аналитику на смоделированных данных](https://analytics.applicationinsights.io/demo)**, если ваше приложение еще не отправляет данные в Application Insights.
* **[Памятка для пользователей SQL](https://aka.ms/sql-analytics)** содержит сопоставление наиболее распространенных идиом.
* **[Справочник по языку](app-insights-analytics-reference.md)** позволит научиться использовать все возможности языка запросов Log Analytics.


## <a name="queries-in-analytics"></a>Запросы в аналитике
Типичный запрос содержит *исходную* таблицу и ряд *операторов*, разделенных `|`. 

Например, давайте выясним, в какое время суток граждане Хайдерабада испытывают наше веб-приложение. Кроме того, давайте еще посмотрим, какие коды результата возвращаются в ответ на их HTTP-запросы. 

```AIQL
requests
| where timestamp > ago(30d)
| summarize ClientCount = dcount(client_IP) by bin(timestamp, 1h), resultCode
| extend LocalTime = timestamp - 4h
| order by LocalTime desc
| render barchart
```

Мы можем сосчитать отдельные клиентские IP-адреса, сгруппировав их по часам дня за последние 7 дней. 

> [!NOTE]
> Чтобы получить результаты за время, выходящее за пределы последних 24 часов, явным образом включите метку времени в запрос или воспользуйтесь раскрывающимся меню диапазона времени.
>

Давайте отобразим результаты на линейчатой диаграмме, распределив результаты по разным кодам ответа.

![Выберите линейчатую диаграмму, оси x и y, а затем сегментацию.](./media/app-insights-analytics/020.png)

Похоже, в Хайдерабаде наше приложение наиболее популярно в обеденное время и время отхода ко сну. (И нам следует изучить эти 500 кодов.)

Кроме того, существуют сложные статистические операции.

![Результаты статистического запроса](./media/app-insights-analytics/025.png)

Язык имеет много привлекательных функций.


* [Фильтрация](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) необработанных данных телеметрии приложения по любым полям, включая пользовательские свойства и метрики.
* [Соединение](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) нескольких таблиц — соотношение запросов с просмотрами страниц, вызовами зависимостей, исключениями и трассировками журнала.
* Сложные статистические [агрегаты](https://docs.loganalytics.io/learn/tutorials/aggregations.html).
* Такие же мощные, как в SQL, но гораздо проще для составления сложных запросов: вместо вложенных инструкций данные передаются из одной простой операции в другую.
* Мгновенные яркие визуализации.
* [Закрепление диаграмм на панелях мониторинга Azure](app-insights-analytics-using.md#pin-to-dashboard).
* [Экспорт запросов в Power BI](app-insights-analytics-using.md#export-to-power-bi).
* Существует интерфейс [REST API](https://dev.applicationinsights.io/), который можно использовать для программного выполнения запросов, например из PowerShell.


## <a name="connect-to-your-application-insights-data"></a>Подключение к данным Application Insights
Откройте аналитику в [колонке "Обзор"](app-insights-dashboards.md) приложения в Application Insights. 

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/app-insights-analytics/001.png)


## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]



## <a name="query-examples"></a>Примеры запросов

Воспользуйтесь этими пошаговыми руководствами, чтобы продемонстрировать возможности использования аналитики:

 *  [Автоматическая диагностика пиков и пропуска шагов в контексте длительности запросов.](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Automatic%20diagnostics%20of%20sudden%20spikes%20or%20step%20jumps%20in%20requests%20duration&shared=true)
 *  [Анализ снижения производительности путем анализа временных рядов.](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20performance%20degradations%20with%20time%20series%20analysis&shared=true)
 *  [Анализ сбоев приложения с помощью функций autocluster и diffpatterns.](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20application%20failures%20with%20autocluster%20and%20diffpatterns&shared=true)
 *  [Расширенное определение формы путем анализа временных рядов.](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Advanced%20shape%20detection%20with%20time%20series%20analysis&shared=true)
 *  [Использование операций скользящего окна для анализа использования приложений (скользящее MAU, DAU и т. д.).](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Using%20sliding%20window%20calculations%20to%20analyze%20usage%20metrics:%20rolling%20MAU~2FDAU%20and%20cohorts&shared=true)
 *  [Обнаружение нарушений в работе службы на основе анализа журналов отладки](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Detection%20of%20service%20disruptions%20based%20on%20regression%20analysis%20of%20trace%20logs&shared=true) и соответствующая запись блога [здесь](https://maximshklar.wordpress.com/2017/02/16/finding-trends-in-traces-with-smart-data-analytics).
 *  [Профилирование производительности приложений с помощью простых журналов отладки](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Profiling%20applications'%20performance%20with%20simple%20debug%20logs&shared=true) и соответствующая запись блога [здесь](https://yossiattasblog.wordpress.com/2017/03/13/first-blog-post/).
 *  [Измерения длительности каждого шага в потоке кода с помощью простых журналов отладки](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Measuring%20the%20duration%20of%20each%20step%20in%20your%20code%20flow%20using%20simple%20debug%20logs&shared=true) и соответствующая запись блога [здесь](https://yossiattasblog.wordpress.com/2017/03/14/measuring-the-duration-of-each-step-in-your-code-flow-using-simple-debug-logs/).
 *  [Анализ параллелизма с помощью простых журналов отладки](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Analyzing%20concurrency%20with%20simple%20debug%20logs&shared=true) и соответствующая запись блога [здесь](https://yossiattasblog.wordpress.com/2017/03/23/analyzing-concurrency-using-simple-debug-logs/).



## <a name="next-steps"></a>Дальнейшие действия
* Мы рекомендуем начать со [знакомства с языком](app-insights-analytics-tour.md). 
* Дополнительные сведения об [использовании аналитики](app-insights-analytics-using.md). 
* [Справочник по языку](app-insights-analytics-reference.md). 

