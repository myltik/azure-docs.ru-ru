---
title: Аналитика — эффективный инструмент поиска Application Insights | Microsoft Docs
description: 'Обзор аналитики — мощного инструмента поиска Application Insights. '
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: awills

---
# Аналитика в Application Insights
[Аналитика](app-insights-analytics.md) — это мощный инструмент поиска [Application Insights](app-insights-overview.md). На этих страницах описан язык запросов аналитики приложений.

* **[Просмотрите видео с вводной информацией](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Протестируйте аналитику на смоделированных данных](https://analytics.applicationinsights.io/demo)**, если ваше приложение еще не отправляет данные в Application Insights.

## Запросы в аналитике
Типичный запрос содержит *исходную* таблицу и ряд *операторов*, разделенных `|`.

Например, давайте выясним, в какое время суток граждане Хайдерабада испытывают наше веб-приложение. Кроме того, давайте еще посмотрим, какие коды результата возвращаются в ответ на их HTTP-запросы.

```AIQL

    requests      // Table of events that log HTTP requests.
    | where timestamp > ago(7d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Мы можем сосчитать отдельные клиентские IP-адреса, сгруппировав их по часам дня за последние 7 дней.

Давайте отобразим результаты на линейчатой диаграмме, распределив результаты по разным кодам ответа.

![Выберите линейчатую диаграмму, оси x и y, а затем сегментацию.](./media/app-insights-analytics/020.png)

Похоже, в Хайдерабаде наше приложение наиболее популярно в обеденное время и время отхода ко сну. (И нам следует изучить эти 500 кодов.)

Кроме того, существуют сложные статистические операции.

![](./media/app-insights-analytics/025.png)

Язык имеет много привлекательных функций.

* [Фильтрация](app-insights-analytics-reference.md#where-operator) необработанных данных телеметрии приложения по любым полям, включая пользовательские свойства и метрики.
* [Соединение](app-insights-analytics-reference.md#join-operator) нескольких таблиц — соотношение запросов с просмотрами страниц, вызовами зависимостей, исключениями и трассировками журнала.
* Сложные статистические [агрегаты](app-insights-analytics-reference.md#aggregations).
* Такие же мощные, как в SQL, но гораздо проще для составления сложных запросов: вместо вложенных инструкций данные передаются из одной простой операции в другую.
* Мгновенные яркие визуализации.

## Подключение к данным Application Insights
Откройте аналитику в [колонке "Обзор"](app-insights-dashboards.md) приложения в Application Insights.

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/app-insights-analytics/001.png)

## Ограничения
В настоящее время результаты запросов ограничены данными за последнюю неделю.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

## Дальнейшие действия
* Мы рекомендуем начать со [знакомства с языком](app-insights-analytics-tour.md).

<!---HONumber=AcomDC_0831_2016-->