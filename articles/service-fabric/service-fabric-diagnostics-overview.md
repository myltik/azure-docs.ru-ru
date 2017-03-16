---
title: "Обзор мониторинга и диагностики Azure Service Fabric | Документация Майкрософт"
description: "Узнайте, как отслеживать и диагностировать приложения Microsoft Azure Service Fabric, размещенные в Azure, в среде разработки или в локальной среде."
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: ebbb29ee031fb477ce284f7a0d27c1522317f4f0
ms.openlocfilehash: 46a35fa4ec341561ab234f7ec19fb20658fcb2c4
ms.lasthandoff: 02/27/2017


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Мониторинг и диагностика приложений Azure Service Fabric

Мониторинг и диагностика критически важны в реальной рабочей среде. Azure Service Fabric поможет вам реализовать мониторинг и диагностику при разработке службы, чтобы обеспечить беспроблемную работу службы как в локальной среде разработки на одном компьютере, так и в реальном рабочем кластере.

При разработке служб мониторинг и диагностика помогают достигнуть следующих целей:
* свести к минимуму нарушения работы клиентов;
* получать аналитическую бизнес-информацию;
* отслеживать использование ресурсов;
* выявлять сбои и проблемы с производительностью оборудования и программного обеспечения;
* диагностировать потенциальные проблемы со службой.

Мониторинг — это широкое понятие, которое включает в себя следующие задачи:
* инструментирование кода;
* сбор журналов инструментирования;
* анализ журналов;
* визуализация аналитики на основе данных журналов;
* настройка оповещений на основе данных журналов и аналитики;
* мониторинг инфраструктуры;
* обнаружение и диагностика проблем, которые влияют на клиентов.

Эта статья представляет собой обзор мониторинга кластеров Service Fabric, размещенных в Azure или в локальной среде на платформе Windows, Linux или Microsoft .NET Framework. Мы рассмотрим три важных аспекта мониторинга и диагностики:
- инструментирование кода или инфраструктуры;
- сбор создаваемых событий;
- хранение, статистическая обработка, визуализация и анализ.

Хотя доступны многие продукты, охватывающие все три аспекта мониторинга, клиенты часто выбирают различные технологии для каждого из них в отдельности. Очень важно, чтобы все элементы работали согласованно, создавая комплексное решение для мониторинга приложения.

## <a name="monitoring-infrastructure"></a>Мониторинг инфраструктуры

Service Fabric помогает поддерживать работу приложения во время сбоев инфраструктуры, но вам важно понимать, где произошел сбой: в самом приложении или в базовой инфраструктуре. Мониторинг инфраструктуры требуется еще и для планирования загрузки, позволяя вовремя добавлять или удалять элементы инфраструктуры. Важно осуществлять мониторинг и диагностику как для инфраструктуры, так и для приложения, которые вместе составляют развернутую службу Service Fabric. В инфраструктуре могут существовать проблемы, даже если приложение доступно для клиентов.

### <a name="azure-monitor"></a>Azure Monitor

[Мониторинг Azure](../monitoring-and-diagnostics/monitoring-overview.md) дает возможность отслеживать многие ресурсы Azure, из которых состоит кластер Service Fabric. Данные по набору метрик для [масштабируемого набора виртуальных машин](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) и отдельных [виртуальных машин](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) автоматически собираются и отображаются на портале Azure. Чтобы просмотреть собранные сведения, выберите на портале Azure группу ресурсов, содержащую кластер Service Fabric. Затем выберите нужный масштабируемый набор виртуальных машин. В разделе **Мониторинг** выберите **Метрики**, чтобы увидеть график значений.

![Представление собранных данных метрик на портале Azure](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.PNG)

