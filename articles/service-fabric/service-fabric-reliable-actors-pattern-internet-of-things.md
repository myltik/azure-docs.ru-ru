
<properties
   pageTitle="Reliable Actors для Интернета вещей | Microsoft Azure"
   description="Субъекты Reliable Actors платформы Service Fabric являются ключевым структурным элементом (в рамках среднего уровня) решения. Такое решение объединяет внешний интерфейс системы обмена сообщениями с поддержкой разных средств передачи данных (например, HTTPS, MQTT и AMQP)."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Шаблон разработки надежных субъектов: Интернет вещей
Интернет вещей (IoT) стал новым трендом наряду с технологическим прогрессом в области разработки устройств и облачных служб. Неудивительно, что разработчики изучают ключевые структурные элементы, используемые при создании своих систем. На следующей схеме показаны ключевые сценарии, реализуемые с помощью субъектов Reliable Actors, предоставляемых платформой Service Fabric.

![Обзор ключевых сценариев использования Reliable Actors в Service Fabric][1]

Субъекты Reliable Actors платформы Service Fabric являются ключевым структурным элементом (в рамках среднего уровня) решения. Такое решение объединяет внешний интерфейс системы обмена сообщениями с поддержкой разных средств передачи данных (например, HTTPS, MQTT и AMQP) и функций обмена данными с субъектами, представляющих отдельные устройства. Так как субъекты могут поддерживать состояние и выполнять моделирование потоков (в частности, обработку потоков с учетом состояния), агрегация по каждому устройству выполняется напрямую. Если данные необходимо сохранить, очистку можно выполнить по требованию или по таймеру. При выполнении этой операции последние N битов данных хранятся в другой переменной для быстрого запроса.

Обратите внимание, что в примерах мы намеренно опустили подробности уровня событий и обмена сообщениями, на котором субъекты могут взаимодействовать с устройствами. Это сделано, чтобы вы могли сосредоточиться на модели Reliable Actors. По большому счету есть два сценария, которые обычно объединяются.

* **Сбор данных телеметрии и состояния с одного или нескольких устройств, а также хранение их состояния**. Представьте десятки тысяч мышеловок (это реальный сценарий заказчика), отправляющих данные. Эти данные являются базовыми независимо от того, попался ли вредитель в устройство. Такие данные агрегируются по регионам. Когда в одном регионе поймано достаточно мышей, туда отправляется инженер для очистки устройств. Является ли мышеловка субъектом? Да. Групповым субъектом в каждом регионе в качестве агрегатора? Конечно.

* **Отправка данных о поведении и конфигурации устройства на отдельное устройство или набор устройств**. Представьте бытовые устройства с солнечными батареями, для которых поставщик передает разные конфигурации с учетом особенностей потребления и сезона.

## Данные телеметрии и группирование устройств

Во-первых, рассмотрим случай, когда устройства (скажем, десятки тысяч) группируются, а затем каждое из них отправляет данные телеметрии в связанную группу. В следующем примере заказчик развернул устройства в каждом регионе. Сразу же после включения устройство отправляет сообщение **ActivateMe** с важными данными, включая сведения о расположении и версии. Субъект, связанный с устройством (через идентификатор устройства), настраивает исходное состояние для устройства, например локальное сохранение состояния (это состояние также может сохраняться) и регистрацию субъекта группы. В этом случае нашей модели мы назначаем случайную группу.

В процессе инициализации устройства мы можем настроить устройство путем получения данных конфигурации от субъекта группы или другого агента. Таким образом, хотя изначально устройства могут быть "глупыми", при инициализации они могут "поумнеть". После этого устройство и субъект готовы отправлять и обрабатывать данные телеметрии.

Все устройства периодически отправляют данные телеметрии соответствующему субъекту. Если субъект уже активирован, будет использоваться именно он. В противном случае субъект будет активирован. На этом этапе при необходимости можно восстановить состояние из стабильного хранилища. Полученные субъектом данные телеметрии сохраняются в локальной переменной.

Мы сделали так, чтобы упростить пример. В настоящей реализации мы, скорее всего, сохранили бы эти данные во внешнее хранилище. Это позволило бы нам выполнять мониторинг и диагностику работоспособности и производительности устройств. Наконец, мы отправляем данные телеметрии субъекту группы, которому субъект устройства логически принадлежит.

