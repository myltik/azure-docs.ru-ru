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
	ms.date="07/13/2015" 
	ms.author="awills"/>

# Мониторинг сайта SharePoint с помощью Application Insights


[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights позволяет отслеживать доступность, производительность и использование приложений. В этой статье вы узнаете, как настроить эту службу для сайта SharePoint.


## Создание ресурса Application Insights


На [портале Azure](http://portal.azure.com) создайте новый ресурс Application Insights. Выберите приложение ASP.NET в качестве типа приложения.

![Нажмите «Свойства», выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-sharepoint/01-new.png)


В отобразившейся колонке будут содержатся данные о производительности и использовании приложения. Чтобы вернуться к ней после следующего входа в Azure, найдите соответствующую плитку на начальном экране. Ее также можно найти, щелкнув «Обзор».
    


## Добавьте свой скрипт на веб-страницы

В разделе «Быстрый запуск» получите сценарий для веб-страниц:

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Вставьте сценарий прямо перед тегом &lt;/head&gt; на каждой странице, которую необходимо отслеживать. Если на вашем веб-сайте есть главная страница, можно разместить скрипт на ней. Например, в проекте ASP.NET MVC сценарий следует разместить на странице View\\Shared_Layout.cshtml.

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

Вернитесь к колонке приложения на [портале Azure](http://portal.azure.com).

В поиске по журналу диагностики будут отображаться первые события.

![](./media/app-insights-sharepoint/09-search.png)

Если вам требуется больше данных, нажмите кнопку «Обновить» через несколько секунд.

**Аналитика использования** ссылается на диаграммы пользователей, сеансы и просмотры страниц:

![](./media/app-insights-sharepoint/06-usage.png)

Например, щелкните диаграмму «Просмотры страниц», чтобы увидеть дополнительные сведения:

![](./media/app-insights-sharepoint/07-pages.png)

Щелкните диаграмму «Пользователи», чтобы получить информацию о новых пользователях и их расположениях.


![](./media/app-insights-sharepoint/08-users.png)



## Дальнейшие действия

* [Использование веб-тестов](app-insights-monitor-web-app-availability.md) для мониторинга доступности сайта.

* [Использование Application Insights](app-insights-get-started.md) для других типов приложений.



<!--Link references-->

<!---HONumber=July15_HO3-->