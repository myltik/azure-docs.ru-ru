---
title: Analytics — мощный инструмент Azure Application Insights для поиска и создания запросов | Документация Майкрософт
description: 'Обзор аналитики — мощного инструмента поиска Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 170cd76c72e8aeb5de48c711ae4637a0244742fb
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294206"
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

* [Фильтрация](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) необработанных данных телеметрии приложения по любым полям, включая пользовательские свойства и метрики.
* [Соединение](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/join-operator) нескольких таблиц — соотношение запросов с просмотрами страниц, вызовами зависимостей, исключениями и трассировками журнала.
* Сложные статистические [агрегаты](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions).
* Мгновенные яркие визуализации.
* [REST API](https://dev.applicationinsights.io/), который можно использовать для программного выполнения запросов, например из PowerShell.

[Полный справочник по языку](https://go.microsoft.com/fwlink/?linkid=856079), который описывает все поддерживаемые команды. Он регулярно обновляется.

## <a name="next-steps"></a>Дополнительная информация
* Начало работы с [порталом Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Написание запросов](https://go.microsoft.com/fwlink/?linkid=856078)
* Просмотр [памятки для пользователей SQL](https://aka.ms/sql-analytics), которая содержит самые распространенные идиомы для преобразования.
* Тестирование Analytics на нашей [площадке](https://analytics.applicationinsights.io/demo), если ваше приложение не отправляет данные в Application Insights.
* Просмотр видео с [вводной информацией](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).