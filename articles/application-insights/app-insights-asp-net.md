<properties 
	pageTitle="Application Insights для ASP.NET" 
	description="Выполните анализ производительности, доступности и использования локального приложения или веб-приложения Microsoft Azure с помощью Application Insights." 
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
	ms.date="01/26/2016" 
	ms.author="awills"/>


# Настройка Application Insights для ASP.NET


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Пакет SDK Application Insights отправляет данные телеметрии из работающего веб-приложения на портал Azure, на который вы можете войти и просмотреть диаграммы производительности и использования приложения.

![Пример диаграмм мониторинга производительности](./media/app-insights-asp-net/10-perf.png)

Вы также сможете исследовать и сопоставлять определенные запросы, исключения и события в журнале. API можно использовать для добавления данных телеметрии, чтобы детально отслеживать производительность и использование.

#### Перед началом работы

Вам необходимы:

* подписка на [Microsoft Azure](http://azure.com). Если у вашей группы или организации есть подписка Azure, владелец может добавить вас в нее с помощью вашей [учетной записи Майкрософт](http://live.com).
* Visual Studio 2013 с обновлением 3 или более новая версия.

## <a name="ide"></a> Добавление Application Insights в проект в Visual Studio

#### Добавление в новый проект

При создании нового проекта в Visual Studio установите флажок для компонента Application Insights.


![Создание проекта ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

Выберите учетную запись с поддержкой входа в Azure. Вам может быть предложено повторно ввести учетные данные. (Или, если вы не входите, будет добавлен код пакета SDK, который можно настроить позже.)


#### ... или, если это существующий проект

В обозревателе решений щелкните проект правой кнопкой мыши и выберите **Добавить Application Insights** или **Настроить Application Insights**.

![Выберите пункт Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)


#### Параметры настройки

Если вы делаете это впервые, вам будет предложено войти в предварительную версию Microsoft Azure или зарегистрироваться в ней.

Если это приложение является частью большего приложения, можно использовать элемент **Настройка параметров**, чтобы поместить его в ту же группу ресурсов, где находятся другие компоненты.


####<a name="land"></a> Что делает команда "Добавить Application Insights"

Команда выполняет следующие действия (при необходимости их можно [выполнить вручную](app-insights-start-monitoring-app-health-usage.md)):

1. Добавляет в проект пакет NuGet с веб-пакетом SDK для Application Insights. Чтобы увидеть его в Visual Studio, щелкните правой кнопкой мыши проект и выберите пункт "Управление пакетами NuGet".
2. Создает ресурс Application Insights на [портале Azure][portal]. Здесь будут отображаться ваши данные. Команда извлекает *ключ инструментирования*, идентифицирующий ресурс.
3. Вставляет ключ инструментирования в `ApplicationInsights.config`, чтобы пакет SDK мог отправлять данные телеметрии на портал.

Если изначально вы не вошли в Azure, пакет SDK будет установлен без подключения к ресурсу. Вы сможете просматривать и искать данные телеметрии Application Insights в журнале диагностики Visual Studio во время отладки. Другие действия можно будет выполнить позже.

## Запуск проекта

Запустите приложение, нажав клавишу F5, и попробуйте открывать разные страницы, чтобы создать некоторый объем данных телеметрии.

В Visual Studio вы увидите число отправленных событий.

![В Visual Studio кнопка Application Insights доступна во время отладки.](./media/app-insights-asp-net/appinsights-09eventcount.png)

Нажмите эту кнопку, чтобы начать поиск по журналу диагностики.


### Поиск по журналу диагностики

В окне поиска отображаются события, которые были отправлены на портал Application Insights. (Аналогичная функция поиска есть и на портале.)

![Щелкните проект правой кнопкой мыши и последовательно выберите пункты "Application Insights" и "Поиск".](./media/app-insights-asp-net/34.png)

Бесплатный полнотекстовый поиск охватывает все поля в журнале событий. Например, можно выполнить поиск по фрагменту URL-адреса страницы, а также по значению свойства (например, город клиента) или ключевым словам в журнале трассировки.


[Дополнительная информация о поиске](app-insights-diagnostic-search.md)

### Исключения

Если вы [настроили отслеживание исключений](app-insights-asp-net-exception-mvc.md), отчеты об исключениях будут отображаться в окне поиска.

Щелкните исключение, чтобы просмотреть трассировку стека. Если код приложения открыт в среде Visual Studio, можно щелкнуть трассировку стека в соответствующей строке кода.



## <a name="monitor"></a> Открытие Application Insights

Откройте ресурс Application Insights на [портале Azure][portal].

![Щелкните проект правой кнопкой мыши и откройте портал Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### Метрики: объединенные данные

Выполните поиск данных в диаграммах "Обзор". Сначала вы увидите только одну или две точки. Например:

![Щелкните плитки, чтобы увидеть больше данных](./media/app-insights-asp-net/12-first-perf.png)

Щелкните любую диаграмму, чтобы увидеть более подробные метрики. [Дополнительные сведения о метриках.][perf]

* *Нет данных пользователя или страницы?* — [Добавление данных пользователя и страницы](app-insights-web-track-usage.md)

### Поиск: отдельные события

Откройте область поиска, чтобы исследовать отдельные запросы и связанные с ними события.

![В колонке поиска найдите имена страниц или другие свойства.](./media/app-insights-asp-net/21-search.png)

[Дополнительная информация о поиске](app-insights-diagnostic-search.md)

* *Нет связанных событий?* Настройте [исключения сервера](app-insights-asp-net-exception-mvc.md) и [зависимости](app-insights-asp-net-dependencies.md).


## Данные отсутствуют?

* В Visual Studio убедитесь, что ваше приложение отправляет данные телеметрии. Трассировки должны отображаться в окне вывода и концентраторе диагностики.
* Убедитесь, что вы проверяете данные в нужных разделах интерфейса Azure. Войдите на [портал Azure](https://portal.azure.com), щелкните «Обзор > Application Insights» и выберите приложение.
* Используйте приложение, открывая различные страницы, чтобы создать некоторый объем данных телеметрии.
* Откройте колонку [Поиск][diagnostic], чтобы просмотреть отдельные события. Иногда для прохождения событий через конвейер метрики требуется чуть больше времени.
* Подождите несколько секунд и нажмите "Обновить".
* См. раздел [Устранение неполадок][qna].



## Публикация приложения

Теперь разверните свое приложение и наблюдайте за тем, как накапливаются данные.

При работе в режиме отладки телеметрия передается через конвейер, поэтому данные должны появиться в течение нескольких секунд. При развертывании приложения данные накапливаются медленнее.

#### Проблемы на сервере сборки?

См. [этот элемент устранения неполадок](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [AZURE.NOTE] Если приложение генерирует много телеметрических данных (а вы используете пакет SDK ASP.NET версии 2.0.0-beta3 или выше), модуль адаптивной выборки автоматически сокращает объем отправляемых на портал данных, пересылая только репрезентативную часть событий. При этом связанные с тем же запросом события отбираются как группа, что позволяет перемещаться между связанными событиями. [Дополнительная информация о выборке.](app-insights-sampling.md)


## Отладка телеметрии

### Концентратор диагностики

Концентратор диагностики (в Visual Studio 2015 или более поздней версии) отображает данные телеметрии сервера Application Insights при их создании. Это средство работает, даже если вы установили пакет SDK без подключения к ресурсу на портале Azure.

![Откройте окно средств диагностики и проверьте события Application Insights.](./media/app-insights-asp-net/31.png)

Это особенно полезно, если у вас есть данные [пользовательской телеметрии](app-insights-api-custom-events-metrics.md), которые необходимо отладить перед отправкой на портал.

* *Сначала мы полностью настроили Application Insights для отправки данных телеметрии на портал. Но теперь нужно, чтобы данные телеметрии отображались только в Visual Studio.*

    Закомментируйте строку `<instrumentationkey>...` в файле ApplicationInsights.config. Когда данные телеметрии будут готовы к отправке на портал, раскомментируйте ее.



## Дальнейшие действия

- [Данные пользователей и страниц](../article/application-insights/app-insights-javascript.md#selector1)
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

 

<!---HONumber=AcomDC_0211_2016-->