## Образец кода IoT: телеметрия

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : StatefulActor<ThingState>, IThing
{

    protected override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return Task.FromResult(true);
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

Задача нашего примера на уровне группы — мониторинг устройств в группе и агрегация данных телеметрии для создания предупреждений инженерам. В этом случае заказчику нужно отправлять инженеров в определенные регионы, где имеется некоторое количество неисправных устройств. Конечно, заказчик хотел бы снизить затраты, сократив время, проведенное инженерами в пути. По этой причине каждый субъект группы поддерживает агрегированное состояние неисправных устройств по региону. Когда это значение достигает порога, заказчик отправляет в регион инженера для замены или ремонта таких устройств.

Рассмотрим, как это делается:

## Образец кода IoT: группирование и агрегация

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : StatefulActor<ThingGroupState>, IThingGroup
{

    protected override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return Task.FromResult(true);
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return Task.FromResult(true);
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return Task.FromResult(true);
    }
}
```

Как видите, все довольно просто. После выполнения простых тестов выходные данные выглядят так:

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

Вы можете решить, что будет лучше сгруппировать устройства по регионам. Поэтому в конечном итоге способ группирования и (или) секционирования устройств — по географическому расположению, типу устройства, версии, клиенту или с использованием других критериев — это ваш выбор.

Все, что вам нужно учитывать при этом, — состояние устройства в контексте создания отчетов и анализа. Не используйте размноженные запросы к субъектам для создания субъектов отчетности. Помимо прочего, это может привести к созданию ненужных экземпляров и вызвать проблемы с производительностью. Именно поэтому мы сделали иллюстрацию шаблона явной. Мы рекомендуем два подхода к отчетности.

* Используйте субъект уровня группы (например, агрегатор), чтобы сохранить представление группы. Разрешите каждому субъекту отправлять с упреждением только релевантные данные. С помощью субъекта уровня группы можно просматривать состояние устройств в группе.

* Поддерживать явное хранилище, предназначенное для отчетности. Агрегатор может буферизировать и периодически передавать данные в хранилище отчетности для последующего запроса и анализа.

## Работа устройств
Теперь рассмотрим следующее: как на всех этих устройствах выполняются операции? Как и в случае с устройствами, субъекты могут предоставлять интерфейсы, позволяющие администраторам выполнять операции на устройствах. Например, представим, что администратору нужно задать новую конфигурацию группе бытовых устройств на солнечных батареях (это еще один реальный сценарий) в зависимости от сезонных или региональных изменений.

Задача — точное управление каждым устройством с помощью субъектов Thing, а также управление операциями группы с помощью субъектов ThingGroup. Это актуально независимо от того, что вы выполняете: агрегацию данных (например, телеметрии), поступающих от устройств, или отправку определенных данных (например, конфигурации) большому количеству устройств. Платформа отвечает как за распределение субъектов устройств, так и за обмен сообщениями между ними. Этот процесс абсолютно прозрачен для разработчика.

При обмене данными между компьютерами (сценарии M2M) хорошо работает как веерная топология, которую мы обсудили в разделе, посвященном [распределенным сетям и графам](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md), так и прямое взаимодействие между субъектами. Для сценариев M2M мы можем смоделировать для группы устройств субъект каталога и индекса. Такой субъект позволит устройствам обнаруживать друг друга и обмениваться сообщениями, как показано ниже.

![Модель субъекта каталога и индекса для группы устройств][2]

Модель Reliable Actors в Service Fabric также отвечает за время существования субъектов. Предположим, у вас есть устройства, которые всегда подключены, а также устройства, которые подключаются периодически. Так зачем тратить ресурсы на субъект, отвечающий за устройство, которое подключается через каждые 14 часов? Платформа Service Fabric позволяет сохранять и восстанавливать состояние устройства в нужное время и место.

Все больше пользователей расценивают предоставляемые платформой Service Fabric субъекты Reliable Actors как ключевые структурные элементы для реализации Интернета вещей.

## Дальнейшие действия
[Конструктивный шаблон Reliable Actors: смарт-кэш](service-fabric-reliable-actors-pattern-smart-cache.md)

[Конструктивный шаблон Reliable Actors: распределенные сети и графы](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Конструктивный шаблон Reliable Actors: управление ресурсами](service-fabric-reliable-actors-pattern-resource-governance.md)

[Конструктивный шаблон Reliable Actors в Service Fabric: построение служб с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Конструктивный шаблон Reliable Actors: распределенные вычисления](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Конструктивный шаблон Reliable Actors: некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=AcomDC_0121_2016-->