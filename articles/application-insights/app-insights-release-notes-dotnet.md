<properties 
	pageTitle="Заметки о выпуске Application Insights для .NET" 
	description="Последние обновления для пакета SDK .NET." 
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
	ms.date="08/06/2015" 
	ms.author="sergkanz"/>
 
# Заметки о выпуске пакета SDK Application Insights для .NET

[Пакет SDK Application Insights для .NET](app-insights-start-monitoring-app-health-usage.md) отправляет телеметрию о вашем работающем приложении службе [Application Insights](http://azure.microsoft.com/services/application-insights/), в которой вы можете проанализировать использование и производительность приложения.


#### Установка пакета SDK в приложении

См. статью [Приступая к работе со службой Application Insights для .NET](app-insights-start-monitoring-app-health-usage.md).

#### Обновление до последнего пакета SDK 

* Выполнив обновление, снова объедините настройки, сделанные для файла ApplicationInsights.config. Если вы не уверены, что настраивали его, создайте другой проект, добавьте в него службу Application Insights и сравните свой файл конфигурации с тем, который находится в новом проекте. Запишите все отличия.
* В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите элемент **Управление пакетами NuGet**.
* Настройте фильтр, чтобы отображались установленные пакеты. 
* Выберите элемент **Microsoft.ApplicationInsights.Web**, а затем — элемент **Обновление**. (В результате будут обновлены также все зависимые пакеты).
* Сравните файл ApplicationInsights.config со старой копией. Большинство изменений, которые отобразятся, обусловлены тем, что одни модули были удалены, а другие теперь подлежат параметризации. Возобновите использование настроек, выполненных в старом файле.
* Перестройте свое решение.

## Версия 2.0.0-beta1
- TrackDependency будет выдавать правильные данные JSON, если были указаны не все обязательные поля.
- Избыточные свойство ```RequestTelemetry.ID``` теперь является просто прокси-объектом для ```RequestTelemetry.Operation.Id```.
- Новый интерфейс ```ISupportSampling``` и его явная реализация для большинства типов элементов данных.
- Свойство ```Count``` в DependencyTelemetry помечено как устаревшее. Вместо него используйте ```SamplingPercentage```.
- Представлен новый ```CloudContext```, в который перемещены свойства ```RoleName``` и ```RoleInstance``` из ```DeviceContext```.
- Новое свойство ```AuthenticatedUserId``` для ```UserContext```, позволяющее указать удостоверение аутентифицированного пользователя.
- Добавлен `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer`, `Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer`, который инициализирует контекст аутентифицированного пользователя согласно настройкам пакета SDK для JavaScript.
- Добавлен `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` и поддержка фиксированной частоты выборки в качестве его реализации.
- Добавлен `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder`, который позволяет создавать `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` с набором `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor`.

## Версия 1.2

- Инициализаторы телеметрии, не имеющие зависимостей от библиотек ASP.NET, были перемещены из `Microsoft.ApplicationInsights.Web` в новый пакет NuGet зависимостей `Microsoft.ApplicationInsights.WindowsServer`.
- Библиотека `Microsoft.ApplicationInsights.Web.dll` переименована в `Microsoft.AI.Web.dll`.
- Пакет NuGet `Microsoft.ApplicationInsights.Web.TelemetryChannel` переименован в `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`. Сборка `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` переименована в `Microsoft.AI.ServerTelemetryChannel.dll`. Класс `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` переименован в `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`.
- Из всех пространств имен, которые являются частью пакета Web SDK, исключена часть `Extensibility`. Это касается всех инициализаторов телеметрии в файле ApplicationInsights.config и модуля `ApplicationInsightsWebTracking` в файле web.config.
- Зависимости, собранные с помощью агента инструментирования для среды выполнения (обеспечивается монитором состояния или расширением Azure WebSite), не помечаются как асинхронные, если в потоке нет HttpContext.Current.
- Свойство `SamplingRatio` модуля `DependencyTrackingTelemetryModule` не выполняет никаких действий и помечено как устаревшее.
- Сборка `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` переименована в `Microsoft.AI.PerfCounterCollector`.
- Исправлены некоторые незначительные ошибки в Web SDK и Devices SDK.


## Версия 1.1

- Добавлен новый тип телеметрии `DependencyTelemetry`, который можно использовать для отправки сведений о вызовах зависимостей из приложения (таких как вызовы SQL, HTTP и т. д).
- Добавлен новый метод перегрузки `TelemetryClient.TrackDependency`, позволяющий отправлять информацию о вызовах зависимостей.
- Решена проблема исключения NullReferenceException, которое выдавалось модулем диагностики при использовании функции TelemetryConfiguration.CreateDefault.

## Версия 1.0

- Инициализаторы и модули телеметрии перемещены из отдельных вложенных пространств имен в корневое пространство имен `Microsoft.ApplicationInsights.Extensibility.Web`.
- Из имен инициализаторов и модулей телеметрии удален префикс Web, так как он уже присутствует в имени пространства имен `Microsoft.ApplicationInsights.Extensibility.Web`.
- `DeviceContextInitializer` перемещен из сборки `Microsoft.ApplicationInsights` в сборку `Microsoft.ApplicationInsights.Extensibility.Web` и преобразован в `ITelemetryInitializer`.
- Имена пространств имен и сборок изменены с `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` на `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` для согласования с именем пакета NuGet.
- `RemoteDependencyModule` переименован в `DependencyTrackingTelemetryModule`.
- `CustomPerformanceCounterCollectionRequest` переименован в `PerformanceCounterCollectionRequest`.

## Версия 0.17
- Удалена зависимость EventSource NuGet для приложений Framework 4.5.
- На стороне сервера больше не будут создаваться файлы cookie сеанса и анонимный пользователь. Для отслеживания пользователей и сеансов веб-приложений теперь требуется инструментирование с помощью пакета SDK JavaScript — входящие в этот пакет файлы cookie по-прежнему учитываются. Модули телеметрии ```WebSessionTrackingTelemetryModule``` и ```WebUserTrackingTelemetryModule``` больше не поддерживаются и были удалены из файла ApplicationInsights.config. Обратите внимание, что это изменение может привести к значительным изменениям в количестве пользователей и сеансов, так как теперь учитываются только сеансы, запущенные пользователями.
- OSVersion больше не заполняется пакетом SDK по умолчанию. Если значение не указано, OS и OSVersion вычисляются конвейером Application Insights в зависимости от агента пользователя. 
- Канал сохраняемости, оптимизированный для сценариев высокой нагрузки, используется для веб-пакета SDK. Исправлена проблема «Спираль смерти». Название «Спираль смерти» обозначает такую ситуацию: резкий скачок в количестве элементов телеметрии, которое из-за этого значительно превышает пороговое значение регулирования на конечной точке, приводит к тому, что через некоторое время выполняется повторная попытка, во время которой снова выполняется регулирование.
- Режим разработчика оптимизирован для производства. Если по ошибке проигнорировать его, то при попытке вывести дополнительную информацию это не приведет к появлению такого большого количества служебных данных, как раньше.
- По умолчанию режим разработчика включается только тогда, когда приложение обрабатывается отладчиком. Вы можете переопределить это поведение с помощью свойства ```DeveloperMode``` интерфейса ```ITelemetryChannel```.

## Версия 0.16 

28\.04.2015 (предварительная)

- Теперь пакет SDK включает поддержку целевой платформы DNX для мониторинга приложений на [платформе .NET Core](http://www.dotnetfoundation.org/NETCore5).
- Экземпляры ```TelemetryClient``` больше не кэшируют ключ инструментирования. Теперь, если ключ инструментирования не задан в ```TelemetryClient``` явным образом, ```InstrumentationKey``` вернет значение null. Устраняет проблему при задании ```TelemetryConfiguration.Active.InstrumentationKey``` после сбора данных телеметрии. Модули телеметрии, такие как сборщик зависимостей, коллекция данных веб-запросов и сборщик счетчиков производительности будут использовать новый ключ инструментирования.

## Версия 0.15

- Новое свойство ```Operation.SyntheticSource``` теперь доступно в ```TelemetryContext```. Теперь можно отметить элементы телеметрии, такие как "трафик нереального пользователя", и указать, как был создан этот трафик. Например, установив это свойство, можно легко отличить трафик автоматизации тестирования от трафика нагрузочного теста.
- Логика канала перемещена в отдельный NuGet, который называется Microsoft.ApplicationInsights.PersistenceChannel. Канал по умолчанию теперь называется InMemoryChannel
- Новый метод ```TelemetryClient.Flush``` позволяет синхронно очищать элементы телеметрии из буфера

## Версия 0.13

Для более старых версий заметки о выпуске не доступны.

 

<!---HONumber=Oct15_HO4-->