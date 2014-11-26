<properties title="Track usage with custom events and metrics" pageTitle="Track usage" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Отслеживание использования



## <a name="webclient"></a> Настройка аналитики использования веб-сайта

[Добавьте Application Insights в веб-проект][start], если вы еще не сделали этого.


## <a name="usage"></a> Аналитика использования

В колонке общих сведений о приложении вы увидите следующие плитки с аналитикой использования:

![](./media/appinsights/appinsights-47usage.png)


### Sessions per browser (Число сеансов на браузер)

*Сеанс* — это период, который начинается, когда пользователь открывает любую страницу на веб-сайте, и заканчивается, если пользователь не отправлял веб-запросов в течение 30 минут.

Чтобы увеличить диаграмму, щелкните ее.

### Top page views (Наиболее популярные страницы)

Показывает общее число просмотров за последние 24 часа.

Чтобы просмотреть более подробную историю, щелкните плитку с числом просмотров страницы.

![](./media/appinsights/appinsights-49usage.png)

Чтобы просмотреть более длительную историю за период до семи дней, щелкните временной диапазон.

Чтобы просмотреть другие доступные метрики, щелкните график.

![](./media/appinsights/appinsights-63usermetrics.png)

## Настраиваемые счетчики страниц

По умолчанию счетчик страницы увеличивается на единицу каждый раз, когда страница загружается в клиентском браузере. Однако вам может потребоваться подсчитать дополнительные просмотры страницы. Например, если содержимое страницы разбито на вкладки, возможно, вы захотите, чтобы счетчик увеличивался на единицу при каждом переключении между ними. Другой пример — ситуация, когда код JavaScript на странице загружает новое содержимое, но URL-адрес в браузере при этом не меняется.

Вставьте в соответствующем месте клиентского кода вызов JavaScript наподобие следующего:

    appInsights.trackPageView(myPageName);

Имя страницы может содержать те же символы, что и URL-адрес, но все символы после "#" или "?" игнорируются.

## Изучение отдельных событий просмотра страницы

Обычно телеметрия просмотра страниц анализируется службой Application Insights, и вы видите только сводные отчеты со средними значениями для всех пользователей. Однако в целях отладки можно также изучать отдельные события просмотра страниц.

В колонке Diagnostic Search (Поиск данных диагностики) в разделе Filter (Фильтр) установите флажок Page View (Просмотр страниц).

![](./media/appinsights/appinsights-51searchpageviews.png)

Выберите любое событие, чтобы просмотреть подробные сведения.

> [WACOM.NOTE] Имейте в виду, что [поиск][diagnostic] выполняется по целым словам: запросы "Информаци" и "нформация" не соответствуют слову "Информация", а запрос "Информаци\*" соответствует. Поисковый запрос нельзя начинать с подстановочного знака. Например, по запросу "\*нформаци" не будет найдено слово "Информация".

> [Подробнее о поиске данных диагностики][diagnostic]



## Отслеживание использования

> [AZURE.VIDEO tracking-usage-with-application-insights]


## Подробнее

* [Приступая к работе с Application Insights][start]
* [Отслеживание работающего веб-сервера][redfield]
* [Отслеживание производительности в веб-приложениях][perf]
* [Поиск по журналам диагностики][diagnostic]
* [Отслеживание доступности с помощью веб-тестов][availability]
* [Отслеживание использования][usage]
* [Часто задаваемые вопросы и устранение неполадок][qna]


<!--Link references-->

[start]: ../app-insights-start-monitoring-app-health-usage/
[0]: ./media/appinsights/appinsights-47usage.png
[1]: ./media/appinsights/appinsights-49usage.png
[2]: ./media/appinsights/appinsights-63usermetrics.png
[3]: ./media/appinsights/appinsights-51searchpageviews.png
[diagnostic]: ../app-insights-search-diagnostic-logs/
[redfield]: ../app-insights-monitor-performance-live-website-now/
[perf]: ../app-insights-web-monitor-performance/
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-web-track-usage/
[qna]: ../app-insights-troubleshoot-faq/
