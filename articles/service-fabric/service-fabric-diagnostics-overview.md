---
title: "Обзор мониторинга и диагностики Azure Service Fabric | Документация Майкрософт"
description: "Узнайте, как отслеживать и диагностировать приложения Microsoft Azure Service Fabric, размещенные в Azure, в среде разработки или локальной среде."
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
ms.sourcegitcommit: a83fe451ec8b77b8b84575c5fd46f3661fc261c0
ms.openlocfilehash: 77e5872654aa6f50b5a6564fb8ee8c7a6a4c29a5


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Мониторинг и диагностика приложений Azure Service Fabric

Мониторинг, обнаружение, диагностика и устранение неполадок обеспечивают работу служб с минимальными нарушениями взаимодействия с пользователем. Эти средства предоставляют бизнес-аналитику, позволяют отслеживать использование ресурсов, обнаруживать сбои оборудования и программного обеспечения или проблемы производительности, а также диагностировать потенциальные проблемы в поведении служб. Мониторинг и диагностика состояния в рабочей среде играют важную роль. Однако вы можете существенно повысить эффективность работы и обеспечить успешное перемещение служб в рабочую среду, внедрив аналогичную модель мониторинга во время разработки служб. Платформа Service Fabric позволяет использовать средства диагностики, которые одинаково хорошо работают как в среде разработки на одном локальном компьютере, так и в условиях реального рабочего кластера. 

Мониторинг — это общий термин, охватывающий инструментирование кода, сбор журналов инструментирования, анализ журналов, визуализацию аналитики на основе данных журнала, оповещение на основе значений журнала и аналитики, мониторинг инфраструктуры и предоставление инженерам средств для выявления и диагностики проблем, влияющих на работу их клиентов. Эта статья содержит обзор возможностей мониторинга кластеров Service Fabric, размещенных в Azure или в локальной среде, которые развернуты на платформе Windows или Linux с использованием .NET. Для начала разделим задачу на три части.
- Инструментирование кода или инфраструктуры.
- Сбор создаваемых событий.
- Хранение, статистическая обработка, визуализация и анализ.

Хотя существуют продукты, которые охватывают все три области, многие клиенты выбирают разные технологии для каждой из них. Очень важно, чтобы они были сопряжены между собой, обеспечивая комплексное решение для мониторинга приложения. 

## <a name="monitoring-infrastructure"></a>Мониторинг инфраструктуры

Хотя Service Fabric помогает поддерживать работу приложения во время сбоев инфраструктуры, операторам приложения нужно знать, где возникает ошибка: в приложении или базовой инфраструктуре. Мониторинг инфраструктуры требуется также для планирования загрузки, чтобы определять, когда нужно добавлять или удалять элементы инфраструктуры. Важно осуществлять мониторинг и диагностику инфраструктуры и приложения, составляющих развернутую службу Service Fabric. Пока приложение доступно клиентам, в тех или иных частях инфраструктуры могут возникать проблемы.

### <a name="azure-monitoring"></a>Мониторинг Azure

[Мониторинг Azure](../monitoring-and-diagnostics/monitoring-overview.md) для развернутых кластеров Azure дает возможность отслеживать множество ресурсов Azure, на которых основан кластер Service Fabric. Предоставляется набор метрик, данные которых автоматически собираются и отображаются на портале Azure, для [масштабируемого набора виртуальных машин (VMSS)](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) и отдельных [виртуальных машин](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines). Эту информацию можно просмотреть на портале Azure, выбрав группу ресурсов, содержащихся в кластере Service Fabric, и щелкнув VMSS для просмотра. Затем следует выбрать "Метрики" в разделе мониторинга, чтобы просмотреть график значений метрики.

