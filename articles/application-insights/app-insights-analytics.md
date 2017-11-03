---
title: "Analytics — мощный инструмент Azure Application Insights для поиска и создания запросов | Документация Майкрософт"
description: "Обзор аналитики — мощного инструмента поиска Application Insights. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 80a9e248ca50c11ef61a5c50c4986c4f8f4ead9d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2017
---
# <a name="analytics-in-application-insights"></a>Аналитика в Application Insights
Analytics — это мощный инструмент [Application Insights](app-insights-overview.md) для поиска и создания запросов. Analytics — это веб-инструмент, поэтому дополнительная настройка не требуется. Если вы уже настроили Application Insights для одного из приложений, вы можете проанализировать данные приложения, открыв Analytics в [колонке обзора](app-insights-dashboards.md) своего приложения.

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/app-insights-analytics/001.png)

Можно также использовать [площадку для Analytics](https://go.microsoft.com/fwlink/?linkid=859557) — бесплатную демонстрационную среду, содержащую множество примеров данных.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Запрос данных в Analytics
Типичный запрос содержит имя таблицы и ряд *операторов*, разделенных символом `|`.
Например, давайте узнаем, сколько запросов наше приложение получило из разных стран в течение последних трех часов:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Мы начнем с выполнения *запросов* имени таблицы и при необходимости добавим последовательность элементов.  Сначала мы определим фильтр времени, чтобы просмотреть записи только за последние три часа.
Затем мы подсчитаем число записей для каждой страны (эти данные находятся в столбце *client_CountryOrRegion*). Наконец, мы визуализируем полученные результаты на круговой диаграмме.
<br>

![Результаты запроса](./media/app-insights-analytics/030.png)

Язык имеет много привлекательных функций.

* [Фильтрация](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) необработанных данных телеметрии приложения по любым полям, включая пользовательские свойства и метрики.
* [Соединение](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) нескольких таблиц — соотношение запросов с просмотрами страниц, вызовами зависимостей, исключениями и трассировками журнала.
* Сложные статистические [агрегаты](https://docs.loganalytics.io/learn/tutorials/aggregations.html).
* Мгновенные яркие визуализации.
* [REST API](https://dev.applicationinsights.io/), который можно использовать для программного выполнения запросов, например из PowerShell.

[Полный справочник по языку](https://go.microsoft.com/fwlink/?linkid=856079), который описывает все поддерживаемые команды. Он регулярно обновляется.

## <a name="next-steps"></a>Дальнейшие действия
* Начало работы с [порталом Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Написание запросов](https://go.microsoft.com/fwlink/?linkid=856078)
* Просмотр [памятки для пользователей SQL](https://aka.ms/sql-analytics), которая содержит самые распространенные идиомы для преобразования.
* Тестирование Analytics на нашей [площадке](https://analytics.applicationinsights.io/demo), если ваше приложение не отправляет данные в Application Insights.
* Просмотр видео с [вводной информацией](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).