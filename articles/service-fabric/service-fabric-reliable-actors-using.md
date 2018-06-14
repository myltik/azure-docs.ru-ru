---
title: Реализация функций в субъектах Azure Service Fabric | Документация Майкрософт
description: В статье описано, как написать собственную службу субъекта, которая реализует функции на уровне службы так же, как это делается при наследовании StatefulService.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 41548c3395fa0c8f56e62cfcfb7338a2d53f040f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212897"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Реализация функций уровня службы в службе субъектов
Как указано в описании[слоев службы](service-fabric-reliable-actors-platform.md#service-layering), сама служба субъекта является надежной службой.  Вы можете написать собственную службу, производную от класса `ActorService`, и реализовать в ней функции уровня службы таким же образом, как при наследовании StatefulService, например:

- резервное копирование и восстановление службы;
- общие функции для всех субъектов, например автоматическое выключение;
- вызовы удаленных взаимодействий для самой службы субъектов, а также для любого отдельного субъекта.

## <a name="using-the-actor-service"></a>Работа со службой субъектов
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

## <a name="actor-service-methods"></a>Методы службы субъектов
Служба субъектов реализует метод `IActorService` (C#) или `ActorService` (Java), который, в свою очередь, реализует метод `IService` (C#) или `Service` (Java). Это интерфейс используется удаленными взаимодействиями служб Reliable Services, что позволяет вызывать удаленные процедуры в методах службы. Он содержит методы уровня службы, которые можно вызывать удаленно с помощью удаленных взаимодействий служб, и позволяет [перечислять](service-fabric-reliable-actors-enumerate.md) и [удалять](service-fabric-reliable-actors-delete-actors.md) субъекты.


## <a name="custom-actor-service"></a>Пользовательская служба субъектов
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

## <a name="implementing-actor-backup-and-restore"></a>Реализация резервного копирования и восстановления субъектов
Пользовательская служба субъектов предоставляет метод для резервного копирования данных субъектов с использованием прослушивателя удаленных взаимодействий, уже присутствующего в `ActorService`.  См. дополнительные сведения о [резервном копировании и восстановлении субъектов](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2-stack"></a>Субъект, использующий стек удаленного взаимодействия версии 2
Теперь с помощью пакета NuGet 2.8 пользователи могут использовать стек удаленного взаимодействия версии 2, который обеспечивает более высокую производительность и предоставляет такие функции, как настраиваемая сериализация. Стек удаленного взаимодействия версии 2 не обеспечивает обратную совместимость с существующим стеком удаленного взаимодействия (который теперь называется стеком удаленного взаимодействия версии 1).

Для использования стека удаленного взаимодействия версии 2 требуется внести следующие изменения.
 1. Добавьте приведенный ниже атрибут сборки в интерфейсы субъекта.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Выполните сборку и обновление проектов службы субъекта и клиента субъекта, чтобы начать использование стека версии 2.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Обновление службы субъекта для использования стека удаленного взаимодействия версии 2 без влияния на доступность службы
Это изменение будет выполнено в 2 этапа. Выполните приведенные действия в указанной последовательности.

1.  Добавьте приведенный ниже атрибут сборки в интерфейсы субъекта. Этот атрибут запустит два прослушивателя для служб субъекта: прослушиватель версии 1 (существующий) и прослушиватель версии 2. Обновите службу субъекта, внеся в нее это изменение.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Обновите клиенты субъекта после завершения обновления, упомянутого выше.
Это гарантирует, что прокси-сервер субъекта будет использовать стек удаленного взаимодействия версии 2.

3. Этот шаг не является обязательным. Измените приведенный выше атрибут, чтобы удалить прослушиватель версии 1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>Дополнительная информация
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
