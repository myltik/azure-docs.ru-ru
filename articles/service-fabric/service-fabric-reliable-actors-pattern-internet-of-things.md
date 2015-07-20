
<properties
   pageTitle="Субъекты Azure Service Fabric для Интернета вещей"
   description="Субъекты Azure Service Fabric являются ключевым строительным блоком (в качестве среднего уровня) в системе, которая объединяет внешний интерфейс системы обмена сообщениями, поддерживающий множество средств транспорта, таких как HTTPS, MQTT или AMQP, затем взаимодействует с субъектами, представляющими отдельные устройства."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/01/2015"
   ms.author="claudioc"/>

# Шаблон разработки субъектов Service Fabric: Интернет вещей
Поскольку IoT стал новым трендом технологического прогресса устройств и облачных служб, разработчики начали рассматривать ключевые строительные блоки для разработки своих систем. На следующей схеме показаны ключевые сценарии, созданные с помощью субъектов Azure Service Fabric:

![][1]

Субъекты Azure Service Fabric являются ключевым строительным блоком (в качестве среднего уровня) в системе, которая объединяет внешний интерфейс системы обмена сообщениями, поддерживающий множество средств транспорта, таких как HTTPS, MQTT или AMQP, затем взаимодействует с субъектами, представляющими отдельные устройства. Так как субъекты могут поддерживать состояние, моделирование потоков, особенно обработка потоков с учетом состояния, и агрегация по каждому устройству выполняются напрямую. Если данные необходимо сохранять, мы также можем легко сбрасывать их по запросу или по таймеру, при этом легко поддерживая последние N битов данных в другой переменной для быстрого запроса. Обратите внимание, что в образцах мы намеренно опустили подробности уровня событий/обмена сообщениями, который позволяет субъектам взаимодействовать с устройствами, чтобы сосредоточиться на модели субъекта. Зачастую по сути объединяются два сценария:

* *Сбор данных телеметрии и состояния с одного или нескольких устройств и поддержание их состояния*. Представьте десятки тысяч мышеловок (да, это реальный сценарий заказчика), отправляющих такие базовые данные, как наличие пойманного вредителя в том или ином устройстве. Данные агрегируются по регионам, и когда в одном регионе поймано достаточно мышей, туда отправляется инженер для очистки устройств. Является ли мышеловка субъектом? Конечно. Групповым субъектом в каждом регионе в качестве агрегатора? Еще бы.

* *Передача поведения и конфигурации устройства на отдельное устройство или их набор*. Представьте домашние устройства солнечной энергетики, когда поставщик передает разные конфигурации в зависимости от схем потребления и сезонности.

## Данные телеметрии и группирование устройств

Сначала давайте рассмотрим случай, когда устройства (например, десятки тысяч) группируются и все вместе передают данные телеметрии связанной с ними группе. В следующем примере заказчик развернул устройства в каждом регионе. При включении устройства прежде всего оно отправляет сообщение ActivateMe с соответствующими сведениями, такими как расположение, версия и т. д. В свою очередь субъект, связанный с устройством (через идентификатор устройства), настраивает начальное состояние для устройства, например локальное сохранение состояния (оно может также сохраняться на постоянной основе) и регистрацию субъекта группы. В данном случае мы назначаем случайную группу для нашей модели.

В процессе инициализации устройства мы можем настроить устройство путем получения данных конфигурации от субъекта группы или другого агента. Таким образом устройства могут изначально быть весьма «глупыми» и стать «умнее» при инициализации. После этого устройство и субъект готовы отправлять и обрабатывать данные телеметрии.

Все устройства периодически отправляют данные своей телеметрии соответствующему субъекту. Если субъект уже активирован, то будет использоваться тот же субъект. В противном случае он будет активирован. На этом этапе он может при необходимости восстановить состояние из стабильного хранилища. При получении субъектом данных телеметрии он сохраняет их в локальной переменной. Это сделано нами для упрощения примера. В настоящей реализации мы, вероятно, сохраним данные во внешнем хранилище, чтобы разрешить операциям мониторинг и диагностику работоспособности и производительности устройств. Наконец, мы передаем данные телеметрии субъекту группы, к которой логически принадлежит субъект устройства.

## Образец кода IoT — телеметрия

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

