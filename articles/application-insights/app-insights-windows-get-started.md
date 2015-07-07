<properties 
	pageTitle="Application Insights для приложений Windows Phone и Магазина Windows" 
	description="Анализ использования и производительности приложения для устройства Windows с помощью Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="awills"/>

# Application Insights для приложений Windows Phone и Магазина Windows

*Application Insights находится в состоянии предварительной версии.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

С помощью Visual Studio Application Insights можно отслеживать следующие показатели опубликованного приложения.

* [**Использование**][windowsUsage] — узнайте, сколько пользователей у вас есть и что они делают с вашим приложением.
* [**Сбои**][windowsCrash] — получайте диагностические отчеты о сбоях и исследуйте их влияние на пользователей.

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)

Для многих типов приложений [Visual Studio может добавлять Application Insights в приложение](#ide) так, что от вас почти ничего не потребуется. Но поскольку вы читаете этот раздел, чтобы лучше понять, что происходит, мы рассмотрим выполнение соответствующих действий вручную.

Вам потребуется следующее:

* подписка на [Microsoft Azure][azure];
* Visual Studio 2013 или более поздняя версия.

## 1. Создание ресурса Application Insights 

На [портале Azure][portal] создайте новый ресурс Application Insights.

![Последовательно выберите пункты «Создать», «Службы для разработчиков», «Application Insights»](./media/app-insights-windows-get-started/01-new.png)

[Ресурс][roles] в Azure — это экземпляр службы. В этом ресурсе будет анализироваться и представляться телеметрия из вашего приложения.

#### Копирование ключа инструментирования

Этот ключ идентифицирует ресурс. Он скоро потребуется вам, чтобы настроить пакет SDK для отправки данных в ресурс.

![Откройте раскрывающуюся панель Essentials и выберите ключ инструментирования](./media/app-insights-windows-get-started/02-props.png)


## 2. Добавление пакета SDK Application Insights в приложение

В Visual Studio добавьте соответствующий пакет SDK в свой проект.

Если это универсальное приложение для Windows, повторите эти шаги для проекта Windows Phone и для проекта Windows.

1. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите пункт **Управление пакетами NuGet**.

    ![](./media/app-insights-windows-get-started/03-nuget.png)

2. Выберите узел **Online** (В сети), укажите **Include prerelease** (Включить предварительный выпуск) и выполните поиск «Application Insights».

    ![](./media/app-insights-windows-get-started/04-ai-nuget.png)

3. Выберите **Application Insights для приложений для Windows**.

4. Отредактируйте файл ApplicationInsights.config (который был добавлен установкой NuGet). Вставьте следующий фрагмент непосредственно перед закрывающим тегом:

    `<InstrumentationKey>`*скопированный ключ*`</InstrumentationKey>`

**Для универсальных приложений Windows**: повторите эти действия для проектов Phone и Магазина.

## <a name="network"></a>3. Включение доступа к сети для вашего приложения

Если приложение еще не [запросило исходящий доступ к сети](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx), вам нужно добавить это в его манифест как [обязательную возможность](https://msdn.microsoft.com/library/windows/apps/br211477.aspx).

## <a name="run"></a>4. Запуск проекта

[Запустите приложение с помощью клавиши F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) и используйте его, чтобы создать некоторый объем телеметрии.

В Visual Studio вы увидите число полученных событий.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

В режиме отладки телеметрия отправляется сразу же после ее создания. В режиме выпуска телеметрия хранится на устройстве и отправляется только тогда, когда приложение возобновляет работу.

## <a name="monitor"></a>5. Просмотр данных мониторинга

Откройте Application Insights из проекта.

![Щелкните проект правой кнопкой мыши и откройте портал Azure](./media/app-insights-windows-get-started/appinsights-04-openPortal.png)


Сначала вы увидите только одну или две точки. Например:

![Щелкните плитки, чтобы увидеть больше данных](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

Нажмите кнопку «Обновить» через несколько секунд, если ожидаете дополнительные данные.

Щелкните любую диаграмму, чтобы просмотреть более подробные сведения.


## <a name="deploy"></a>5. Публикация приложения в Магазине

[Опубликуйте свое приложение](http://dev.windows.com/publish) и наблюдайте за данными, которые собираются по мере загрузки и использования этого приложения пользователями.

## Что дальше?

* [Обнаружение и диагностика сбоев в приложении][windowsCrash]
* [Дополнительная информация о метриках][metrics].
* [Дополнительная информация о поиске по журналу диагностики][diagnostic].


## <a name="ide"></a>Автоматическая установка

Если вы предпочитаете использовать Visual Studio для выполнения шагов настройки, это можно сделать с приложениями Windows Phone, Магазина Windows и многими другими типами приложений.

###<a name="new"></a> Если создается новый проект приложения Windows...

Выберите Application Insights в диалоговом окне создания проекта.

Если появляется запрос на вход, используйте учетные данные для своей учетной записи Azure (которая отличается от учетной записи Visual Studio Online).

![](./media/app-insights-windows-get-started/appinsights-d21-new.png)


###<a name="existing"></a> Или если это существующий проект...

Добавьте Application Insights из обозревателя решений.


![](./media/app-insights-windows-get-started/appinsights-d22-add.png)


## <a name="usage"></a>Дальнейшие действия


[Обнаружение и диагностика сбоев в приложении][windowsCrash]

[Ведение журналов диагностики и поиск по ним][diagnostic]


[Отслеживание использования приложения][windowsUsage]

[Использование API для отправки пользовательской телеметрии][api]

[Устранение неполадок][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

 

<!---HONumber=62-->