Настройка параметров диаграммы описана в статье [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Кроме того, на основе этих метрик можно создавать оповещения, как описано в статье [Создание оповещений в Azure Monitor для служб Azure с помощью портала Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Можно отправлять оповещения в службу уведомлений с помощью объектов webhook, как описано в статье [Настройка объектов webhook для оповещений на основе метрик Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure Monitor поддерживает только одну подписку. Если вы настраиваете мониторинг для нескольких подписок, или вам нужны дополнительные функции, воспользуйтесь решением [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), которое входит в состав пакета Operations Management Suite. Это комплексное решение для управления как локальной, так и облачной ИТ-инфраструктурой. Данные из Azure Monitor можно направлять непосредственно в Log Analytics, чтобы метрики и журналы для всей среды отображались в одном расположении.

Для мониторинга локальной инфраструктуры мы рекомендуем использовать пакет Operations Management Suite, но вы можете применить любое другое решение для мониторинга инфраструктуры, которое есть в распоряжении вашей организации.

### <a name="service-fabric-support-logs"></a>Журналы поддержки Service Fabric

Если вы будете обращаться в службу поддержки корпорации Майкрософт с вопросами по управлению кластером Azure Service Fabric, вам почти наверняка потребуются журналы поддержки. Если кластер размещен в Azure, эти журналы автоматически настраиваются в процессе создания кластера, а затем собираются. Эти журналы хранятся в выделенной учетной записи хранения, размещенной в группе ресурсов вашего кластера. У этой учетной записи хранения нет фиксированного имени, но ее можно опознать по наличию таблиц и контейнеров больших двоичных объектов, имена которых начинаются с префикса *fabric*. Сведения о настройке сбора журналов для автономного кластера см. в статьях [Создание изолированного кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md) и [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md). Для автономных экземпляров Service Fabric журналы следует передавать в локальный файловый ресурс. Эти журналы **необходимы** для технической поддержки, но они не предназначены для использования кем-либо, кроме группы поддержки клиентов Майкрософт.

## <a name="instrument-your-code"></a>Инструментирование кода

В основе большинства других аспектов мониторинга служб лежит инструментирование кода. Инструментирование — это единственный способ выяснить, что какой-то компонент работает неправильно, и диагностировать проблему для ее дальнейшего исправления. Технически есть возможность подключить отладчик к рабочей службе, но обычно так никто не делает. Поэтому вам нужны подробные данные инструментирования. Но при таком большом объеме создаваемой информации вы столкнетесь с существенными затратами на передачу данных о событиях с локального узла. Многие службы используют двухэтапную стратегию сокращения объема данных инструментирования.
1.  Все события хранятся в локальном последовательно заполняемом файле журнала в течение непродолжительного времени и собираются, только когда они нужны для отладки. Обычно события, необходимые для подробной диагностики, остаются на узле, чтобы снизить затраты и использование ресурсов.
2.  Все события, которые сообщают о работоспособности службы, отправляются в центральный репозиторий, где их можно использовать для предупреждений о неработоспособности служб. Сюда относятся сведения об ошибках, пакеты пульса и информация о производительности.

Некоторые решения выполняют инструментирование кода автоматически. Они могут вполне успешно работать, но почти всегда требуется инструментирование вручную. В итоге у вас должно быть достаточно информации для экспертной отладки приложения. В следующих разделах описываются различные подходы к инструментированию кода и преимущества этих подходов для разных ситуаций.

### <a name="eventsource"></a>EventSource

Если вы создаете решение Service Fabric из шаблона в Visual Studio, в нем создается класс **ServiceEventSource** или **ActorEventSource**, производный от **EventSource**. При этом создается шаблон, в который вы можете добавлять события для конкретного приложения или службы. Имя **EventSource** **должно** быть уникальным, поэтому потребуется изменить стандартное имя MyCompany-&lt;решение&gt;-&lt;проект&gt;. Наличие нескольких определений **EventSource** с одинаковым именем приведет к возникновению проблемы во время выполнения. У каждого определенного события должен быть уникальный идентификатор. Если идентификатор не является уникальным, происходит сбой во время выполнения. Некоторые организации предварительно назначают для идентификаторов диапазоны значений, чтобы избежать конфликтов между командами разработчиков. Дополнительные сведения см. в [блоге Вэнса (Vance)](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) или в [документации MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

#### <a name="using-structured-eventsource-events"></a>Использование структурированных событий EventSource

Каждое из событий в примерах кода в этом разделе определено для конкретной ситуации, например для регистрации типа службы. Если вы определяете сообщения по сценариям использования, в них можно добавить текст сообщения об ошибке. Кроме того, вам будет легче искать или фильтровать сообщения по именам или значениям заданных свойств. Структурирование выходных данных инструментирования упрощает их использование, но чтобы определить новое событие для каждого варианта использования, требуется больше умственной работы и времени. Некоторые определения событий могут быть общими для всего приложения. Например, запуск или остановка метода применяются многократно во многих службах в рамках приложения. Специализированная служба, например система обработки заказов, может использовать событие **CreateOrder**, включающее собственное уникальное событие. Такой подход будет порождать множество событий и может потребовать согласования идентификаторов между командами проектов. Полный пример структуры для событий **EventSource** в Service Fabric представлен в событии **PartyCluster.ApplicationDeployService** из примера Party Cluster.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The instance constructor is private to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```

#### <a name="using-eventsource-generically"></a>Универсальное использование EventSource

Так как определение конкретных событий может вызвать трудности, многие разработчики определяют несколько событий с общим набором параметров, информация о которых обычно выводится в строковом формате. При этом они теряют преимущества структурированности, что усложняет поиск и фильтрацию результатов. При таком подходе обычно определяется небольшое число событий, соответствующих уровням ведения журнала. В приведенном ниже фрагменте кода определяются отладочное сообщение и сообщение об ошибке.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The Instance constructor is private, to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```

Хорошие результаты может дать гибридный подход, сочетающий структурированное и универсальное инструментирование. Структурированное инструментирование используется для уведомления об ошибках и метриках. Универсальные события можно использовать для подробного ведения журнала, который используется инженерами для устранения неполадок.

### <a name="aspnet-core-logging"></a>Ведение журнала ASP.NET Core

Очень важно тщательно спланировать методы инструментирования кода. Правильный план инструментирования позволит избежать потенциальной дестабилизации базы кода, которая повлечет за собой повторное инструментрирование кода. Чтобы снизить риск, вы можете применить библиотеку инструментирования, например [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), которая входит в Microsoft ASP.NET Core. ASP.NET Core предоставляет интерфейс [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger), который можно подключить к любому поставщику, с минимальными изменениями существующего кода. Код ASP.NET Core можно использовать на платформах Windows, Linux и в полной версии .NET Framework, то есть код инструментирования будет полностью стандартизирован.

#### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Использование Microsoft.Extensions.Logging в Service Fabric

1. Добавьте пакет NuGet Microsoft.Extensions.Logging в инструментируемый проект. Также добавьте все необходимые пакеты поставщиков (в следующем примере показано использование пакета стороннего разработчика). Дополнительные сведения см. в разделе [Introduction to Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Вводные сведения о ведении журналов в ASP.NET Core).
2. Добавьте в файл службы директиву **using** для Microsoft.Extensions.Logging.
3. Определите частную переменную в классе службы.

  ```csharp
  private ILogger _logger = null;

  ```
4. Добавьте в конструктор класса службы приведенный ниже код.

  ```csharp
  _logger = new LoggerFactory().CreateLogger<Stateless>();

  ```
5. Запустите инструментирование кода в методах. Вот несколько примеров.

  ```csharp
  _logger.LogDebug("Debug-level event from Microsoft.Logging");
  _logger.LogInformation("Informational-level event from Microsoft.Logging");

  // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
  // Later in the article, we discuss why this step is useful.
  _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

  ```

#### <a name="using-other-logging-providers"></a>Использование других поставщиков ведения журнала

Некоторые сторонние поставщики, например [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/) и [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging), используют подход, описанный в предыдущем разделе. Вы можете подключить любой из них к подсистеме ведения журналов ASP.NET Core или использовать их отдельно. Serilog предоставляет возможность создавать дополнительные свойства для сообщений, отправляемых из средства ведения журнала. Эту функцию можно использовать для вывода информации об имени и типе службы, а также секционировании. Чтобы использовать эту возможность в инфраструктуре ASP.NET Core, сделайте следующее.

1. Добавьте в проект пакеты NuGet Serilog, Serilog.Extensions.Logging и Serilog.Sinks.Observable. Для следующего примера добавьте также Serilog.Sinks.Literate. Более правильный подход показан далее в этой статье.
2. В SeriLog создайте LoggerConfiguration и экземпляр средства ведения журнала.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Добавьте аргумент Serilog.ILogger в конструктор службы и передайте созданное средство ведения журнала.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. В конструкторе службы добавьте следующий код. Этот код создает для службы дополнительные свойства с именами **ServiceTypeName**, **ServiceName**, **PartitionId** и **InstanceId**. Он также добавляет дополнительные свойства в фабрику ведения журналов ASP.NET Core, что позволяет использовать Microsoft.Extensions.Logging.ILogger в коде.

  ```csharp
  public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
      : base(context)
  {
      PropertyEnricher[] properties = new PropertyEnricher[]
      {
          new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
          new PropertyEnricher("ServiceName", context.ServiceName),
          new PropertyEnricher("PartitionId", context.PartitionId),
          new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
      };

      serilog.ForContext(properties);

      _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
  }
  ```

5. Инструментирование кода выполняется так же, как в ASP.NET Core без SeriLog.

  > [!NOTE]
  > Мы рекомендуем в предыдущем примере не использовать статический аргумент Log.Logger. Service Fabric позволяет разместить в одном процессе несколько экземпляров службы одного типа. Если вы примените статический аргумент Log.Logger, последний модуль записи дополнительных свойств покажет значения для всех запущенных экземпляров. Это одна из причин, по которой переменная _logger является частным членом класса службы. Кроме того, переменная _logger должна быть доступна в общем коде, который может использоваться несколькими службами.

### <a name="choosing-a-logging-provider"></a>Выбор поставщика ведения журнала

Если для вашего приложения требуется высокая производительность, лучше всего остановить выбор на **EventSource**. **EventSource** *обычно* использует меньше ресурсов и работает лучше, чем подсистема ведения журнала ASP.NET Core или любое из доступных решений сторонних поставщиков.  Для многих служб это не проблема, но для высокопроизводительных служб **EventSource** будет наилучшим выбором. Но чтобы использовать в **EventSource** все преимущества структурированного ведения журнала, потребуются большие усилия команды разработчиков. Чтобы выбрать правильный подход для своего проекта, создайте упрощенные прототипы по каждому из вариантов и выберите из них тот, который лучше всего соответствует вашим потребностям.

## <a name="event-and-log-collection"></a>Сбор событий и журналов

### <a name="azure-diagnostics"></a>Диагностика Azure

Помимо той информации, которую предоставляет Azure Monitor, Azure собирает в централизованном расположении события от всех служб. В отдельных статьях описана настройка сбора событий для [Windows](service-fabric-diagnostics-how-to-setup-wad.md) и [Linux](service-fabric-diagnostics-how-to-setup-lad.md). В них описывается сбор данных событий и их отправка в службу хранилища Azure. Для этого вам нужно включить диагностику на портале Azure или в шаблоне Azure Resource Manager. Система диагностики Azure собирает данные из нескольких источников событий, которые Service Fabric создает автоматически:

- События **EventSource** и счетчики производительности, если вы используете модель программирования Reliable Actors. Эти события перечислены в статье [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
- События **EventSource**, если вы используете модель программирования Reliable Services. Эти события перечислены в статье [Диагностические функции для надежных служб с отслеживанием состояния](service-fabric-reliable-services-diagnostics.md).
- Системные события передаются в рамках трассировки событий Windows (ETW). Существует множество событий в этой категории, которые передаются из Service Fabric. К ним относятся события размещения, запуска и остановки служб. Чтобы просмотреть переданные события, лучше всего использовать [средство просмотра диагностических данных Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) на локальном компьютере. Так как это собственные события ETW, существуют некоторые ограничения, касающиеся методов их сбора.
- Начиная с выпуска Service Fabric 5.4 предоставляются события метрик работоспособности и нагрузки. Таким образом, сбор событий можно использовать для отчетов и оповещений по историческим данным. Это также собственные события ETW, поэтому на методы их сбора также распространяются определенные ограничения.

Настроенные события отображаются в учетной записи хранения Azure, которая была создана при создании кластера, если вы включили для него диагностику. Используются таблицы с именами WADServiceFabricReliableActorEventTable, WADServiceFabricReliableServiceEventTable и WADServiceFabricSystemEventTable. По умолчанию события работоспособности не добавляются. Чтобы добавить их, следует внести изменения в шаблон Resource Manager. Дополнительные сведения см. в статье [Сбор журналов с помощью системы диагностики Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Перечисленные в этом разделе статьи позволяют также понять, как следует передавать пользовательские события в хранилище Azure. К кластеру Service Fabric применимы и любые другие статьи о системе диагностики Azure, в которых описывается настройка счетчиков производительности или передача других данных мониторинга с виртуальных машин в систему диагностики Azure. Например, если вы не хотите использовать хранилище таблиц Azure в качестве места назначения, изучите рекомендации из статьи [Потоковая передача данных системы диагностики Azure по критическому пути с помощью концентраторов событий](../event-hubs/event-hubs-streaming-azure-diags-data.md). События, собранные в концентраторах событий, можно просматривать и передавать в любое удобное расположение. Дополнительную информацию см. в записи блога [об интеграции системы диагностики Azure с Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/).

Недостатком системы диагностики Azure можно считать то, что она настраивается в шаблоне Resource Manager. Таким образом, диагностика применяется только на уровне масштабируемого набора виртуальных машин. Масштабируемый набор виртуальных машин соответствует типу узла в Service Fabric. Для каждого типа узла придется задать настройки для всех приложений и служб, которые могут выполняться на узлах этого типа. Это может привести к большому количеству событий **EventSource**, в зависимости от количества настроенных приложений и служб. Кроме того, при любых изменениях конфигурации приложений потребуется выполнять развертывание Resource Manager. В идеале конфигурация мониторинга должна передаваться вместе с конфигурацией службы.

Система диагностики Azure работает только для кластеров Service Fabric, развернутых в Azure. Ее можно использовать для кластеров под управлением Windows и Linux.

### <a name="eventflow"></a>EventFlow

[EventFlow службы диагностики Microsoft](https://github.com/Azure/diagnostics-eventflow) позволяет направлять события от узла к одному или нескольким целевым объектам для мониторинга. Так как это решение включается в проект службы как пакет NuGet, код и конфигурация EventFlow перемещаются вместе со службой, устраняя необходимость отдельной настройки каждого узла для системы диагностики Azure, которую мы обсуждали выше. EventFlow выполняется внутри процесса службы и напрямую подключается к настроенным потокам вывода. Прямое подключение позволяет использовать EventFlow для служб, развернутых в Azure, с помощью контейнера или в локальной среде. Соблюдайте осторожность при выполнении EventFlow в сценариях с высокой плотностью, например в контейнере, так как каждый конвейер EventFlow создает внешнее соединение. Если вы разместите много процессов, то получите много исходящих подключений. Об этом можно не беспокоиться при использовании приложений Service Fabric, так как все реплики `ServiceType` выполняются в одном процессе, что ограничивает количество исходящих подключений. EventFlow также поддерживает фильтрацию событий, то есть позволяет отправлять только события, соответствующие указанному фильтру. Дополнительные сведения об использовании EventFlow для Service Fabric см. в статье [Сбор журналов непосредственно из процесса службы Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md).

Чтобы использовать EventFlow, выполните следующие действия.

1. Добавьте пакет NuGet в проект службы.
2. В функции **Main** этой службы создайте конвейер EventFlow и настройте потоки вывода. В следующем примере мы используем в качестве потока вывода Serilog.

  ```csharp
  internal static class Program
  {
      /// <summary>
      /// This is the entry point of the service host process.
      /// </summary>
      private static void Main()
      {
          try
          {
              using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
              {
                  Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                  // The ServiceManifest.xml file defines one or more service type names.
                  // Registering a service maps a service type name to a .NET type.
                  // When Service Fabric creates an instance of this service type,
                  // an instance of the class is created in this host process.

                  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                  ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);

                  // Prevents this host process from terminating, so services keep running.
                  Thread.Sleep(Timeout.Infinite);
              }
          }
          catch (Exception e)
          {
              ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
              throw;
          }
      }
  }
  ```

3. Создайте файл eventFlowConfig.json в папке \\PackageRoot\\Config в рамках службы. Содержимое файла конфигурации выглядит так:

  ```json
      {
      "inputs": [
          {
          "type": "EventSource",
          "sources": [
              { "providerName": "Microsoft-ServiceFabric-Services" },
              { "providerName": "Microsoft-ServiceFabric-Actors" },
              { "providerName": "MyCompany-MonitoringE2E-Stateless" }
          ]
          },
          {
          "type": "Serilog"
          }
      ],
      "filters": [
          {
          "type": "drop",
          "include": "Level == Verbose"
          },
          {
          "type": "metadata",
          "metadata": "request",
          "requestNameProperty": "RequestName",
          "include":  "RequestName==MyRequest",
          "durationProperty": "Duration",
          "durationUnit": "milliseconds"
          }
      ],
      "outputs": [
          {
          "type": "StdOutput"
          },
          {
          "type": "ApplicationInsights",
          "instrumentationKey": "== instrumentation key here =="
          }
      ],
      "schemaVersion": "2016-08-11",
      "extensions": []
      }
  ```
    В конфигурации определено два потока ввода: два источника на основе **EventSource**, созданные Service Fabric, и **EventSource** для службы. Для EventFlow недоступны события уровня системы и события работоспособности, использующие данные ETW. Это связано с тем, что для ожидания передачи данных от источника ETW требуется высокий уровень привилегий, а службы не могут выполняться с высоким уровнем привилегий. Второй поток вывода — Serilog. Настройка SeriLog производится в методе **Main**.  При этом применяются определенные фильтры. Первый фильтр сообщает EventFlow, что следует удалить все события с уровнем событий "Подробный". Настроено два потока вывода: стандартный поток вывода, который передает данные в окно вывода в Visual Studio, и ApplicationInsights. Обязательно добавьте ключ инструментирования.

4. Выполните инструментирование кода. Ниже представлены несколько примеров инструментирования `RunAsync` разными способами. В следующем коде мы снова используем Serilog. Синтаксис этого кода частично определяется особенностями Serilog. Не забудьте учесть возможности того решения для ведения журнала, которое вы применяете. Создаются три события: событие уровня отладки и два информационных события. Второе информационное событие отслеживает длительность запроса. В той конфигурации EventFlow, которая представлена выше, событие уровня отладки не будет попадать в поток вывода.

  ```csharp
      Stopwatch sw = Stopwatch.StartNew();

      while (true)
      {
          cancellationToken.ThrowIfCancellationRequested();

          sw.Restart();

          // Delay a random interval, to provide a more interesting request duration.
          await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

          ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
          _logger.LogDebug("Debug level event from Microsoft.Logging");
          _logger.LogInformation("Informational level event from Microsoft.Logging");
          _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
      }
  ```

Чтобы просмотреть события в Azure Application Insights, откройте портал Azure и перейдите к ресурсу Application Insights. Чтобы просмотреть события, выберите поле **Поиск**.

![Представление поиска событий Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.PNG)

Трассировки можно увидеть в нижней части предыдущего снимка экрана. Здесь отображаются только два события, так как событие уровня отладки было удалено EventFlow. Третья строка инструментирования `_logger` — это запись запроса, предшествующая трассировке. Эта строка показывает, что событие в Application Insights было преобразовано в метрику запроса.

В определении фильтра указан тип **метаданные**. Здесь объявлено, что событие, у которого есть свойство `RequestName` со значением `MyRequest`, и еще одно свойство, `Duration`, содержат длительность запроса в миллисекундах. Именно это отображается в событии запроса в Application Insights. Этот метод подходит для любых потоков ввода, поддерживаемых EventFlow, включая **EventSource**.

Если у вас есть автономный кластер, который нельзя подключить к облачному решению в соответствии с действующими политиками, в качестве выходного потока можно использовать Elasticsearch. Данные можно записывать и в другие выходные потоки. Мы рекомендуем использовать запросы на включение внесенных изменений. У некоторых сторонних поставщиков систем ведения журнала для ASP.NET Core также есть решения, поддерживающие локальную установку.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Отчеты о работоспособности и нагрузке Azure Service Fabric

В Service Fabric используется собственная модель работоспособности, которая подробно описана в следующих статьях.
- [Общие сведения о наблюдении за работоспособностью системы в Service Fabric](service-fabric-health-introduction.md)
- [Проверка работоспособности службы и оповещение о проблемах](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Добавление настраиваемых отчетов о работоспособности Service Fabric](service-fabric-report-health.md)
- [Просмотр отчетов о работоспособности Service Fabric](service-fabric-view-entities-aggregated-health.md)

Мониторинг работоспособности крайне важен для многих аспектов работы службы. Отслеживать работоспособность особенно важно, когда Service Fabric выполняет обновление именованного приложения. После того как каждый домен обновления службы обновляется и становится доступен клиентам, для него проводится проверка работоспособности. Только после этого можно переходить к развертыванию службы в следующем домене обновления. Если достичь работоспособности не удается, выполняется откат развернутой службы, и приложение возвращается в известное рабочее состояние. В этот период до отката службы некоторые клиенты могут столкнуться с проблемами, но для большинства из них процесс пройдет незаметно. Устранение проблемы происходит довольно быстро и не требует вмешательства человека. Чем больше проверок работоспособности внедрено в код, тем устойчивее служба к проблемам развертывания.

Еще один аспект работоспособности службы — передача метрик из службы. Метрики очень важны в Service Fabric, так как они используются для балансировки использования ресурсов. Метрики также служат признаком работоспособности системы. Допустим, вы используете приложение с несколькими службами, в котором каждый экземпляр передает метрику количества запросов в секунду (RPS). Если одна из служб использует больше ресурсов, чем другая, Service Fabric перемещает экземпляры службы по кластеру, поддерживая равномерное использование ресурсов. Использование ресурсов подробно описано в статье [Управление потреблением ресурсов и нагрузкой в Service Fabric с помощью метрик](service-fabric-cluster-resource-manager-metrics.md).

Также метрики дают представление об эффективности службы. С их помощью вы можете убедиться, что служба работает с соблюдением заданных параметров. Например, если статистические данные показывают, что в 9:00 в понедельник выполняется в среднем 1000 запросов в секунду, вы можете настроить отчет о работоспособности, который оповестит о том, что число запросов в секунду оказалось больше 1500 или меньше 500. Возможно, все работает нормально, но такие отклонения следует проверять дополнительно, чтобы гарантировать удобство работы для ваших клиентов. Для службы можно определить набор метрик, которые будут информировать о работоспособности, но не будут использоваться для балансировки ресурсов кластера. Для этого присвойте весу метрик нулевое значение. Мы рекомендуем изначально присваивать всем метрикам нулевой вес и не увеличивать его, пока вы не будете твердо уверены, как изменение веса повлияет на балансировку ресурсов кластера.

> [!TIP]
> Не используйте много метрик с ненулевым весом. Вам будет сложно понять, почему экземпляры службы перемещаются для балансировки. Нескольких метрик вполне достаточно.

Для метрик и отчетов о работоспособности подойдет любая информация, которая указывает на работоспособность и производительность приложения. Счетчик производительности ЦП может показать, как используется узел, но он не даст информации о работоспособности определенной службы, так как на одном узле может быть запущено несколько служб. Для этих целей лучше подойдут такие метрики, как количество запросов в секунду, количество обработанных элементов или задержка выполнения запроса.

Чтобы создать отчет о работоспособности, используйте примерно такой код:

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Чтобы создать отчет о метрике, используйте примерно такой код:

  ```csharp
    this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

## <a name="watchdogs"></a>Модули наблюдения

Модуль наблюдения — это отдельная служба, которая может отслеживать работоспособность и нагрузку в службах и сообщать о работоспособности любого элемента в иерархии модели работоспособности. Он позволяет предотвратить ошибки, которые невозможно обнаружить в контексте отдельной службы. В модулях наблюдения также удобно размещать код, который может выполнять действия по исправлению известных условий без взаимодействия с пользователем.

## <a name="visualization-analysis-and-alerts"></a>Визуализация, анализ и оповещения

Последней составляющей мониторинга является визуализация потока событий, создание отчетов о производительности службы и оповещение при обнаружении проблем. Есть много решений, которые можно использовать для реализации этого аспекта мониторинга. Azure Application Insights и Operations Management Suite могут создавать оповещения на основе потока событий. Для визуализации данных можно применить Microsoft Power BI или решения сторонних производителей, например [Kibana](https://www.elastic.co/products/kibana) или [Splunk](https://www.splunk.com/).

## <a name="next-steps"></a>Дальнейшие действия

* [Сбор журналов с помощью системы диагностики Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Сбор журналов непосредственно из процесса службы Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Управление потреблением ресурсов и нагрузкой в Service Fabric с помощью метрик](service-fabric-cluster-resource-manager-metrics.md)