public class Thing : Actor<ThingState>, IThing
{

    public override Task OnActivateAsync()
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
        return TaskDone.Done;
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

На уровне группы в нашем примере цель — отслеживание устройств в группе и агрегация данных телеметрии для выдачи предупреждений инженерам. В данном случае заказчику требуется отправлять инженеров в определенные регионы, где имеется некоторое количество неисправных устройств. Конечно, заказчик хотел бы снизить затраты путем минимизации времени инженеров, проводимого в пути. По этой причине каждый субъект группы поддерживает агрегированное состояние неисправных устройств по региону. Когда это значение достигает порога, заказчик отправляет в регион инженера для замены или восстановления таких устройств. Рассмотрим, как это делается:

## Образец кода IoT — группирование и агрегация

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

public class ThingGroup : Actor<ThingGroupState>, IThingGroup
{

    public override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return TaskDone.Done;
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return TaskDone.Done;
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

        return TaskDone.Done;
    }
}
```

Как видим, это достаточно прямолинейно. После выполнения простых тестов вывод выглядит следующим образом:

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

К слову, вы могли подумать, что было бы лучше, если устройства были сгруппированы по регионам. Разумеется, условия группирования или разделения устройств полностью определяем мы, будь то географическое местоположение, тип устройства, версия, арендатор и т. д. Здесь имеется важный момент: состояние устройства против отчетности и анализа. Именно поэтому мы сделали иллюстрацию шаблона явной. Нам необходимо избегать использования разветвленных запросов к субъектам для построения субъектов отчетности. Создание ненужных экземпляров и влияние на производительность — только два примера недостатков. Мы рекомендуем два подхода к отчетности.

* Использовать субъекта уровня группы, такого как агрегатор, для поддержания представления группы. Позволить каждому субъекту проактивно передавать только соответствующие данные для данного субъекта. Затем с помощью этого субъекта уровня группы можно просмотреть состояние устройств в группе.

* Поддерживать явное хранилище, предназначенное для отчетности. Агрегатор может буферизировать и периодически передавать данные в хранилище отчетности для последующего запроса и анализа.

## Работа устройств
Пока все хорошо, но как насчет операций на этих устройствах? Как и для устройств, субъекты могут предоставлять операционные интерфейсы, чтобы администратор мог выполнять операции на устройствах. Например, администратору требуется передать новую конфигурацию для группы домашних устройств солнечной энергетики (это еще один реальны сценарий) в зависимости от сезонных или региональных изменений.

Ключевая идея здесь в том, что мы обладаем детальным контролем над каждым устройством с помощью субъектов Thing, а также над групповыми операциями с помощью субъектов ThingGroup, будь то агрегация данных, поступающих с этих устройств, типа телеметрии, или отправка данных, например конфигурации, большому количеству устройств. Платформа отвечает за распределение субъектов устройств и обмен сообщениями между ними, что совершенно прозрачно для разработчика.

В плане связи между машинами (M2M) хорошо работает как схема «звезда», которую мы обсудили в разделе распределенных сетей и графов, так и прямое взаимодействие между субъектами. Для сценариев M2M мы можем смоделировать субъекта «Каталог/Индекс» для группы устройств, что позволит им обнаруживать друг друга и обмениваться сообщениями, как показано ниже:

![][2]

Субъекты Azure Service Fabric также заботятся о своем времени существования. Это можно представить так: у нас есть постоянно включенные устройства и устройства, которые подключаются периодически. Зачем нам поддерживать в памяти субъект, следящий за устройством, которое подключается один раз в 14 часов? Azure Service Fabric позволяет сохранять и восстанавливать состояние устройства в нужное время и в нужном месте.

Мы можем заключить, что все больше клиентов рассматривают субъекты Azure Service Fabric как ключевой строительный блок для реализации Интернета вещей.

## Дальнейшие действия
[Шаблон: смарт-кэш](service-fabric-reliable-actors-pattern-smart-cache.md)

[Шаблон: распределенные сети и графы](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Шаблон: управление ресурсами](service-fabric-reliable-actors-pattern-resource-governance.md)

[Шаблон: построение службы с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Шаблон: распределенные вычисления](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png
 

<!---HONumber=July15_HO2-->