<properties 
	pageTitle="Настройка аналитики веб-приложения для ASP.NET с помощью Application Insights" 
	description="Настройка средств аналитики производительности, доступности и использования для веб-сайта ASP.NET, размещенного локально или в Azure." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="awills"/>


# Настройка Application Insights для ASP.NET

[Visual Studio Application Insights](app-insights-overview.md) выполняет мониторинг работы действующего приложения, благодаря чему вы можете [обнаруживать и диагностировать проблемы, связанные с производительностью и исключениями](app-insights-detect-triage-diagnose.md), а также [просматривать показатели использования приложения](app-insights-overview-usage.md). Эта услуга работает для приложений, размещенных на локальных серверах IIS или на облачных виртуальных машинах, а также для веб-приложений Azure.


## Перед началом работы

Вам необходимы:

* Visual Studio 2013 с обновлением 3 или более новая версия. Чем новее версия, тем лучше.
* подписка на [Microsoft Azure](http://azure.com). Если у вашей группы или организации есть подписка Azure, владелец может добавить вас в нее с помощью вашей [учетной записи Майкрософт](http://live.com). 

Существуют и другие статьи, к которым можно обратиться, если вас интересуют такие темы, как:

* [инструментирование веб-приложения во время выполнения](app-insights-monitor-performance-live-website-now.md);
* [ASP.NET Core](app-insights-asp-net-core.md);
* [облачные службы Azure](app-insights-cloudservices.md).

## <a name="ide"></a> 1. Добавление пакета SDK для Application Insights


### Добавление в новый проект

При создании нового проекта в Visual Studio установите флажок для компонента Application Insights.


![Создание проекта ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### ... или, если это существующий проект

В обозревателе решений щелкните проект правой кнопкой мыши и выберите пункт **Добавить телеметрию Application Insights** или **Настроить Application Insights**.

![Выберите пункт Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)




## <a name="run"></a> 2. Запуск приложения для Android

Запустите приложение, нажав клавишу F5, и попробуйте открывать разные страницы, чтобы создать некоторый объем данных телеметрии.

В Visual Studio вы увидите число записанных в журнал событий.

![В Visual Studio кнопка Application Insights доступна во время отладки.](./media/app-insights-asp-net/54.png)

## 3\. Просмотр данных телеметрии

### В Visual Studio

Откройте окно Application Insights в Visual Studio: нажмите кнопку Application Insights или щелкните проект в обозревателе решений правой кнопкой мыши:

![В Visual Studio кнопка Application Insights доступна во время отладки.](./media/app-insights-asp-net/55.png)

В этом представлении отображаются данные телеметрии, созданные в серверной части приложения. Поэкспериментируйте с фильтрами и щелкните любое событие, чтобы просмотреть подробные сведения.

[Дополнительные сведения о средствах Application Insights в Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### На портале

Если не выбран вариант *Установить только пакет SDK*, вы можете также просматривать данные телеметрии на веб-портале Application Insights.

На портале представлено больше дополнительных диаграмм, средств анализа и панелей мониторинга, чем в Visual Studio.


Откройте ресурс Application Insights на [портале Azure](https://portal.azure.com/).

![Щелкните проект правой кнопкой мыши и откройте портал Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

На портале откроется представление данных телеметрии из приложения: ![](./media/app-insights-asp-net/66.png)

* Отдельные события появляются в области **поиска** (1). Данные сначала отображаются здесь (и в динамическом потоке). Выберите любое событие, чтобы просмотреть его свойства. 
* Сводные показатели отображаются на диаграммах (2). Через пару минут данные появятся здесь. Щелкните любую диаграмму, чтобы открыть колонку с более подробной информацией.

[Дополнительные сведения об использовании Application Insights на портале Azure](app-insights-dashboards.md).

##<a name="land"></a> Что делает команда "Добавить Application Insights"

Application Insights отправляет данные телеметрии из приложения на портал Application Insights (размещенный в Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Эта команда выполняет три действия:

1. Добавляет в проект пакет NuGet с веб-пакетом SDK для Application Insights. Чтобы увидеть его в Visual Studio, щелкните правой кнопкой мыши проект и выберите пункт "Управление пакетами NuGet".
2. Создает ресурс Application Insights на [портале Azure](https://portal.azure.com/). Здесь будут отображаться ваши данные. Команда извлекает *ключ инструментирования*, идентифицирующий ресурс.
3. Вставляет ключ инструментирования в `ApplicationInsights.config`, чтобы пакет SDK мог отправлять данные телеметрии на портал.

При необходимости вы можете [выполнить эти действия вручную](app-insights-asp-net-manual.md).


## Что дальше?

| | 
|---|---
|**[Работа с Application Insights в Visual Studio](app-insights-visual-studio.md)**<br/>Отладка с помощью телеметрии, поиск по журналу диагностики, детализация кода.|![Visual Studio](./media/app-insights-asp-net/61.png)
|**[Работа с порталом Application Insights](app-insights-dashboards.md)**<br/>Панели мониторинга, мощные средства диагностики и анализа, оповещения, карта динамических зависимостей приложения, а также экспорт телеметрии. |![Visual Studio](./media/app-insights-asp-net/62.png)
|**[Добавление дополнительных данных](app-insights-asp-net-more.md)**<br/>Мониторинг использования, доступности, зависимостей и исключений. Интеграция трассировок из платформ ведения журналов. Написание пользовательской телеметрии. | ![Visual Studio](./media/app-insights-asp-net/64.png)

<!---HONumber=AcomDC_0608_2016-->