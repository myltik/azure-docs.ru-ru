---
title: "Reliable Actors в Service Fabric | Документация Майкрософт"
description: "В этой статье описывается структура субъектов Reliable Actors в службах Reliable Services и работа с функциями платформы Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 0a12da52b6e74c721cd25f89e7cde3c07153a396
ms.contentlocale: ru-ru
ms.lasthandoff: 04/14/2017

---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Использование платформы Service Fabric надежными субъектами
В этой статье объясняется, как работают субъекты Reliable Actors на платформе Azure Service Fabric. Субъекты Reliable Actors выполняются в среде, размещенной в реализации службы Reliable Services с отслеживанием состояния под названием *служба субъектов*. Служба субъектов содержит все компоненты, необходимые для управления жизненным циклом и диспетчеризации относящихся к субъектам сообщений:

* Среда выполнения субъектов управляет жизненным циклом и сбором мусора, а также обеспечивает однопоточный доступ.
* Прослушиватель удаленных взаимодействий субъекта службы принимает вызовы удаленного доступа к субъектам и отправляет их диспетчеру для маршрутизации в соответствующий экземпляр субъекта.
* Поставщик состояний субъекта служит оболочкой для поставщиков состояний (например для поставщика состояний надежных коллекций) и предоставляет адаптер для управления состоянием субъекта.

Вместе эти компоненты образуют платформу Reliable Actors.

## <a name="service-layering"></a>Структура служб
Так как служба субъектов сама по себе является надежной службой, понятия [модели приложений](service-fabric-application-model.md), жизненного цикла, [упаковки](service-fabric-package-apps.md), [развертывания](service-fabric-deploy-remove-applications.md), обновления и масштабирования, связанные с Reliable Services, также относятся и к службам субъектов. 

![Структура службы субъектов][1]

На предыдущей схеме показана связь между платформами приложений Service Fabric и кодом пользователя. Синие элементы обозначают платформу приложений служб Reliable Services, оранжевые — платформу Reliable Actor, а зеленые — код пользователя.

В службах Reliable Services ваша служба наследует класс `StatefulService`, производный от `StatefulServiceBase` (или `StatelessService` для служб без отслеживания состояния). В службе Reliable Actors используется служба субъектов с другой реализацией класса `StatefulServiceBase` — она реализует шаблон субъекта, согласно которому выполняются ваши субъекты. Так как служба субъектов — всего лишь реализация класса `StatefulServiceBase`, вы можете написать свою собственную службу, производную от класса `ActorService`, и реализовать в ней функции уровня службы таким же образом, как при наследовании класса `StatefulService`, например:

* резервное копирование и восстановление службы;
* общие функции для всех субъектов, например автоматическое выключение;
* вызовы удаленных взаимодействий для самой службы субъектов, а также для любого отдельного субъекта.

> [!NOTE]
> В настоящее время службы с отслеживанием состояния не поддерживаются в Java или Linux.

### <a name="using-the-actor-service"></a>Работа со службой субъектов
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
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```


Как и все службы Reliable Services, служба субъектов должна быть зарегистрирована в среде выполнения Service Fabric с указанием типа службы. Чтобы служба субъектов выполняла экземпляры вашей службы, тип вашего субъекта также нужно зарегистрировать в службе субъектов. Метод регистрации `ActorRuntime` выполняет это действие для субъектов. В самом простом случае можно просто зарегистрировать тип субъекта, и служба субъектов будет использоваться по умолчанию с предустановленными параметрами:

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

Можно также сформировать саму службу субъектов, воспользовавшись лямбда-выражением, предоставляемым методом регистрации. Это позволяет настраивать службу субъектов, а также явно формировать экземпляры субъектов, в которые можно закладывать зависимости для субъекта через конструктор:

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
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

### <a name="actor-service-methods"></a>Методы службы субъектов
Служба субъектов реализует метод `IActorService` (C#) или `ActorService` (Java), который, в свою очередь, реализует метод `IService` (C#) или `Service` (Java). Это интерфейс используется удаленными взаимодействиями служб Reliable Services, что позволяет вызывать удаленные процедуры в методах службы. Он содержит методы уровня службы, которые можно вызывать удаленно с помощью удаленных взаимодействий служб.

#### <a name="enumerating-actors"></a>Перечисление субъектов
Служба субъектов позволяет клиенту перечислять метаданные о размещенных службой субъектах. Так как служба субъектов — это секционированная служба с отслеживанием состояния, перечисление выполняется по каждому разделу. Так как каждая секция может содержать большое количество субъектов, перечисление возвращается как набор постраничных результатов. Страницы зацикливаются до тех пор, пока все они не будут прочитаны. В следующем примере показано, как создать список всех активных субъектов в одной секции службы субъектов:

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

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>Удаление субъектов
В службе субъектов имеется также функция удаления субъектов:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Дополнительные сведения об удалении субъектов и их состояний см. в [документации по жизненному циклу субъектов](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Пользовательская служба субъектов
С помощью лямбда-выражения для регистрации субъектов можно зарегистрировать собственную пользовательскую службу субъектов, производную от `ActorService` (C#) и `FabricActorService` (Java), и реализовать в ней собственные функции уровня службы. Для этого нужно написать класс службы, наследующий класс `ActorService` (C#) или `FabricActorService` (Java). Пользовательская служба субъектов наследует все функции среды выполнения субъектов от класса `ActorService` (C#) или `FabricActorService` (Java) и может использовать ваши собственные методы.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
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
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

#### <a name="implementing-actor-backup-and-restore"></a>Реализация резервного копирования и восстановления субъектов
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
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```


