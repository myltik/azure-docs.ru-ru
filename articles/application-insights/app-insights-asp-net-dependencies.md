<properties 
	pageTitle="Отслеживание зависимостей в Application Insights" 
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
	ms.date="03/02/2016" 
	ms.author="awills"/>


# Настройка Application Insights: отслеживание зависимостей


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



*Зависимость* – это внешний компонент, который вызывается приложением. Как правило, это служба, вызываемая с использованием HTTP, база данных или файловая система. В Visual Studio Application Insights можно легко увидеть то, сколько времени приложение ожидает зависимости и как часто происходит сбой вызова зависимости.

![примеры диаграмм](./media/app-insights-asp-net-dependencies/10-intro.png)

Сейчас монитор зависимостей по умолчанию предоставляет отчеты о вызовах зависимостей таких типов:

* ASP.NET:
 * базы данных SQL;
 * веб-службы и службы WCF ASP.NET, использующие привязки на основе HTTP;
 * локальные или удаленные HTTP-вызовы;
 * Azure DocumentDB, очередь, таблица и хранилище больших двоичных объектов.
* Java:
 * вызовы к базе данных с помощью драйвера [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) (например, MySQL, SQL Server, PostgreSQL или SQLite).
* JavaScript на веб-страницах — [веб-страницы SDK](app-insights-javascript.md) автоматически регистрируют вызовы Ajax как зависимости.

Можно написать собственные вызовы пакета SDK для отслеживания других зависимостей с помощью [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


## Настройка мониторинга зависимостей

Вам потребуется подписка на [Microsoft Azure](http://azure.com).

### Если приложение выполняется на сервере IIS

Если веб-приложение использует .NET 4.6 или более позднюю версию, то вы автоматически получите отслеживание зависимостей, если в приложении [установлен пакет SDK для Application Insights](app-insights-asp-net.md). Больше делать ничего не нужно.

В противном случае установите монитор состояний Application Insights на сервер.

1. Войдите на веб-сервер IIS с учетными данными администратора.
2. Скачайте и запустите [установщик монитора состояний](http://go.microsoft.com/fwlink/?LinkId=506648).
4. В мастере установки выполните вход в Microsoft Azure.

    ![Войдите в Azure с данными учетной записи Майкрософт.](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *Возникли ошибки подключения? См. раздел [Устранение неполадок](#troubleshooting).*

5. Выберите установленное веб-приложение или веб-сайт для мониторинга и настройте ресурс, где вы будете просматривать результаты на портале Application Insights.

    ![Выберите приложение и ресурс.](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    Как правило, необходимо настроить новый ресурс и [группу ресурсов][roles].

    В противном случае используйте существующий ресурс, если для вашего сайта уже настроены [веб-тесты][availability] или [система мониторинга веб-клиентов][client].

6. Перезапустите IIS.

    ![Выберите "Перезапустить" в верхней части диалогового окна.](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    Работа вашей веб-службы будет ненадолго прервана.

6. Обратите внимание, что в веб-приложения, для которых планируется мониторинг, вставлен файл ApplicationInsights.config.

    ![Найдите файл .config и файлы кода веб-приложения.](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   Также некоторые изменения вносятся в файл web.config.

#### Необходимо выполнить настройку позднее?

После завершения работы мастера можно в любое время выполнить повторную настройку агента. Это можно использовать и в том случае, если агент был установлен, но на начальном этапе возникли какие-либо проблемы.

![Нажатие значка Application Insights в панели задач](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### Если приложение выполняется в качестве веб-приложения Azure

На панели управления веб-приложения Azure добавьте расширение Application Insights.

![В веб-приложении последовательно выберите пункты "Настройки", "Расширения", "Добавить" и "Application Insights".](./media/app-insights-asp-net-dependencies/05-extend.png)


### Если это проект облачной службы Azure

[Добавьте сценарии в веб-роли и рабочие роли](app-insights-cloudservices.md#dependencies). Или [установите платформу .NET Framework 4.6 или более позднюю версию](../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="diagnosis"></a> Диагностика проблем с производительностью зависимостей

Чтобы оценить производительность запросов сервера:

![На странице «Обзор» приложения в Application Insights щелкните плитку «Производительность».](./media/app-insights-asp-net-dependencies/01-performance.png)

прокрутите вниз, чтобы увидеть сетку запросов.

![Список запросов со средними значениями и их количеством](./media/app-insights-asp-net-dependencies/02-reqs.png)

Получение ответа на верхний запрос заняло слишком много времени. Стоит попытаться выяснить, на что потрачено время.

Щелкните эту строку, чтобы просмотреть события для отдельного запроса.


![Список вхождений запросов](./media/app-insights-asp-net-dependencies/03-instances.png)

Щелкните любой длительный экземпляр, чтобы проверить его более детально.

> [AZURE.NOTE] Немного прокрутите вниз, чтобы выбрать экземпляр. Задержка в конвейере может означать неполноту данных верхних экземпляров.

Прокрутите вниз до вызовов удаленных зависимостей, связанных с этим запросом:

![Найдите вызовы удаленных зависимостей и определите необычную продолжительность.](./media/app-insights-asp-net-dependencies/04-dependencies.png)

По-видимому, большую часть времени обработки этого запроса занял вызов локальной службы.

Выберите эту строку, чтобы получить дополнительную информацию.


![Щелкните эту удаленную зависимость, чтобы определить причину.](./media/app-insights-asp-net-dependencies/05-detail.png)

Подробности включают в себя достаточно данных для диагностики проблемы.



## Сбои

При наличии неудачных запросов щелкните диаграмму.

![Щелкните диаграмму неудачных запросов.](./media/app-insights-asp-net-dependencies/06-fail.png)

Щелкните тип и экземпляр запроса, чтобы найти удаленную зависимость, вызов которой не удалось выполнить.


![Щелкните тип запроса, а затем его экземпляр, чтобы открыть этот же экземпляр в другом представлении. Щелкните его еще раз, чтобы просмотреть подробную информацию об исключении.](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## Пользовательское отслеживание зависимостей

Стандартный модуль отслеживания зависимостей выявляет внешние зависимости, например базы данных и API REST, автоматически. Однако при необходимости аналогичную обработку можно настроить и для других компонентов.

Код, который отправляет сведения о зависимостях, можно написать с использованием того же интерфейса [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency), что используется в стандартных модулях.

Например, формируя код на основе готовой сборки, можно назначить время для всех вызовов этого кода и таким образом выяснить, как он влияет на время отклика вашей системы. Чтобы эти данные отображались в диаграммах зависимостей в Application Insights, используйте для их отправки командлет `TrackDependency`.

```C#

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Чтобы отключить стандартный модуль отслеживания зависимостей, удалите ссылку на DependencyTrackingTelemetryModule в файле [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## Дальнейшие действия

- [Исключения](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Данные пользователей и страниц](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Доступность](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md

 

<!---HONumber=AcomDC_0309_2016-->