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
	ms.date="01/12/2016" 
	ms.author="abaranch"/>
 
# Заметки о выпуске пакета SDK Application Insights для .NET

[Пакет SDK Application Insights для .NET](app-insights-asp-net.md) отправляет телеметрию о вашем работающем приложении службе [Application Insights](https://azure.microsoft.com/services/application-insights/), в которой вы можете проанализировать использование и производительность приложения.


#### Установка пакета SDK в приложении

См. статью [Приступая к работе со службой Application Insights для .NET](app-insights-asp-net.md).

#### Обновление до последнего пакета SDK 

* Выполнив обновление, снова объедините настройки, сделанные для файла ApplicationInsights.config. Если вы не уверены, что настраивали его, создайте другой проект, добавьте в него службу Application Insights и сравните свой файл конфигурации с тем, который находится в новом проекте. Запишите все отличия.
* В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите элемент **Управление пакетами NuGet**.
* Настройте фильтр, чтобы отображались установленные пакеты. 
* Выберите элемент **Microsoft.ApplicationInsights.Web**, а затем — элемент **Обновление**. (В результате будут обновлены также все зависимые пакеты).
* Сравните файл ApplicationInsights.config со старой копией. Большинство изменений, которые отобразятся, обусловлены тем, что одни модули были удалены, а другие теперь подлежат параметризации. Возобновите использование настроек, выполненных в старом файле.
* Перестройте свое решение.

## Версия 2.0.0-beta4

- Методы расширения UseAdaptiveSampling и UseSampling были перемещены в Microsoft.ApplicationInsights.Extensibility
- Прекращена поддержка универсальных приложений для Windows Phone и магазина приложений Windows
- В ```DependencyTelemetry``` добавлены новые свойства ```ResultCode``` и ```Id```. ```ResultCode``` будет использоваться для предоставления кода ответа HTTP для зависимостей HTTP и кода ошибок для зависимостей SQL. ```Id``` будет использоваться для корреляции между компонентами. 
- Если ```ServerTelemetryChannel``` инициализируется программно, теперь необходимо вызывать метод ```ServerTelemetryChannel.Initialize()```. В противном случае постоянное хранилище не будет инициализировано (что приведет к тому, что при невозможности отправить данные телеметрии в случае временных неполадок они будут утеряны).
- ```ServerTelemetryChannel``` имеет новое свойство ```StorageFolder```, которое можно задать в коде или в конфигурации. Если это свойство установлено, ApplicationInsights будет использовать указанное расположение для хранения данных телеметрии, которые не были отправлены в случае временных неполадок. Если свойство не задано или указанный каталог недоступен, ApplicationInsights, как и прежде, попытается использовать каталоги LocalAppData и Temp.
- Метод расширения ```TelemetryConfiguration.GetTelemetryProcessorChainBuilder``` удален. Вместо этого метода используйте метод экземпляра ```TelemetryConfiguration.TelemetryProcessorChainBuilder```.
- Класс ```TelemetryConfiguration``` имеет новое свойство ```TelemetryProcessors```, которое обеспечивает доступ только для чтения к коллекции ```TelemetryProcessors```.
- ```Use```, ```UseSampling``` и ```UseAdaptiveSampling``` сохраняют ```TelemetryProcessors```, загруженное из конфигурации.
- По умолчанию в файле конфигурации указаны два обработчика телеметрии — обработчик телеметрии фильтра пользовательского агента и обработчик телеметрии обработчика запросов. Их поведение можно настроить. Можно добавить строку агента пользователя, которую необходимо отфильтровать в файле AI.config. По умолчанию мы отфильтровываем строку агента пользователя ```AllwaysOn```. На данный момент строки в файле конфигурации сравниваются со строками агента пользователя с помощью полного сопоставления без учета регистра. Также можно настроить список обработчиков, для которых требуется фильтрация запросов. 
- Версия зависимого пакета NuGet Microsoft.ApplicationInsights.Agent.Intercept была обновлена до 1.2.1. Она содержит исправления ошибок коллекции зависимостей SQL.

## Версия 2.0.0-beta3

- [Адаптивная выборка](app-insights-sampling.md) включена по умолчанию в канале телеметрии сервера. 
- Исправлена подпись ```UseSampling```, чтобы разрешить объединение в цепочки с другими вызовами для использования ```Use``` с обработчиками данных телеметрии.  
- Свойство ```Request.ID``` возвращается обратно. ```OperationContext``` теперь имеет свойство ```ParentId``` для сквозной корреляции.
- ```TimestampTelemetryInitializer``` удален. Метка времени будет добавляться ```TelemetryClient``` автоматически.
- ```OperationCorrelationTelemetryInitializer``` добавляется по умолчанию, чтобы включить корреляцию операций.
- ```OperationCorrelationTelemetryInitializer``` используется вместо ```OperationIdTelemetryInitializer```.
- По умолчанию сбор данных для агента пользователя не выполняется. Инициализатор телеметрии агента пользователя был удален.
- Данные поля ```DependencyTelemetry.Async``` не будут собираться модулем телеметрии сборщика зависимостей. 
- Запросы статического содержимого и диагностики не будут собираться модулем телеметрии запросов. Используйте ```HandlersToFilter``` из коллекции ```RequestTrackingTelemetryModule``` для фильтрации запросов, создаваемых определенными обработчиками HTTP-данных. 
- Автоматически созданная телеметрия запроса доступна посредством метода расширения HttpContext: System.Web.HttpContextExtension.GetRequestTelemetry.  


## Версия 2.0.0-beta2
- Добавлена поддержка для ITelemetryProcessor и возможность настройки с помощью кода или конфигурации. [Включает настраиваемую фильтрацию в пакете SDK](app-insights-api-filtering-sampling/#filtering)
- Удалены инициализаторы контекста. Вместо них следует использовать [инициализаторы телеметрии](https://azure.microsoft.com/documentation/articles/app-insights-api-filtering-sampling/#filtering).
- Обновлена служба Application Insights для .NET Framework 4.6. 
- Имена пользовательских событий теперь могут содержать до 512 символов.
- Свойство ```OperationContext.Name``` переименовано в ```RootName```.
- Свойство ```RequestTelemetry.Id``` удалено.
- Свойства ```Id``` и ```Context.Operation.Id``` RequestTelemetry не инициализируются при создании нового объекта RequestTelemetry.
- Свойство ```RequestTelemetry.Name``` больше не инициализируется. Вместо него будет использоваться ```RequestTelemetry.Context.Operation.Name```.
- При мониторинге запроса код ответа 401 является частью обычной проверки подлинности и приведет к успешному выполнению запроса.
- Исправлена блокировка потока пользовательского интерфейса при инициализации InMemoryChannel (канал по умолчанию) из потока пользовательского интерфейса. Это устраняет проблемы с зависанием пользовательского интерфейса в приложениях WPF.
 
## Версия 2.0.0-beta1
- TrackDependency будет выдавать правильные данные JSON, если были указаны не все обязательные поля.
- Избыточное свойство ```RequestTelemetry.ID``` теперь является просто прокси-объектом для ```RequestTelemetry.Operation.Id```.
- Новый интерфейс ```ISupportSampling``` и его явная реализация для большинства типов элементов данных.
- Свойство ```Count``` в классе DependencyTelemetry помечено как устаревшее. Вместо него используйте ```SamplingPercentage```.
- Представлен новый класс ```CloudContext```, в который перемещены свойства ```RoleName``` и ```RoleInstance``` из класса ```DeviceContext```.
- Добавлено свойство ```AuthenticatedUserId``` для класса ```UserContext```, позволяющее указать удостоверение прошедшего проверку подлинности пользователя.
- Добавлены классы `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer` и `Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer`, которые инициализируют контекст прошедшего проверку подлинности пользователя согласно настройкам пакета SDK для JavaScript.
- Добавлен класс `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` и поддержка фиксированной частоты выборки в качестве его реализации.
- Добавлен класс `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder`, который позволяет создавать канал `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` с набором объектов `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor`.

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
- Класс `DeviceContextInitializer` перемещен из сборки `Microsoft.ApplicationInsights` в сборку `Microsoft.ApplicationInsights.Extensibility.Web` и преобразован в `ITelemetryInitializer`.
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

 

<!---HONumber=AcomDC_0128_2016-->