В этом примере `IMyActorService` — это контракт удаленного взаимодействия, который реализует класс `IService` (C#) и `Service` (Java), а затем реализуется классом `MyActorService`. При добавлении удаленного контракта методы в классе `IMyActorService` становятся доступными для клиента за счет создания прокси-сервера удаленного взаимодействия с помощью метода `ActorServiceProxy`.

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

## <a name="application-model"></a>Модель приложения
Службы субъектов — это службы Reliable Services, поэтому используют точно такую же модель приложений. В то же время инструменты сборки платформы субъектов создают для вас некоторые файлы модели приложений.

### <a name="service-manifest"></a>Манифест службы
Содержимое файла ServiceManifest.xml для службы субъектов создается инструментами сборки платформы субъектов автоматически. Этот файл содержит:

* Тип службы субъектов. Имя типа создается с учетом имени проекта субъекта. В зависимости от атрибута сохраняемости у субъекта устанавливается флаг HasPersistedState.
* Пакет кода.
* Пакет конфигурации.
* Ресурсы и конечные точки.

### <a name="application-manifest"></a>Манифест приложения
Средства сборки платформы субъектов автоматически создают для вашей службы субъектов определение по умолчанию. Свойства службы по умолчанию заполняются инструментами сборки:

* Счетчик наборов реплик определяется атрибутом сохраняемости у субъекта. Каждый раз, когда атрибут сохраняемости у субъекта изменяется, счетчик наборов реплик в определении службы по умолчанию сбрасывается.
* Устанавливается схема секционирования и диапазон Uniform Int64 с полным диапазоном ключей Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Основные понятия о секции Service Fabric для субъектов
Службы субъектов — это секционированные службы с отслеживанием состояния. Каждая секция службы субъектов содержит набор субъектов. Секции службы автоматически распределяются между несколькими узлами Service Fabric. В результате распространяются экземпляры субъектов.

![Секционирование и распределение субъектов][5]

Службы Reliable Services создаются с использованием различных схем и диапазонов ключей секционирования. Служба субъектов использует схему секционирования Int64 с полным диапазоном ключей Int64 для сопоставления субъектов и секций.

### <a name="actor-id"></a>Идентификатор субъекта
Каждый созданный в службе субъект имеет уникальный идентификатор, представляемый классом `ActorId` . `ActorId` — это непрозрачное значение идентификатора, которое можно использовать для равномерного распределения субъектов между секциями служб за счет генерации идентификаторов случайным образом:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Каждый `ActorId` хэшируется в значение типа Int64, поэтому в службе субъектов необходимо использовать схему секционирования Int64 с полным диапазоном ключей Int64. Тем не менее для `ActorID` можно использовать пользовательские значения идентификаторов, включая строковые значения, а также GUID или UUID и значения типа Int64.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

При использовании строк и GUID или UUID значения хэшируются в значения типа Int64. Однако, если значение типа Int64 предоставляется в `ActorId` напрямую, Int64 сопоставляется с секцией напрямую без дополнительного хэширования. Этот метод можно использовать для управления тем, в каких секциях будут размещаться субъекты.

## <a name="next-steps"></a>Дальнейшие действия
* [Управление состоянием субъекта](service-fabric-reliable-actors-state-management.md)
* [Жизненный цикл субъектов и сбор мусора](service-fabric-reliable-actors-lifecycle.md)
* [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Пример кода .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Пример кода Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

