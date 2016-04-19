<properties
   pageTitle="Reliable Actors в Service Fabric | Microsoft Azure"
   description="В этой статье описывается структура субъектов Reliable Actors в службах Reliable Services и работа с функциями платформы Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="amanbha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="vturecek"/>

# Использование платформы Service Fabric надежными субъектами

В этой статье объясняется, как работают субъекты Reliable Actors на платформе Service Fabric. Субъекты Reliable Actors выполняются в среде, размещенной в реализации службы Reliable Services с отслеживанием состояния под названием *служба субъектов*. Служба субъектов содержит все компоненты, необходимые для управления жизненным циклом и диспетчеризации относящихся к субъектам сообщений:

 - Среда выполнения субъектов управляет жизненным циклом и сбором мусора, а также обеспечивает однопоточный доступ.
 - Прослушиватель удаленных взаимодействий субъекта службы принимает вызовы удаленного доступа к субъектам и отправляет их диспетчеру для маршрутизации в соответствующий экземпляр субъекта.
 - Поставщик состояний субъекта служит оболочкой для поставщиков состояний (например для поставщика состояний надежных коллекций) и предоставляет адаптер для управления состоянием субъекта.

Вместе эти компоненты образуют платформу Reliable Actors.

## Структура служб

Поскольку служба субъектов сама по себе является службой Reliable Services, понятия [модели приложений](service-fabric-application-model.md), жизненного цикла, [упаковки](service-fabric-application-model.md#package-an-application), [развертывания]((service-fabric-deploy-remove-applications.md#deploy-an-application), обновления и масштабирования, связанные с Reliable Services, также относятся и к службам субъектов.

![Структура службы субъектов][1]

На представленной выше схеме показана связь между платформами приложений Service Fabric и кодом пользователя. Синие элементы обозначают платформу приложений служб Reliable Services, оранжевые — платформу Reliable Actor, а зеленые — код пользователя.


В службах Reliable Services служба наследует класс `StatefulService`, производный от `StatefulServiceBase` (`StatelessService` для служб без отслеживания состояния). В субъектах Reliable Actors вы используете службу субъектов с другой реализацией класса `StatefulServiceBase` — она реализует шаблон субъекта, где выполняются ваши объекты. Поскольку служба субъектов — всего лишь реализация класса `StatefulServiceBase`, вы можете написать свою собственную службу, производную от класса `ActorService`, и реализовать в ней функции уровня службы таким же образом, как при наследовании класса `StatefulService`, например:

 - резервное копирование и восстановление службы;
 - общие функции для всех субъектов, например автоматическое выключение;
 - вызовы удаленных взаимодействий для самой службы субъектов, а также для любого отдельного субъекта. 

### Работа со службой субъектов

Экземпляры субъектов имеют доступ к службе субъектов, в которой они выполняются. Через службу субъектов они могут программным образом получить контекст службы, включая идентификатор секции, имя службы, имя приложения и другие данные конкретной платформы Service Fabric:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```

Как и все службы Reliable Services, служба субъектов должна быть зарегистрирована в среде выполнения Service Fabric с указанием типа службы. Для того чтобы служба субъектов выполняла экземпляры вашей службы, тип вашего субъекта также нужно зарегистрировать в службе субъектов. Метод регистрации `ActorRuntime` выполняет это действие для субъектов. В самом простом случае можно просто зарегистрировать тип субъекта, и служба субъектов будет использоваться по умолчанию с предустановленными параметрами:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```  

Также можно сформировать саму службу субъектов, воспользовавшись лямбда-выражением, предоставляемым методом регистрации. Это позволяет настраивать службу субъектов, а также явно формировать экземпляры субъектов, в которые можно закладывать зависимости для субъекта через конструктор:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

### Методы службы субъектов

Служба субъектов реализует метод `IActorService`, который в свою очередь реализует метод `IService`. Это интерфейс используется удаленными взаимодействиями служб Reliable Services, что позволяет вызывать удаленные процедуры в методах службы. Он содержит методы уровня службы, которые можно вызывать удаленно с помощью удаленных взаимодействий служб.


#### Перечисление субъектов

Служба субъектов позволяет клиенту перечислять метаданные о размещенных службой субъектах. Поскольку служба субъектов — это секционированная служба с отслеживанием состояния, перечисление выполняется по каждому разделу. Так как каждая секция может содержать большое количество субъектов, перечисление возвращается как набор постраничных результатов. Страницы зацикливаются до тех пор, пока все они не будут прочитаны. В следующем примере показано, как создать список всех активных субъектов в одной секции службы субъектов:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);
                
    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

