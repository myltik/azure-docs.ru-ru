.<properties
	pageTitle="Диагностика проблем производительности на работающем веб-сайте IIS | Microsoft Azure"
	description="Мониторинг производительности веб-сайта без необходимости его повторного развертывания. Для получения телеметрии зависимостей используйте автономно или с пакетом SDK для Application Insights."
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
	ms.date="08/24/2016"
	ms.author="awills"/>


# Инструментирование веб-приложений во время выполнения с помощью Application Insights

*Доступна только предварительная версия Application Insights.*

Действующее веб-приложение можно инструментировать с помощью Visual Studio Application Insights, не прибегая к изменению или повторному развертыванию кода. В приложениях, размещенных на локальном сервере IIS, можно установить монитор состояния. А если используются веб-приложения Azure или приложения, запущенные на виртуальной машине Azure, вы можете установить расширение Application Insights. (См. статьи об инструментировании [динамических веб-приложений J2EE](app-insights-java-live.md) и [облачных служб Azure](app-insights-cloudservices.md).)

![примеры диаграмм](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Вы можете выбрать один из трех указанных ниже вариантов применения Application Insights для веб-приложений .NET.

* **Во время сборки.** [Добавьте пакет SDK для Application Insights][greenbrown] в код вашего веб-приложения
* **Во время выполнения.** Инструментируйте веб-приложение на сервере, как описано ниже, без повторной сборки и развертывания кода.
* **Оба варианта.** Включите пакет SDK в код веб-приложения и примените расширения во время выполнения, чтобы воспользоваться преимуществами двух вариантов.

Ниже представлено общее сравнение предлагаемых возможностей.

||Во время сборки|Во время выполнения|
|---|---|---|
|Запросы и исключения|Да|Да|
|[Более подробные исключения](app-insights-asp-net-exceptions.md)||Да|
|[Диагностика зависимостей](app-insights-asp-net-dependencies.md)|На платформе .NET 4.6+|Да|
|[Счетчики производительности системы](app-insights-web-monitor-performance.md#system-performance-counters)||Облачная служба Azure или сервер IIS, но не веб-приложение Azure|
|[API для пользовательской телеметрии][api]|Да||
|[Интеграция журнала трассировки](app-insights-asp-net-trace-logs.md)|Да||
|[Просмотр страницы и пользовательские данные](app-insights-javascript.md)|Да||
|Повторная сборка кода не требуется|Нет||




## Инструментирование веб-приложения во время выполнения

Вам потребуется подписка [Microsoft Azure](http://azure.com).

### Если приложение размещено на сервере IIS

1. Войдите на веб-сервер IIS с учетными данными администратора.
2. Скачайте и запустите [установщик монитора состояний](http://go.microsoft.com/fwlink/?LinkId=506648).
4. В мастере установки выполните вход в Microsoft Azure.

    ![Войдите в Azure с данными учетной записи Майкрософт.](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *Возникли ошибки подключения? См. раздел [Устранение неполадок](#troubleshooting).*

5. Выберите установленное веб-приложение или веб-сайт для мониторинга и настройте ресурс, где вы будете просматривать результаты на портале Application Insights.

    ![Выберите приложение и ресурс.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Как правило, необходимо настроить новый ресурс и [группу ресурсов][roles].

    В противном случае используйте существующий ресурс, если для вашего сайта уже настроены [веб-тесты][availability] или [система мониторинга веб-клиентов][client].

6. Перезапустите IIS.

    ![Выберите "Перезапустить" в верхней части диалогового окна.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Работа вашей веб-службы будет ненадолго прервана.

6. Обратите внимание, что в веб-приложения, для которых планируется мониторинг, вставлен файл ApplicationInsights.config.

    ![Найдите файл .config и файлы кода веб-приложения.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Также некоторые изменения вносятся в файл web.config.

#### Необходимо выполнить настройку позднее?

После завершения работы мастера можно в любое время выполнить повторную настройку агента. Это можно использовать и в том случае, если агент был установлен, но на начальном этапе возникли какие-либо проблемы.

![Нажатие значка Application Insights в панели задач](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)


### Если приложение выполняется в качестве веб-приложения Azure

1. На [портале Azure](https://portal.azure.com) создайте ресурс Application Insights типа ASP.NET. Здесь будут храниться, анализироваться и отображаться данные телеметрии вашего приложения.

    ![Добавление Application Insights Выбор типа ASP.NET](./media/app-insights-monitor-performance-live-website-now/01-new.png)
     
2. Теперь на панели управления веб-приложения Azure откройте **Сервис > Мониторинг производительности** и добавьте расширение Application Insights.

    ![Последовательно выберите в веб-приложении "Средства", "Расширения", "Добавить" и "Application Insights"](./media/app-insights-monitor-performance-live-website-now/05-extend.png)

    Выберите только что созданный ресурс Application Insights.


### Если это проект облачной службы Azure

[Добавьте сценарии в веб-роли и рабочие роли](app-insights-cloudservices.md).


## Просмотр телеметрии производительности

Войдите на [портал Azure](https://portal.azure.com), перейдите в Application Insights и откройте созданный ресурс.

![Выберите "Обзор", Application Insights, а затем свое приложение.](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Откройте колонку "Производительность", чтобы отобразить сведения о запросе, времени отклика, зависимости и другие данные.

![Производительность](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Щелкните любую диаграмму, чтобы открыть подробное представление.

Вы можете [изменять, переупорядочивать и сохранять](app-insights-metrics-explorer.md) диаграммы и колонки, а также закреплять их на [панели мониторинга](app-insights-dashboards.md).

## Зависимости

На диаграмме длительности зависимостей отображается время, потраченное на выполнение вызовов вашего приложения к внешним компонентам, например базам данных, REST API или хранилищу больших двоичных объектов Azure.

Чтобы сегментировать выбранную диаграмму по вызовам к разным зависимостям, включите функцию группирования, а затем выберите вариант "Зависимость", "Тип зависимости" или "Производительность зависимости".

![Dependency](./media/app-insights-monitor-performance-live-website-now/23-dep.png)

## Счетчики производительности 

(Не предназначено для веб-приложений Azure.) В колонке обзора щелкните "Серверы", чтобы отобразить диаграммы счетчиков производительности серверов, например счетчиков использования ресурсов ЦП и памяти.

Если у вас есть несколько экземпляров сервера, можно изменить диаграммы для группировки по экземпляру роли.

![Серверы](./media/app-insights-monitor-performance-live-website-now/22-servers.png)

Кроме того, вы можете [изменить набор счетчиков производительности, данные которых передает пакет SDK](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3).


## Исключения

![Щелкните диаграмму исключений сервера.](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Вы можете подробно рассмотреть конкретные исключения (за последние семь дней) и получить данные трассировки стека и контекста.

## Выборка

Если ваше приложение выполняет отправку больших объемов данных, а вы используете Application Insights SDK для ASP.NET версии 2.0.0-beta3 или более поздней, функция адаптивной выборки может сработать и отправить только часть вашей телеметрии. [Дополнительная информация о выборке.](app-insights-sampling.md)


## Устранение неполадок

### Ошибки подключения

Чтобы монитор состояний мог работать, в брандмауэре сервера необходимо открыть [некоторые исходящие порты](app-insights-ip-addresses.md#outgoing-ports).

### Отсутствие данных телеметрии

  * С помощью своего сайта создайте данные.
  * Подождите несколько минут, пока поступят данные, а затем щелкните **Обновить**.
  * Чтобы просмотреть отдельные события, откройте поиск по журналу диагностики, щелкнув плитку "Поиск". События часто отображаются в поле поиска по журналу диагностики перед появлением статистических данных на диаграммах.
  * Откройте монитор состояний и на левой панели выберите свое приложение. Проверьте, не появились ли в разделе "Уведомления о конфигурации» сообщения с диагностическими сведениями для этого приложения:

  ![Откройте колонку "Производительность", чтобы отобразить сведения о запросе, времени отклика, зависимости и другие данные.](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Убедитесь, что брандмауэр сервера пропускает исходящий трафик через перечисленные выше порты.
  * Если на сервере отображается сообщение "Недостаточно разрешений", выполните следующее:
    * В диспетчере IIS выберите ваш пул приложений, откройте **Дополнительные параметры** и в разделе **Модель процесса** скопируйте значение параметра "Идентификация".
    * На панели управления компьютера добавьте это значение к группе пользователей монитора производительности.
  * Если на вашем сервере установлен MMA/SCOM, некоторые версии могут конфликтовать. Удалите SCOM и монитор состояний и повторно установите последние версии.
  * См. раздел [Устранение неполадок][qna].

## Требования к системе

Операционные системы, которые поддерживаются для монитора состояний Application Insights на сервере:

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2.

На них должны быть установлены последний пакет обновления и платформа .NET Framework 4.0 и 4.5.

На клиентских компьютерах должна быть установлена ОС Windows 7, 8 или 8.1 с платформой .NET Framework 4.0 и 4.5.

Поддерживаются такие версии IIS: 7, 7.5, 8, 8.5 (IIS – обязательный компонент).

## Автоматизация с помощью PowerShell

Мониторинг можно запускать и останавливать с помощью PowerShell.

Сначала импортируйте модуль Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Узнайте, какие приложения отслеживаются:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (необязательный параметр) — имя веб-приложения.
* Отображает состояние мониторинга Application Insights для каждого веб-приложения (или именованного приложения) на этом сервере IIS.

* Возвращает `ApplicationInsightsApplication` для каждого приложения.
 * `SdkState==EnabledAfterDeployment`: приложение отслеживается. Оно инструментировано во время выполнения с помощью монитора состояния или командлета `Start-ApplicationInsightsMonitoring`.
 * `SdkState==Disabled`: приложение не инструментировано для Application Insights. Оно либо никогда не было инструментировано, либо мониторинг во время выполнения был отключен с помощью монитора состояния или командлета `Stop-ApplicationInsightsMonitoring`.
 * `SdkState==EnabledByCodeInstrumentation`: приложение инструментировано путем добавления пакета SDK в исходный код. Этот пакет SDK нельзя обновить или остановить.
 * `SdkVersion` — отображает версию, которая используется для мониторинга этого приложения.
 * `LatestAvailableSdkVersion` — отображает версию, доступную сейчас в коллекции NuGet. Чтобы обновить приложение до этой версии, используйте командлет `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` — имя приложения на сервере IIS.
* `-InstrumentationKey` — ключ инструментирования ресурса Application Insights, в котором должны отображаться результаты.

* Этот командлет влияет только на приложения, которые еще не инструментированы, то есть SdkState==NotInstrumented.

    Командлет не влияет на приложение, которое уже было инструментировано путем добавления пакета SDK в код во время сборки или с использованием этого же командлета ранее во время выполнения.

    Версия пакета SDK для инструментирования приложения — это последняя версия, загруженная на этот сервер.

    Чтобы загрузить последнюю версию, используйте командлет Update-ApplicationInsightsVersion.

* В случае успешного выполнения возвращает `ApplicationInsightsApplication`. В случае сбоя трассировка записывается в stderr.

    
          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` — имя приложения на сервере IIS.
* `-All` — останавливает мониторинг всех приложений на этом сервере IIS, для которых `SdkState==EnabledAfterDeployment`.

* Останавливает мониторинг для указанных приложений и удаляет инструментирование. Применимо только для приложений, инструментированных во время выполнения с использованием монитора состояния или командлета Start-ApplicationInsightsApplication (`SdkState==EnabledAfterDeployment`).

* Возвращает ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name` — имя веб-приложения на сервере IIS.
* `-InstrumentationKey` (необязательный параметр). используется для изменения ресурса, в который отправляются данные телеметрии приложения.
* Этот командлет:
 * Обновляет именованное приложение до последней версии пакета SDK, загруженной на этот компьютер (работает, только если `SdkState==EnabledAfterDeployment`).
 * Если указан ключ инструментирования, именованное приложение повторно настраивается для отправки данных телеметрии в ресурс с этим ключом (работает, если `SdkState != Disabled`).

`Update-ApplicationInsightsVersion`

* Загружает последний пакет SDK для Application Insights на сервер.

## Шаблон Azure

Если веб-приложение работает в Azure и вы создаете ресурсы с помощью шаблона Azure Resource Manager, можно настроить Application Insights, добавив следующий код в узел ресурсов:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` — имя ресурса Application Insights.
* `myWebAppName` — идентификатор веб-приложения.

## <a name="next"></a>Дальнейшие действия

* [Создайте веб-тесты][availability], чтобы убедиться, что ваш сайт продолжает работать.
* [Поиск событий и журналов][diagnostic] для диагностики неполадок.
* [Добавьте телеметрию веб-клиента][usage], чтобы просматривать исключения в коде веб-страницы и вставлять вызовы трассировки.
* [Добавьте пакет SDK для Application Insights в код веб-службы][greenbrown], чтобы иметь возможность вставлять вызовы трассировки и журналов в серверный код.

## Видео

#### Мониторинг производительности

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=AcomDC_0831_2016-->