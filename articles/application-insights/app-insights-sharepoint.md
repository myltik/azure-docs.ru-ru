<properties 
	pageTitle="Мониторинг сайта SharePoint с помощью Application Insights" 
	description="Начало мониторинга нового приложения с помощью нового ключа инструментирования" 
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
	ms.date="11/06/2015" 
	ms.author="awills"/>

# Мониторинг сайта SharePoint с помощью Application Insights


[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights позволяет отслеживать доступность, производительность и использование приложений. В этой статье вы узнаете, как настроить эту службу для сайта SharePoint.


## Создание ресурса Application Insights


На [портале Azure](https://portal.azure.com) создайте новый ресурс Application Insights. Выберите приложение ASP.NET в качестве типа приложения.

![Нажмите «Свойства», выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-sharepoint/01-new.png)


В отобразившейся колонке будут содержатся данные о производительности и использовании приложения. Чтобы возвратиться к нему после очередного входа в Azure, найдите соответствующую плитку на начальном экране. Ее также можно найти, щелкнув «Обзор».
    


## Добавьте свой скрипт на веб-страницы

В разделе «Быстрый запуск» получите сценарий для веб-страниц:

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Вставьте сценарий прямо перед тегом &lt;/head&gt; на каждой странице, которую необходимо отслеживать. Если на вашем веб-сайте есть главная страница, можно разместить скрипт на ней. Например, в проекте ASP.NET MVC сценарий следует разместить на странице View\\Shared\_Layout.cshtml.

Сценарий содержит ключ инструментирования, который направляет данные телеметрии к ресурсу Application Insights.

### Добавление кода на страницы сайта

#### На главной странице

Отредактировав главную страницу сайта, можно обеспечить мониторинг каждой страницы сайта.

Ознакомьтесь с главной страницей и измените ее с помощью SharePoint Designer или другого редактора.

![](./media/app-insights-sharepoint/03-master.png)


Добавьте код прямо перед тегом </head>.


![](./media/app-insights-sharepoint/04-code.png)

#### На отдельных страницах

Для мониторинга ограниченного набора страниц добавьте сценарий отдельно для каждой страницы.

Вставьте веб-часть и внедрите в нее фрагмент кода.


![](./media/app-insights-sharepoint/05-page.png)


## Просмотр данных о приложении

Разверните приложение заново.

Вернитесь к колонке приложения на [портале Azure](https://portal.azure.com).

В окне поиска отобразятся первые события.

![](./media/app-insights-sharepoint/09-search.png)

Нажмите кнопку «Обновить» через несколько секунд, если ожидаете дополнительные данные.

В колонке "Обзор" выберите **Аналитика использования**, чтобы просмотреть диаграммы пользователей, сеансов и просмотров страницы:

![](./media/app-insights-sharepoint/06-usage.png)

Щелкните любую диаграмму для просмотра дополнительных сведений, например "Просмотры страницы":

![](./media/app-insights-sharepoint/07-pages.png)

или "Пользователей":


![](./media/app-insights-sharepoint/08-users.png)



## Дальнейшие действия

* [Использование веб-тестов](app-insights-monitor-web-app-availability.md) для мониторинга доступности сайта.

* [Использование Application Insights](app-insights-overview.md) для других типов приложений.



<!--Link references-->

<!---HONumber=AcomDC_0128_2016-->