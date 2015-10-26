<properties 
	pageTitle="Application Insights для ASP.NET" 
	description="Анализ использования, доступности и производительности локального приложения или веб-приложения Microsoft Azure с помощью Application Insights." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/13/2015" 
	ms.author="awills"/>


# Настройка Application Insights для ASP.NET

*Application Insights находится в состоянии предварительной версии.*

<a name="selector1"></a>

[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) осуществляет наблюдение за действующим приложением, помогая [обнаруживать и диагностировать проблемы производительности и исключения][detect], а также [исследовать, как используется ваше приложение][knowUsers]. Для этого в приложение устанавливается пакет SDK. Пакет SDK отправляет данные телеметрии о приложении в службу Application Insights, где вы анализируете и визуализируете поведение своего приложения.


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Добавьте пакет SDK в свое приложения в Visual Studio, и вы получите диаграммы запросов сервера, времени отклика и сбоев.

![Пример диаграмм мониторинга производительности](./media/app-insights-asp-net/10-perf.png)

Кроме того, для подробного наблюдения за использованием можно будет использовать API.

#### Перед началом работы

Вам необходимы:

* подписка на [Microsoft Azure](http://azure.com). Если у вашей группы или организации есть подписка Azure, владелец может добавить вас в нее с помощью вашей [учетной записи Майкрософт](http://live.com).
* Visual Studio 2013 с обновлением 3 или более новая версия.

## <a name="ide"></a> Добавление Application Insights в проект в Visual Studio

#### Добавление в новый проект

При создании нового проекта в Visual Studio установите флажок для компонента Application Insights.


![Создание проекта ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### ... или, если это существующий проект

В обозревателе решений щелкните проект правой кнопкой мыши и выберите пункт Add Application Insights (Добавить Application Insights).

![Выберите пункт Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### Параметры настройки

Если вы делаете это впервые, вам будет предложено войти в Microsoft Azure или зарегистрироваться в ней.

Если это приложение является частью большего приложения, можно использовать элемент **Настройка параметров**, чтобы поместить его в ту же группу ресурсов, где находятся другие компоненты.


####<a name="land"></a> Что делает команда «Добавить Application Insights»?

Команда выполняет следующие действия (при необходимости их можно [выполнить вручную](app-insights-start-monitoring-app-health-usage.md)):

* Создает ресурс Application Insights на [портале Azure][portal]. Здесь будут отображаться ваши данные. Команда извлекает *ключ инструментирования*, который идентифицирует ресурс.
* Добавляет в проект пакет NuGet с веб-пакетом SDK для Application Insights. Чтобы увидеть его в Visual Studio, щелкните правой кнопкой мыши проект и выберите пункт "Управление пакетами NuGet".
* Добавляет ключ инструментирования в `ApplicationInsights.config`.


## Запуск проекта

Запустите приложение, нажав клавишу F5, и попробуйте открывать разные страницы, чтобы создать некоторый объем данных телеметрии.

В Visual Studio вы увидите число отправленных событий.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Открытие Application Insights

Откройте ресурс Application Insights на [портале Azure][portal].

![Щелкните проект правой кнопкой мыши и откройте портал Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### Метрики: объединенные данные

Выполните поиск данных в диаграммах "Обзор". Сначала вы увидите только одну или две точки. Например:

![Щелкните плитки, чтобы увидеть больше данных](./media/app-insights-asp-net/12-first-perf.png)

Щелкните любую диаграмму, чтобы увидеть более подробные метрики. [Дополнительные сведения о метриках.][perf]

* *Нет данных пользователя или страницы?* — [Добавление данных пользователя и страницы](../article/application-insights/app-insights-asp-net-client.md)

### Поиск: отдельные события

Откройте область поиска, чтобы исследовать отдельные запросы и связанные с ними события.

![](./media/app-insights-asp-net/21-search.png)

[Дополнительная информация о поиске](app-insights-diagnostic-search.md)

* *Нет связанных событий?* Настройте [исключения сервера](../article/application-insights/app-insights-asp-net-exception-mvc.md) и [зависимости](../article/application-insights/app-insights-asp-net-dependencies.md).

### Данные отсутствуют?

* Убедитесь, что вы смотрите именно туда, куда необходимо. Войдите на [портал Azure](https://portal.azure.com), щелкните «Обзор > Application Insights» и выберите приложение.
* Используйте приложение, открывая различные страницы, чтобы создать некоторый объем данных телеметрии.
* Откройте колонку [Поиск][diagnostic], чтобы просмотреть отдельные события. Иногда для прохождения событий через конвейер метрики требуется чуть больше времени.
* Подождите несколько секунд и нажмите "Обновить".
* См. раздел [Устранение неполадок][qna].


## Публикация приложения

Теперь разверните свое приложение и наблюдайте за тем, как накапливаются данные.

При работе в режиме отладки телеметрия передается через конвейер, поэтому данные должны появиться в течение нескольких секунд. При развертывании приложения данные накапливаются медленнее.

#### Проблемы на сервере сборки?

См. [этот элемент устранения неполадок](app-insights-troubleshoot-faq.md#NuGetBuild).

## Дальнейшие действия

- [Данные пользователей и страниц](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Исключения](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Зависимости](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Доступность](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





## Обновление до будущих версий пакета SDK

Чтобы выполнить обновление до [нового выпуска пакета SDK](app-insights-release-notes-dotnet.md), еще раз откройте диспетчер пакетов NuGet и выполните фильтрацию по установленным пакетам. Выберите элемент Microsoft.ApplicationInsights.Web, а затем — элемент "Обновление".

Если были выполнены какие-либо настройки файла ApplicationInsights.config, то, прежде чем выполнять обновление, сохраните его копию, а затем объедините изменения в новой версии.



## <a name="video"></a>Видео

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO3-->