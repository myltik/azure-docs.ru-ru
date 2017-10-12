---
title: "Мониторинг на уровне приложений службы Azure Service Fabric | Документы Майкрософт"
description: "Сведения о журналах и событиях на уровне приложений и служб, используемых для мониторинга и диагностики кластеров Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: 3c472904641108b7383cd0f1416c47460f8de11a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="application-and-service-level-event-and-log-generation"></a>Создание событий и журналов на уровне приложений и служб

## <a name="instrumenting-the-code-with-custom-events"></a>Инструментирование кода с помощью пользовательских событий

В основе большинства других аспектов мониторинга служб лежит инструментирование кода. Инструментирование — это единственный способ выяснить, что какой-то компонент работает неправильно, и диагностировать проблему для ее дальнейшего исправления. Технически есть возможность подключить отладчик к рабочей службе, но обычно так никто не делает. Поэтому вам нужны подробные данные инструментирования.

Некоторые решения выполняют инструментирование кода автоматически. Они могут вполне успешно работать, но почти всегда требуется инструментирование вручную. В итоге у вас должно быть достаточно информации для экспертной отладки приложения. В этом документе описываются различные подходы к инструментированию кода и преимущества этих подходов для разных ситуаций.

## <a name="eventsource"></a>EventSource
Если вы создаете решение Service Fabric из шаблона в Visual Studio, в нем создается класс **ServiceEventSource** или **ActorEventSource**, производный от **EventSource**. При этом создается шаблон, в который вы можете добавлять события для конкретного приложения или службы. Имя **EventSource** **должно** быть уникальным, поэтому потребуется изменить стандартное имя MyCompany-&lt;решение&gt;-&lt;проект&gt;. Наличие нескольких определений **EventSource** с одинаковым именем приведет к возникновению проблемы во время выполнения. У каждого определенного события должен быть уникальный идентификатор. Если идентификатор не является уникальным, происходит сбой во время выполнения. Некоторые организации предварительно назначают для идентификаторов диапазоны значений, чтобы избежать конфликтов между командами разработчиков. Дополнительные сведения см. в [блоге Вэнса (Vance)](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) или в [документации MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

### <a name="using-structured-eventsource-events"></a>Использование структурированных событий EventSource

Каждое из событий в примерах кода в этом разделе определено для конкретной ситуации, например для регистрации типа службы. Если вы определяете сообщения по сценариям использования, в них можно добавить текст сообщения об ошибке. Кроме того, вам будет легче искать или фильтровать сообщения по именам или значениям заданных свойств. Структурирование выходных данных инструментирования упрощает их использование, но чтобы определить новое событие для каждого варианта использования, требуется больше умственной работы и времени. Некоторые определения событий могут быть общими для всего приложения. Например, запуск или остановка метода применяются многократно во многих службах в рамках приложения. Специализированная служба, например система обработки заказов, может использовать событие **CreateOrder**, включающее собственное уникальное событие. Такой подход будет порождать множество событий и может потребовать согласования идентификаторов между командами проектов. 

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

### <a name="using-eventsource-generically"></a>Универсальное использование EventSource

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

## <a name="aspnet-core-logging"></a>Ведение журнала ASP.NET Core

Очень важно тщательно спланировать методы инструментирования кода. Правильный план инструментирования позволит избежать потенциальной дестабилизации базы кода, которая повлечет за собой повторное инструментрирование кода. Чтобы снизить риск, вы можете применить библиотеку инструментирования, например [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), которая входит в Microsoft ASP.NET Core. ASP.NET Core предоставляет интерфейс [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger), который можно подключить к любому поставщику, с минимальными изменениями существующего кода. Код ASP.NET Core можно использовать на платформах Windows, Linux и в полной версии .NET Framework, то есть код инструментирования будет полностью стандартизирован. Это подробнее рассматривается ниже:

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Использование Microsoft.Extensions.Logging в Service Fabric

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

## <a name="using-other-logging-providers"></a>Использование других поставщиков ведения журнала

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

4. В конструктор службы добавьте приведенный ниже код, который создает расширения для свойств **ServiceTypeName**, **ServiceName**, **PartitionId** и **InstanceId** службы. Он также добавляет дополнительные свойства в фабрику ведения журналов ASP.NET Core, что позволяет использовать Microsoft.Extensions.Logging.ILogger в коде.

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

  >[!NOTE]
  >Мы рекомендуем в предыдущем примере не использовать статический аргумент Log.Logger. Service Fabric позволяет разместить в одном процессе несколько экземпляров службы одного типа. Если вы примените статический аргумент Log.Logger, последний модуль записи дополнительных свойств покажет значения для всех запущенных экземпляров. Это одна из причин, по которой переменная _logger является частным членом класса службы. Кроме того, переменная _logger должна быть доступна в общем коде, который может использоваться несколькими службами.

## <a name="choosing-a-logging-provider"></a>Выбор поставщика ведения журнала

Если для вашего приложения требуется высокая производительность, рекомендуется остановить выбор на **EventSource**. **EventSource** *обычно* использует меньше ресурсов и работает лучше, чем подсистема ведения журнала ASP.NET Core или любое из доступных решений сторонних поставщиков.  Для многих служб это не проблема, но для высокопроизводительных служб **EventSource** будет наилучшим выбором. Однако чтобы использовать в **EventSource** преимущества структурированного ведения журнала, потребуются большие усилия команды разработчиков. По возможности, создайте легко реализуемые модели нескольких параметров ведения журнала, а затем выберите ту модель, которая лучше всего соответствует требованиям.

## <a name="next-steps"></a>Дальнейшие действия

После выбора поставщика ведения журнала для инструментирования приложений и служб вам потребуется агрегировать журналы и события перед отправкой в любую платформу аналитики. Прочтите статьи об [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) и [WAD](service-fabric-diagnostics-event-aggregation-wad.md), чтобы лучше понять некоторые рекомендуемые варианты.