#### Удаление субъектов

В службе субъектов имеется также функция удаления субъектов:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);
            
await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```

Дополнительные сведения об удалении субъектов и их состояний см. в [документации по жизненному циклу субъектов](service-fabric-reliable-actors-lifecycle.md).

### Пользовательская служба субъектов

С помощью лямбда-выражения для регистрации субъектов можно также зарегистрировать собственную пользовательскую службу субъектов, производную от `ActorService`, и реализовать в ней собственные функции уровня службы. Для этого нужно написать класс службы, наследующий класс `ActorService`. Пользовательская служба субъектов наследует все функции среды выполнения субъектов от класса `ActorService` и может использовать ваши собственные методы.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```


#### Реализация резервного копирования и восстановления субъектов

 В следующем примере пользовательская служба субъектов предоставляет метод для резервного копирования данных субъектов с использованием прослушивателя удаленных взаимодействий, уже присутствующего в `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(...));
    }
}
```

В этом примере `IMyActorService` — это контракт удаленного взаимодействия, который реализует класс `IService`, а затем реализуется классом `MyActorService`. При добавлении удаленного контракта методы в классе `IMyActorService` становятся доступными для клиента за счет создания удаленного прокси с помощью метода `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```


## Модель приложения

Службы субъектов — это службы Reliable Services, поэтому используют точно такую же модель приложений. В то же время средства сборки платформы субъектов создают для вас множество файлов модели приложений.

### Манифест службы
 
Содержимое файла ServiceManifest.xml для службы субъектов создается средствами сборки платформы субъектов автоматически. А именно:

 - Тип службы субъектов. Имя типа создается с учетом имени проекта субъекта. В зависимости от атрибута сохраняемости у субъекта устанавливается флаг HasPersistedState.
 - Пакет кода.
 - Пакет конфигурации.
 - Ресурсы и конечные точки

### Манифест приложения

Средства сборки платформы субъектов автоматически создают для вашей службы субъектов определение по умолчанию. Свойства службы по умолчанию заполняются средствами сборки:

 - Счетчик наборов реплик определяется атрибутом сохраняемости у субъекта. Каждый раз, когда атрибут сохраняемости у субъекта изменяется, счетчик наборов реплик в определении службы по умолчанию сбрасывается.
 - Устанавливается схема секционирования и диапазон Uniform Int64 с полным диапазоном ключей Int64.

## Основные понятия о секции Service Fabric для субъектов

Службы субъектов — это секционированные службы с отслеживанием состояния. Каждая секция службы субъектов содержит набор субъектов. Секции службы автоматически распределяются между несколькими узлами Service Fabric. В результате распространяются экземпляры субъектов.

![Секционирование и распределение субъектов][5]

Службы Reliable Services создаются с использованием различных схем и диапазонов ключей секционирования. Служба субъектов использует схему секционирования Int64 с полным диапазоном ключей Int64 для сопоставления субъектов и секций.

### Идентификатор субъекта

Каждый созданный в службе субъект имеет уникальный идентификатор, представляемый классом `ActorId`. `ActorId` — это непрозрачное значение идентификатора, которое можно использовать для равномерного распределения субъектов между секциями служб за счет генерации идентификаторов случайным образом:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```

Каждый `ActorId` хэшируется в значение типа Int64, поэтому в службе субъектов необходимо использовать схему секционирования Int64 с полным диапазоном ключей Int64. Тем не менее, для `ActorID` можно использовать пользовательские значения идентификаторов, включая строковые значения, а также GUID и значения типа Int64.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```

При использовании строк и GUID значения хэшируются в значения типа Int64. Однако, если значение типа Int64 предоставляется в `ActorId` напрямую, Int64 сопоставляется с секцией напрямую без дополнительного хэширования. Его можно использовать для управления тем, в каких секциях будут размещаться субъекты.

## Дальнейшие действия
 - [Управление состоянием субъекта](service-fabric-reliable-actors-state-management.md)
 - [Жизненный цикл субъектов и сбор мусора](service-fabric-reliable-actors-lifecycle.md)
 - [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Пример кода](https://github.com/Azure/servicefabric-samples)

 
<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

<!---HONumber=AcomDC_0406_2016-->