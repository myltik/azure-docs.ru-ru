<properties 
	pageTitle="Аналитика приложений — мощная поисковая система для Application Insights" 
	description="Обзор аналитики приложений — мощной поисковой системы для Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/11/2016" 
	ms.author="awills"/>




# Аналитика Application Insights: обзор языка


[Аналитика Application Insights](app-analytics.md) — это мощный обработчик запросов для телеметрии [Application Insights](app-insights-overview.md). На этих страницах описан AIQL, язык запросов аналитики Application Insights.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
Типичный запрос AIQL содержит *исходную* таблицу и ряд *операторов*, разделенных `|`.

Например, давайте выясним, в какое время суток граждане Хайдерабада испытывают наше веб-приложение. Кроме того, давайте еще посмотрим, какие коды результата возвращаются в ответ на их HTTP-запросы.

```AIQL

    requests 
    | where timestamp > ago(30d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Мы можем сосчитать отдельные клиентские IP-адреса, сгруппировав их по часам дня за последние 30 дней.

Давайте отобразим результаты на линейчатой диаграмме, распределив результаты по разным кодам ответа.

![Выберите линейчатую диаграмму, оси x и y, а затем сегментацию.](./media/app-analytics/020.png)

Похоже, в Хайдерабаде наше приложение наиболее популярно в обеденное время и время отхода ко сну. (И нам следует изучить эти 500 кодов.)


Кроме того, существуют сложные статистические операции.

![](./media/app-analytics/025.png)


Язык имеет много привлекательных функций.

* [Фильтрация](app-analytics-queries.md) необработанных данных телеметрии приложения по любым полям, включая пользовательские свойства и метрики.
* [Соединение](app-analytics-queries.md#join-operator) нескольких таблиц — соотношение запросов с просмотрами страниц, вызовами зависимостей, исключениями и трассировками журнала.
* Мощные статистические [агрегаты](app-analytics-aggregations.md).
* Такие же мощные, как в SQL, но гораздо проще для составления сложных запросов: вместо вложенных инструкций данные передаются из одной простой операции в другую.
* Мгновенные яркие визуализации.



>[AZURE.NOTE] Мы рекомендуем начать со [знакомства с языком](app-analytics-tour.md).


## Подключение к данным Application Insights


Откройте аналитику в [колонке "Обзор"](app-insights-dashboards.md) вашего приложения в Application Insights.

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0316_2016-->