![Представление собранных данных метрик на портале Azure](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Диаграммы можно настроить, следуя инструкциям в статье [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). На основе этих метрик можно также создавать оповещения, как описано в статье [Создание оповещений для служб Azure с помощью портала Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Оповещения могут отправляться в службу уведомлений с помощью объектов webhook, как описано в статье [Настройка объектов webhook для оповещений на основе метрик Azure]. Мониторинг Azure поддерживает отдельную подписку. Если требуется поддержка нескольких подписок или дополнительные функции, можно воспользоваться решением [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) (входит в состав пакета Operations Management Suite), которое обеспечивает целостное решение по управлению ИТ-инфраструктурой (как локальной, так и облачной). Данные из службы мониторинга Azure могут направляться непосредственно в Log Analytics, в результате чего метрики и журналы для всей среды будут отображаться в одном месте.

Для мониторинга локальной инфраструктуры мы рекомендуем использовать пакет Operations Management Suite, но вы можете применить любое другое решение, которое есть в распоряжении вашей организации.

### <a name="service-fabric-support-logs"></a>Журналы поддержки Service Fabric

В случае, когда необходимо обратиться в службу поддержки Майкрософт за помощью в управлении кластером Azure Service Fabric, почти всегда требуются журналы поддержки. Если кластер размещен в Azure, то эти журналы автоматически настраиваются в процессе создания кластера, а затем собираются. Данные журналы хранятся в выделенной учетной записи хранения, которую можно просмотреть в группе ресурсов вашего кластера. У этой учетной записи хранения нет какого-либо фиксированного имени, но в ней отображаются контейнеры больших двоичных объектов и таблицы, имена которых начинаются с префикса "fabric". Если ваш кластер является изолированным, то необходимо настроить сбор этих журналов согласно инструкциям в разделах [Создание кластера под управлением Windows Server и управление им](service-fabric-cluster-creation-for-windows-server.md) и [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md). Для изолированной среды Service Fabric журналы следует отправлять в локальный файловый ресурс. Эти журналы **необходимы** для технической поддержки, но они не предназначены для использования кем-либо, кроме группы поддержки клиентов Майкрософт. Перефразируя Оби-Вана Кеноби, "это не те журналы, что вы ищете…"

## <a name="instrument-your-code"></a>Инструментирование кода

В основе большинства других аспектов мониторинга служб лежит инструментирование кода. Часто люди с удивлением узнают, как много нужно инструментов, но это нормально, если учесть, что это единственный способ узнать о проблеме, диагностировать ее и найти способ устранения. Хотя технически это возможно, мало где отладчик подключается к рабочей службе, поэтому важно иметь подробные данные инструментирования. При создании такого объема информации доставка всех событий из локального узла может быть затратной. Многие службы используют двухэтапную стратегию сокращения объема данных инструментирования.
* Все события хранятся в локальном последовательно заполняемом файле журнала в течение небольшого числа дней и собираются, только когда они нужны для отладки. Обычно события, необходимые для подробной диагностики, остаются на узле, чтобы снизить затраты и использование ресурсов.
* Любые события, отражающие работоспособность службы, такие как события ошибки, пульса или производительности, отправляются в центральный репозиторий, где их можно использовать для оповещения о неработоспособности службы.

Существуют решения, которые автоматически инструментируют код. Хотя эти продукты могут вполне успешно работать, почти всегда необходимо инструментирование вручную. В итоге у вас должно быть достаточно информации для экспертной отладки приложения. В следующих разделах описываются различные подходы к инструментированию кода и их преимущества в той или иной ситуации.

### <a name="eventsource"></a>EventSource

При создании решения Azure Service Fabric из шаблона в Visual Studio создается производный класс EventSource (*ServiceEventSource* или *ActorEventSource*). Таким образом вы получаете шаблон, в который можно добавить дополнительные события, подходящие для вашего приложения или службы. Имя EventSource **должно** быть уникальным, поэтому потребуется изменить первоначальную строку MyCompany-&lt;решение&gt;-&lt;проект&gt;. Наличие нескольких определений EventSource с одинаковым именем приведет к возникновению проблемы во время выполнения. У каждого определенного события должен быть уникальный идентификатор. Если идентификатор не является уникальным, происходит сбой во время выполнения. Нередко для идентификаторов предварительно назначаются диапазоны значений, чтобы избежать конфликтов между отдельными командами разработчиков. Дополнительные сведения об EventSource доступны в [блоге Вэнса Моррисона (Vance Morrison)](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) или [документации MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

#### <a name="using-structured-eventsource-events"></a>Использование структурированных событий EventSource

Каждое из приведенных событий определено для конкретного сценария использования (например, для регистрации типа службы). Подобное определение сообщений позволяет упаковывать данные вместе с текстом ошибки. Это расширяет возможности поиска и фильтрации по именам или значениям указанных свойств. Структурирование выходных данных инструментирования упрощает их использование, но чтобы определить новое событие для каждого варианта использования, требуется больше умственной работы и времени. Некоторые определения событий могут общими для всего приложения. Например, событие запуска или остановки метода может использоваться во многих службах в приложении. Предметная служба, например система обработки заказов, может использовать событие CreateOrder, которое будет уникальным исключительно для этой системы. Часто этот подход порождает множество событий и потенциально требует согласования идентификаторов между командами проектов. Полный пример структуры определений EventSource в Service Fabric представлен в службе [PartyCluster.ApplicationDeployService.ServiceEventSource](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster/blob/master/src/PartyCluster.ApplicationDeployService/ServiceEventSource.cs), входящей в пример Party Cluster.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        // ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```
#### <a name="using-eventsource-generically"></a>Универсальное использование EventSource

Так как определение конкретных событий может вызвать трудности, многие разработчики определяют несколько событий с общим набором параметров, которые обычно выводят свои данные в виде строки. Большая часть аспекта структурированности теряется, что усложняет поиск и фильтрацию результатов. При таком подходе несколько событий обычно соответствует уровням ведения журнала. В приведенном ниже фрагменте кода определяются сообщение об отладке и сообщение об ошибке.
```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
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
Гибридный подход, использующий структурированное и универсальное инструментирования, также может оказаться удачным. В этом случае структурированное инструментирование используется для сообщения об ошибках и метриках, а универсальные события — для подробного ведения журнала, с помощью которого инженеры устраняют неполадки.

### <a name="aspnet-core-logging"></a>Ведение журнала ASP.NET Core

Выбрать способ инструментирования кода может быть сложно. В случае неудачного выбора потребуется повторное инструментирование, а это означает пересмотр кода и его потенциальная дестабилизация. Чтобы снизить риск, разработчики могут выбрать библиотеку инструментирования, например [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), предоставляемую в ASP.NET Core. Это обеспечивает интерфейс [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger), который позволяет использовать выбранный поставщик, сведя к минимуму вмешательство в существующий код. Еще один привлекательный аспект заключается в том, что код может использоваться не только в .NET Core в Windows и Linux, но в полной версии платформы .NET. Это дает возможность стандартизировать код инструментирования для .NET и .NET Core.

#### <a name="how-to-use-microsoftextensionslogging-within-service-fabric"></a>Как использовать Microsoft.Extensions.Logging с Service Fabric

1. Добавьте пакет NuGet **Microsoft.Extensions.Logging** в инструментируемый проект. Вам также можете добавить пакеты любого поставщика, мы сделаем это для сторонних пакетов ниже. Дополнительные сведения см. в разделе [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Ведение журналов в ASP.NET Core).

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

        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");

        // In this variant, we're adding structured properties RequestName and Duration that has values MyRequest and the duration of the request.
        // More on why you'll want to do this later.
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

    ```

#### <a name="using-other-logging-providers"></a>Использование других поставщиков ведения журнала

Существуют сторонние поставщики, которые работают с таким подходом, например [SeriLog](https://serilog.net/), [NLog](http://nlog-project.org/) и [loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Каждый из них можно подключить к службе ведения журнала ASP.NET Core или использовать отдельно. В SeriLog имеется функция, позволяющая дополнять все сообщения, отправляемые из средства ведения журнала. Это можно использовать для вывода сведений об имени службы, типе и секциях. Чтобы использовать эту возможность в инфраструктуре ASP.NET Core, выполните следующее.

1. Добавьте в проект пакеты NuGet **Serilog**, **Serilog.Extensions.Logging** и **Serilog.Sinks.Observable**. Также в этом примере добавьте **SeriLog.Sinks.Literate**. Далее в этой статье показан более удачный подход.
2. Создайте LoggerConfiguration и экземпляр средства ведения журнала в SeriLog.

    ```csharp

        Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();

    ```

3. Добавьте аргумент SeriLog.ILogger в конструктор службы и передайте созданное средство ведения журнала.
    
    ```csharp

        ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();

    ```

4. В конструктор службы добавьте приведенный ниже код, который создает расширения для свойств ServiceTypeName, ServiceName, PartitionId и InstanceId службы. Он также добавляет их в фабрику ведения журнала ASP.NET Core, чтобы Microsoft.Extensions.Logging.ILogger можно было использовать в коде.
    
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

5. Инструментируйте код так же, как при использовании ASP.NET Core без SeriLog.

> [!NOTE] 
> Не рекомендуется использовать статический Log.Logger при таком подходе, так как Service Fabric может разместить в одном процессе несколько экземпляров одного типа службы. Это означало бы, что для всех запущенных экземпляров отображались бы значения расширений свойств, внесенные последним модулем записи. Это одна из причин, по которой переменная _logger является частным членом класса службы. Это также означает, что переменная _logger должна быть доступна в общем коде, который может совместно использоваться службами. 

### <a name="which-one-should-i-use"></a>Что же следует использовать?

Если для приложения очень важна производительность, то лучше всего использовать подход с EventSource, так как при нем **обычно** потребляется меньше ресурсов и повышается производительность по сравнению с ведением журнала ASP.NET Core или любыми сторонними решениями.  Для многих служб это не проблема, но если служба ориентирована на производительность, то использование EventSource может быть наилучшим выбором. Чтобы обеспечить преимущества структурированного ведения журнала, для внедрения EventSource потребуются большие усилия команды разработчиков. Лучший способ определить, что требуется для проекта, — выполнить быстрое прототипирование каждого подхода, а затем выбрать тот из них, который наилучшим образом соответствует вашим потребностям.

## <a name="event-and-log-collection"></a>Сбор событий и журналов

### <a name="azure-diagnostics"></a>Диагностика Azure

Помимо возможностей мониторинга, Azure также предоставляет средства для сбора событий из всех служб в центральное расположение. Существуют две статьи, в которых показано, как настроить сбор событий для [Windows](service-fabric-diagnostics-how-to-setup-wad.md) и [Linux](service-fabric-diagnostics-how-to-setup-lad.md). В этих статьях описывается сбор данных событий и их отправка в службу хранилища Azure. Это легко сделать на портале или с помощью шаблона Resource Manager, включив диагностику. После этого начнется сбор данных из нескольких источников событий, которые Service Fabric создает автоматически.

- События EventSource и счетчики производительности при использовании модели программирования Reliable Actors. Эти события перечислены в статье [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
- События EventSource при использовании модели программирования Reliable Services. Эти события перечислены в статье [Диагностические функции для надежных служб с отслеживанием состояния](service-fabric-reliable-services-diagnostics.md).
- Системные события, передаваемые как события трассировки событий Windows. Существует множество событий в этой категории, которые передаются из Service Fabric. К ним относятся события размещения, запуска и остановки службы. Чтобы просмотреть переданные события, лучше всего использовать [средство просмотра диагностических данных Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) при работе на локальном компьютере. Так как это собственные события трассировки событий Windows, существуют некоторые ограничения для их сбора.
- Начиная с выпуска Service Fabric 5.4 предоставляются события метрик работоспособности и нагрузки. Это позволяет собирать данные события и использовать их для создания хронологических отчетов и оповещений. Это также собственные события трассировки событий Windows, поэтому для их сбора существуют некоторые ограничения.

Если эти события настроены, то они отображаются в одной из учетных записей хранения Azure, созданных при создании кластера, при условии, что была включена диагностика. Соответствующие таблицы называются *WADServiceFabricReliableActorEventTable*, *WADServiceFabricReliableServiceEventTable* и *WADServiceFabricSystemEventTable*. События работоспособности не добавляются по умолчанию и требуют изменения шаблона Resource Manager. Дополнительные сведения см. в разделе [Сбор журналов с помощью системы диагностики Azure](service-fabric-diagnostics-how-to-setup-wad.md).

В этих статьях также показано, как помещать пользовательские события в службу хранилища Azure. Любая из написанных статей о системе диагностики Azure про то, как настроить передачу данных счетчиков производительности или других данных мониторинга из виртуальной машины в систему диагностики Azure, подходит и для кластера Service Fabric. Например, если вы не хотите использовать хранилище таблиц Azure в качестве места назначения, то можете прочитать статью [Потоковая передача данных системы диагностики Azure по критическому пути с помощью концентраторов событий](../event-hubs/event-hubs-streaming-azure-diags-data.md). После поступления событий в концентратор событий их можно считать и отправить в папку по своему усмотрению. Вы также можете изучить статью [Azure Diagnostics integration with Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/) (Интеграция системы диагностики Azure с Application Insights).

Одним из недостатков использования системы диагностики Azure является то, что настройка производится с помощью шаблона Resource Manager и поэтому происходит только на уровне масштабируемого набора виртуальных машин (VMSS). VMSS соответствует типу узла в Service Fabric. Это означает, что необходимо настроить каждый тип узла для всех приложений и служб, которые могут выполняться на узле этого типа. Это может привести к большому количеству событий EventSource, в зависимости от количества настроенных приложений и служб. Кроме того, при каждом изменении конфигурации любого приложения требуется выполнять развертывание с помощью Resource Manager. В идеале конфигурация мониторинга должна передаваться вместе с конфигурацией службы.

Система диагностики Azure работает только для кластеров Service Fabric, развернутых в Azure, но зато работает для кластеров Windows и Linux.

### <a name="eventflow"></a>EventFlow

[Решение EventFlow было выпущено командой разработчиков Visual Studio](https://github.com/Azure/diagnostics-eventflow). Оно предоставляет механизм маршрутизации событий от узла к одному или нескольким назначениям мониторинга. Так как это решение добавляется в проект службы как пакет NuGet, код и конфигурация EventFlow перемещается вместе со службой, устраняя проблему отдельной настройки каждого узла, которая была упомянута в отношении системы диагностики Azure. EventFlow выполняется внутри процесса службы и напрямую подключается к настроенным потокам вывода. Из-за этого прямого подключения EventFlow подходит для служб, развернутых в Azure, с помощью контейнера или в локальной среде. Нужно соблюдать осторожность, если на одном узле выполняется много реплик, поскольку каждый конвейер EventFlow создает внешнее соединение. Если вы размещаете много процессов, вы получите большое количество исходящих соединений. Это не является существенной проблемой для приложений Service Fabric, так как все реплики ServiceType выполняются в одном процессе, что ограничивает количество исходящих подключений. EventFlow также обеспечивает фильтрацию событий, что позволяет отправлять только события, соответствующие указанному фильтру. Дополнительные сведения об использовании EventFlow с Service Fabric см. в разделе [Сбор журналов непосредственно из процесса службы Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md).

> [!NOTE]
> В будущем выпуске Service Fabric станет доступным ведущее приложение EventSource, что обеспечит ожидание передачи входных данных на основе данных трассировки событий Windows, сбор метрик уровня узла и поддержку последовательно заполняемого файла журнала.

Использовать EventFlow довольно просто.
1. Добавьте пакет NuGet в проект службы.
2. В функции **Main** службы создайте конвейер EventFlow и настройте потоки вывода. В этом случае показано, как для вывода используется SeriLog.
    ```csharp

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

                    // The ServiceManifest.XML file defines one or more service type names.
                    // Registering a service maps a service type name to a .NET type.
                    // When Service Fabric creates an instance of this service type,
                    // an instance of the class is created in this host process.

                    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);
                    
                    // Prevents this host process from terminating so services keep running.
                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }

    ```
3. Создайте файл *eventFlowConfig.json* в папке службы PackageRoot\Config. Содержимое файла конфигурации выглядит следующим образом.
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
В конфигурации определено два потока ввода, два источника на основе EventSource, созданные Service Fabric, и EventSource для службы. Обратите внимание, что события уровня системы и события работоспособности, использующие данные трассировки событий Windows, недоступны для EventFlow. Это вызвано высоким уровнем привилегий, необходимых для ожидания передачи данных в источник данных трассировки событий Windows, а службы никогда не должны выполняться с высоким уровнем привилегий. Еще одним потоком ввода является SeriLog, его настройка выполнена в методе **Main**.  Кроме того, применено несколько фильтров. Первый указывает EventFlow удалять все события, которые относятся к подробному ведению журнала. Скоро мы вернемся к другому определению фильтра. Настроено два потока вывода. Это стандартный поток вывода, который будет записывать данные в окно вывода в Visual Studio, и ApplicationInsights. Не забудьте добавить ключ инструментирования.

4. Последним шагом является инструментирование кода. В этом примере мы будем инструментировать RunAsync несколькими способами в целях демонстрации. В приведенном ниже коде мы по-прежнему используем SeriLog, и часть синтаксиса предназначена исключительно для SeriLog. Следует учитывать специальные возможности выбранного решения для ведения журнала. Создано три события: событие уровня отладки и два информационных события, второе из которых отслеживает длительность запроса. При конфигурации EventFlow, приведенной выше, событие уровня отладки не должно попасть в поток вывода.

    ```csharp
        Stopwatch sw = Stopwatch.StartNew();

        while (true)
        {
            cancellationToken.ThrowIfCancellationRequested();

            sw.Restart();

            // Delay a random interval to provide a more interesting request duration.
            await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

            ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
            _logger.LogDebug("Debug level event from Microsoft.Logging");
            _logger.LogInformation("Informational level event from Microsoft.Logging");
            _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
        }
    ```

Чтобы просмотреть события в Application Insights, откройте портал Azure и перейдите к ресурсу ApplicationInsights. Щелкните "Поиск" в левом верхнем углу, и события должны отобразиться.

![Представление поиска событий Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.png)

Трассировки расположены в нижней части рисунка. Как видите, осталось только два события, так как событие уровня отладки было удалено EventFlow. А что это за запись запроса над трассировкой? Это третья строка инструментирования _logger. Она показывает, что событие было преобразовано в метрику запросов в Application Insights. Вернемся к определению фильтра, в котором указан тип metadata. В нем объявлено, что событие со свойством RequestName, имеющим значение MyRequest, и свойством Duration содержит длительность запроса в миллисекундах. Именно это отображается в событии запроса в Application Insights. Этот метод подходит для любых потоков ввода, поддерживаемых EventFlow, включая EventSource.

Если кластер является изолированным, и его невозможно подключить к облачному решению из-за установленных политик, то вы можете использовать Elasticsearch в качестве потока вывода, что поддерживается EventFlow. При этом возможна запись других потоков вывода. Рекомендуется также использовать запросы на включение внесенных изменений. У некоторых сторонних поставщиков ведения журнала для ASP.NET Core также есть решения, поддерживающие локальную установку.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Отчеты о работоспособности и нагрузке Azure Service Fabric

В Service Fabric используется собственная модель работоспособности, которая подробно описана в нескольких статьях.
- [Общие сведения о наблюдении за работоспособностью системы в Service Fabric](service-fabric-health-introduction.md)
- [Проверка работоспособности службы и оповещение о проблемах](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Добавление настраиваемых отчетов о работоспособности Service Fabric](service-fabric-report-health.md)
- [Просмотр отчетов о работоспособности Service Fabric](service-fabric-view-entities-aggregated-health.md)

Мониторинг работоспособности крайне важен для различных аспектов работы службы. Мониторинг наиболее важен, когда Service Fabric устанавливает новую версию именованного приложения, и после того, как каждый домен обновления службы будет обновлен и станет доступным для клиентов, прежде чем переходить к следующему домену обновления, необходимо пройти проверку работоспособности. Если достичь работоспособности не удается, выполняется откат развернутой службы, и приложение возвращается в известное рабочее состояние. Хотя некоторые клиенты будут затронуты, прежде чем службу удастся откатить, большинство из них не столкнется с проблемой. Кроме того, устранение проблемы произойдет относительно быстро и без необходимости ожидать действий со стороны оператора. Чем больше проверок работоспособности внедрено в код, тем устойчивее служба к проблемам развертывания.

Еще один аспект работоспособности службы — передача метрик из службы. Метрики важны в Service Fabric, так как они используются для балансировки использования ресурсов и могут служить индикатором работоспособности системы. Допустим, приложение содержит множество служб, и каждый экземпляр передает метрику запросов в секунду (RPS). Если одна служба использует больше ресурсов, чем другая служба, то Service Fabric перемещает экземпляры службы по кластеру, пытаясь выравнять использование ресурсов. Как это работает, подробно пояснено в статье [Управление потреблением ресурсов и нагрузкой в Service Fabric с помощью метрик](service-fabric-cluster-resource-manager-metrics.md).

Метрики также позволяют понять, как функционирует служба, и со временем с их помощью можно будет понять, работает ли служба в пределах ожидаемых параметров. Например, если служба основывается на тенденциях, и на 9:00 в понедельник среднее число запросов в секунду составляет 1000, то вы можете настроить отчет о работоспособности, который оповещает пользователя, если это значение выйдет за пределы диапазона 500–1500. Все может выглядеть отлично, но лучше еще раз проверить, чтобы вашим клиентам было удобно работать. Служба может определять набор метрик, которые могут использоваться в отчетах о работоспособности. Чтобы они не влияли на балансировку ресурсов кластера, присвойте весу метрик нулевое значение. Мы рекомендуем изначально присваивать метрикам нулевой вес и не увеличивать его, пока вы не убедитесь, что понимаете, как это повлияет на балансировку ресурсов кластера.

> [!TIP]
> Постарайтесь не использовать слишком много метрик с ненулевым весом. Может быть сложно понять, почему экземпляры службы перемещаются по кластеру, и небольшое число метрик может сыграть важную роль.

Целями метрик и отчетов о работоспособности может быть все, что может указывать на работоспособность и производительность приложения. Счетчик производительности ЦП может показать, как используется узел, но с его помощью вы вряд ли узнаете, является ли определенная служба работоспособной, так как на одном узле может быть запущено несколько служб. С другой стороны, работоспособность определенной службы можно определить с помощью таких метрик, как метрика запросов в секунду, метрика обработанных элементов или метрика задержки запросов.

Чтобы создавать отчет о работоспособности, добавьте код, как показано ниже.
```csharp
        if (!result.HasValue)
        {
            HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
            this.Partition.ReportInstanceHealth(healthInformation);
        }
```

Чтобы создавать отчет о метрике, добавьте в службу код, как показано ниже.
```csharp
        this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

## <a name="watchdogs"></a>Модули наблюдения

Модуль наблюдения — отдельная служба, которая может отслеживать работоспособность и нагрузку служб и сообщать о работоспособности любого элемента в иерархии модели работоспособности. Это позволяет предотвратить ошибки, которые невозможно обнаружить в контексте отдельной службы. В модулях наблюдения также удобно размещать код, который может выполнять действия по исправлению известных условий без взаимодействия с пользователем.

## <a name="visualization-analysis-and-alerting"></a>Визуализация, анализ и оповещение

Последней составляющей мониторинга является визуализация потока событий, создание отчетов о производительности службы и оповещение при обнаружении проблемы. Существует множество решений, которые используются для этого аспекта мониторинга. Application Insights и OMS можно использовать для оповещения на основе потока событий. Для визуализации данных можно использовать PowerBI или стороннее решение, например [Kibana](https://www.elastic.co/products/kibana) или [Splunk](https://www.splunk.com/).

## <a name="next-steps"></a>Дальнейшие действия

* [Сбор журналов с помощью системы диагностики Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Сбор журналов непосредственно из процесса службы Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Управление потреблением ресурсов и нагрузкой в Service Fabric с помощью метрик](service-fabric-cluster-resource-manager-metrics.md)




<!--HONumber=Feb17_HO3-->


