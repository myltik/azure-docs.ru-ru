---
title: Настройка аналитики веб-приложения для ASP.NET с помощью Application Insights | Microsoft Docs
description: Настройка средств аналитики производительности, доступности и использования для веб-сайта ASP.NET, размещенного локально или в Azure.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: daviste

---
# Настройка Application Insights для ASP.NET
[Visual Studio Application Insights](app-insights-overview.md) выполняет мониторинг работы действующего приложения, благодаря чему вы можете [обнаруживать и диагностировать проблемы, связанные с производительностью и исключениями](app-insights-detect-triage-diagnose.md), а также [просматривать показатели использования приложения](app-insights-overview-usage.md). Эта услуга работает для приложений, размещенных на локальных серверах IIS или на облачных виртуальных машинах, а также для веб-приложений Azure.

## Перед началом работы
Вам необходимы:

* Visual Studio 2013 с обновлением 3 или более новая версия. Чем новее версия, тем лучше.
* подписка на [Microsoft Azure](http://azure.com). Если у вашей группы или организации есть подписка Azure, владелец может добавить вас в нее с помощью вашей [учетной записи Майкрософт](http://live.com).

Существуют и другие статьи, к которым можно обратиться, если вас интересуют такие темы, как:

* [инструментирование веб-приложения во время выполнения;](app-insights-monitor-performance-live-website-now.md)
* [облачные службы Azure.](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Добавление пакета SDK для Application Insights
### Добавление в новый проект
При создании нового проекта в Visual Studio установите флажок для компонента Application Insights.

![Создание проекта ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### ... или, если это существующий проект
В обозревателе решений щелкните проект правой кнопкой мыши и выберите пункт **Добавить телеметрию Application Insights** или **Настроить Application Insights**.

![Выберите пункт Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* Если же это проект ASP.NET Core, [выполните эти указания, чтобы исправить несколько строк кода](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs).

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

* Первая телеметрия отображается в [динамическом потоке метрик](app-insights-metrics-explorer.md#live-metrics-stream).
* Отдельные события появляются в области **поиска** (1). Для отображения данных может потребоваться несколько минут. Щелкните любое событие, чтобы просмотреть его свойства.
* Сводные показатели отображаются на диаграммах (2). Через пару минут данные появятся здесь. Щелкните любую диаграмму, чтобы открыть колонку с более подробной информацией.

[Дополнительные сведения об использовании Application Insights на портале Azure](app-insights-dashboards.md).

## 4\. Публикация приложения
Опубликуйте приложение на сервере IIS или в Azure. Просмотрите [динамический поток метрик](app-insights-metrics-explorer.md#live-metrics-stream), чтобы убедиться в бесперебойной работе приложения.

Вы увидите, как создается телеметрия на портале Application Insights, где можно отслеживать метрики, выполнять поиск данных телеметрии и настраивать [панели мониторинга](app-insights-dashboards.md). Можно также использовать эффективный [язык запросов аналитики](app-insights-analytics.md) для анализа использования и производительности или поиска определенных событий.

Кроме того, можно продолжить анализировать телеметрию в [Visual Studio](app-insights-visual-studio.md) с помощью таких средств, как поиск по журналу диагностики и [тренды](app-insights-visual-studio-trends.md).

> [!NOTE]
> Если приложение отправляет достаточно данных телеметрии для достижения [пределов регулирования](app-insights-pricing.md#limits-summary), включается автоматическая [выборка](app-insights-sampling.md). Выборка позволяет уменьшить количество данных телеметрии, отправляемых из приложения, сохраняя связанные данные в целях диагностики.
> 
> 

## <a name="land"></a> Что делает команда "Добавить Application Insights"
Application Insights отправляет данные телеметрии из приложения на портал Application Insights (размещенный в Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Эта команда выполняет три действия:

1. Добавляет в проект пакет NuGet с веб-пакетом SDK для Application Insights. Чтобы увидеть его в Visual Studio, щелкните правой кнопкой мыши проект и выберите пункт "Управление пакетами NuGet".
2. Создает ресурс Application Insights на [портале Azure](https://portal.azure.com/). Здесь будут отображаться ваши данные. Команда извлекает *ключ инструментирования*, идентифицирующий ресурс.
3. Вставляет ключ инструментирования в `ApplicationInsights.config`, чтобы пакет SDK мог отправлять данные телеметрии на портал.

При необходимости вы можете выполнить эти действия вручную для проекта [ASP.NET 4](app-insights-windows-services.md) или [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

### Обновление до будущих версий пакета SDK
Чтобы установить [новый выпуск пакета SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), еще раз откройте диспетчер пакетов NuGet и выполните фильтрацию по установленным пакетам. Выберите элемент Microsoft.ApplicationInsights.Web, а затем — элемент "Обновление".

Если были выполнены какие-либо настройки файла ApplicationInsights.config, то, прежде чем выполнять обновление, сохраните его копию, а затем объедините изменения в новой версии.

## Дальнейшие действия
|  |
| --- | --- |
| **[Работа с Application Insights в Visual Studio](app-insights-visual-studio.md)**<br/>Отладка с помощью телеметрии, поиск по журналу диагностики, детализация кода. |
| **[Работа с порталом Application Insights](app-insights-dashboards.md)**<br/>Панели мониторинга, эффективные средства диагностики и анализа, оповещения, карта динамических зависимостей приложения, а также экспорт данных телеметрии. |
| **[Добавление дополнительных данных](app-insights-asp-net-more.md)**<br/>Мониторинг использования, доступности, зависимостей и исключений. Интеграция трассировок из платформ ведения журналов. Написание пользовательской телеметрии. |

<!---HONumber=AcomDC_0907_2016-->