<properties 
	pageTitle="Использование аналитики — эффективного инструмента поиска Application Insights | Microsoft Azure" 
	description="Использование аналитики — эффективного инструмента поиска по журналу диагностики Application Insights." 
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
	ms.date="03/30/2016" 
	ms.author="awills"/>




# Использование аналитики в Application Insights


[Аналитика](app-insights-analytics.md) — это эффективный инструмент поиска [Application Insights](app-insights-overview.md). На этих страницах описан язык запросов аналитики приложений.


## Открытие аналитики

В домашнем ресурсе вашего приложения в Application Insights щелкните "Аналитика".

![На сайте portal.azure.com откройте ресурс Application Insights и щелкните "Аналитика".](./media/app-insights-analytics-using/001.png)

Встроенный учебник подаст вам некоторые идеи о возможностях.

Смотрите [исчерпывающий обзор здесь](app-insights-analytics-tour.md).

## Написание запросов

Напишите запрос, начиная с имен каких-либо таблиц, приведенных в левой части окна. Используйте `|` для создания конвейера [операторов](app-insights-analytics-reference.md#queries-and-operators).


![](./media/app-insights-analytics-using/150.png)

* Не помещайте в запрос пустые строки.
* В запросе можно использовать только однократные разрывы строк.
* Чтобы разместить несколько запросов в одном окне, разделяйте их пустыми строками.
* Для выполнения запроса **поместите курсор внутри или в конце его** и нажмите кнопку "Go" (Вперед).


![](./media/app-insights-analytics-using/130.png)

* Содержимое окна запроса можно сохранять и повторно вызывать.

![](./media/app-insights-analytics-using/140.png)

## Упорядочение результатов

Можно выбрать столбцы, которые вы хотите видеть. Разверните любой элемент, чтобы увидеть все возвращенные значения столбцов.

![](./media/app-insights-analytics-using/030.png)

> [AZURE.NOTE] Чтобы быстро изменить порядок результатов, доступных в веб-браузере, щелкните заголовок столбца. Но имейте в виду, что для большого результирующего набора в браузер загружается ограниченное количество строк. Поэтому сортировка таким способом не всегда показывает фактические наибольшие или наименьшие элементы. Для этого следует использовать оператор [top](app-insights-analytics-reference.md#top-operator) или [sort](app-insights-analytics-reference.md#sort-operator).

Однако рекомендуется применять операторы [take](app-insights-analytics-reference.md#take-operator), [top](app-insights-analytics-reference.md#top-operator) или [summarize](app-insights-analytics-reference.md#summarize-operator), чтобы избежать скачивания больших таблиц с сервера. В любом случае существует автоматическое ограничение — около 10 000 строк в запросе.


## Схемы

Выберите тип схемы, которая вам нужна:

![](./media/app-insights-analytics-using/230.png)

Если имеется несколько столбцов правильных типов, можно выбрать оси x и y и столбец измерений, чтобы разделять по ним результаты:

![](./media/app-insights-analytics-using/100.png)

По умолчанию результаты изначально отображаются в виде таблицы, а схему вы выбираете вручную. Однако можно использовать [директиву render](app-insights-analytics-reference.md#render-directive) в конце запроса, чтобы выбрать схему.

## Экспорт в Excel

После выполнения запроса можно скачать CSV-файл. Щелкните **Экспорт в Excel**.

## Экспорт в Power BI

1. Поместите курсор в запрос, а затем выберите **Export to Power BI** (Экспорт в Power BI).

    ![](./media/app-insights-analytics-using/240.png)

    При этом скачивается файл скрипта M.

3. Скопируйте скрипт на языке M в расширенный редактор запросов Power BI Desktop.
 * Откройте экспортированный файл.
 * В Power BI Desktop выберите **"Получить данные", "Пустой запрос", "Расширенный редактор"** и вставьте этот сценарий на языке M.

    ![](./media/app-insights-analytics-using/250.png)

4. При необходимости измените учетные данные. Теперь можно строить отчет.

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0615_2016-->