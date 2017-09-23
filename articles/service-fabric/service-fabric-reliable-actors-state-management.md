---
title: "Управление состоянием Reliable Actors | Документация Майкрософт"
description: "Описание управления, сохранения и репликации субъектов Reliable Actors для обеспечения высокого уровня доступности."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: aca8cf2b94e8b746a5cac6af021c7221a29b7345
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---
# <a name="reliable-actors-state-management"></a>Управление состоянием субъектов Reliable Actors
Субъекты Reliable Actors — это однопотоковые объекты для инкапсуляции логики и состояния. Так как субъекты выполняются в службах Reliable Services, они могут поддерживать состояние, используя те же механизмы сохранения и репликации, которые применяются службами Reliable Services. При этом субъекты не теряют свое состояние после сбоев, повторной активации после сборки мусора или перемещения между узлами в кластере из-за балансировки ресурсов или обновления.

## <a name="state-persistence-and-replication"></a>Сохранение состояния и репликация
Все субъекты Reliable Actors *отслеживают состояние* , так как каждый экземпляр субъекта сопоставляется с уникальным идентификатором. Это означает, что повторные вызовы одного идентификатора субъекта направляются в один и тот же экземпляр субъекта. Это отличается от системы без отслеживания состояния, в которой не гарантируется, что клиентские вызовы будут каждый раз направляться на один и тот же сервер. Поэтому службы субъектов всегда отслеживают состояние.

Несмотря на то что субъекты отслеживают состояние, это не означает, что они надежно хранят состояние. Субъекты могут выбрать уровень сохранения и репликации состояния на основе их требований к хранению данных:

* **Сохраняемое состояние:** состояние сохраняется на диске и реплицируется на 3 или более реплик. Это самый устойчивый вариант хранения состояния, при котором оно может сохраниться даже после полного сбоя кластера.
* **Непостоянное состояние:** состояние реплицируется на 3 или более реплик и хранится только в памяти. Это обеспечивает устойчивость в случае сбоя узла, сбоя субъекта и во время обновления и балансировки ресурсов. Однако состояние не сохраняется на диск, поэтому если все реплики будут потеряны одновременно, состояние также теряется.
* **Несохраняемое состояние:** состояние не реплицируется и не записывается на диск. Этот уровень предназначен для субъектов, которым просто не требуется надежно хранить состояние.

Каждый уровень сохраняемости — это просто другая конфигурация *поставщика состояний* и *репликации* службы. Записывается ли состояние на диск, зависит от поставщика состояний — компонента Reliable Service, который хранит состояние. А репликация зависит от того, на скольких репликах развертывается служба. Как и в случае со службами Reliable Services, поставщик состояний и число реплик можно легко настроить вручную. Платформа субъектов предоставляет атрибут, который при использовании с субъектом автоматически выбирает поставщик состояний по умолчанию и создает параметры числа реплик для достижения одного из этих трех параметров сохраняемости. Производный класс не наследует атрибут StatePersistence, каждый тип субъекта должен предоставлять собственный уровень StatePersistence.

### <a name="persisted-state"></a>Сохраненное состояние
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Этот параметр использует поставщик состояний, который хранит данные на диске, и автоматически задает 3 реплики службы.

### <a name="volatile-state"></a>Непостоянное состояние
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Этот параметр использует поставщик состояний только в памяти и настраивает 3 реплики.

### <a name="no-persisted-state"></a>Несохраняемое состояние
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Этот параметр использует поставщик состояний только в памяти и настраивает 1 реплику.

### <a name="defaults-and-generated-settings"></a>Значения по умолчанию и созданные параметры
При использовании атрибута `StatePersistence` поставщик состояний автоматически выбирается во время выполнения при запуске службы субъекта. Однако число реплик задается средствами построения субъекта Visual Studio во время компиляции. Средства построения автоматически создают *службу по умолчанию* для службы субъекта в ApplicationManifest.xml. Параметры создаются для **минимального размера набора реплик** и **целевого размера набора реплик**.

Вы можете изменить эти параметры вручную. Однако при каждом изменении атрибута `StatePersistence` параметрам будут присвоены значения размера набора реплик по умолчанию для выбранного атрибута `StatePersistence`, а все предыдущие значения будут переопределены. Другими словами, указанные в файле ServiceManifest.xml значения будут переопределены *только* во время сборки при изменении значения атрибута `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Диспетчер состояний:
У каждого экземпляра субъекта есть собственный диспетчер состояний: структура данных, подобная словарю, которая надежно хранит пары "ключ — значение". Диспетчер состояний — это оболочка вокруг поставщика состояний. Его можно использовать для хранения данных независимо от того, какой параметр сохраняемости применяется, но он не предоставляет никаких гарантий, что для работающей службы субъекта можно изменить параметр непостоянного состояния (только в памяти) на сохраняемое состояние с помощью последовательного обновления при сохранении данных. Тем не менее, можно изменить количество реплик для запущенной службы.

Ключи диспетчера состояний должны быть строками. Значения являются универсальными и могут быть любого типа, в том числе пользовательского. Значения, хранящиеся в диспетчере состояний, должны быть сериализуемыми контрактом данных, так как они могут передаваться по сети другим узлам во время репликации и быть записаны на диск в зависимости от параметра сохранения состояния субъекта.

Диспетчер состояний предоставляет общие методы словаря для управления состоянием, аналогичные используемым в надежном словаре.

### <a name="accessing-state"></a>Доступ к состоянию
Доступ к состоянию можно получить через диспетчер состояний по ключу. Методы диспетчера состояний асинхронны, так как они могут потребовать дискового ввода-вывода, если у субъектов сохраняемое состояние. При первом доступе объекты состояния кэшируются в памяти. Повторные операции доступа к объектам обращаются к объектам непосредственно из памяти и возвращают данные синхронно без дискового ввода-вывода или асинхронного переключения контекста. Объект состояния удаляется из кэша в следующих случаях:

* Метод субъекта вызывает необработанное исключение после извлечения объекта из диспетчера состояний.
* Субъект повторно активируется после отключения или из-за сбоя.
* Поставщик состояния записывает состояние на диск. Поведение зависит от реализации поставщика состояний. Поставщик состояний по умолчанию для параметра `Persisted` применяет такое поведение.

Получить состояние можно с помощью стандартной операции *Get*, которая порождает исключение `KeyNotFoundException`(C#) или `NoSuchElementException`(Java), если для ключа запись не существует.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Получить состояние можно также используя метод *TryGet*. Он не вызывает исключение, если для ключа запись не существует.

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

### <a name="saving-state"></a>Сохранение состояния
Методы извлечения диспетчера состояний возвращают ссылку на объект в локальной памяти. Изменение этого объекта только в локальной памяти не приводит к его длительному сохранению. Когда объект извлекается из диспетчера состояний и изменяется, его необходимо снова вставить в диспетчер состояний для постоянного сохранения.

Состояние можно вставить с помощью безусловной операции *Set*, которая эквивалента синтаксису `dictionary["key"] = value`.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Состояние можно добавить с помощью метода *Add*, который порождает исключение `InvalidOperationException`(C#) или `IllegalStateException`(Java) при попытке добавить ключ, который уже существует.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Состояние также можно добавить с помощью метода *TryAdd*, который не вызывает исключение при попытке добавить уже существующий ключ.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

В конце метода субъекта диспетчер состояний автоматически сохраняет все значения, которые были добавлены или изменены с помощью операции вставки или обновления. "Сохранение" может включать в себя сохранение на диск и репликацию в зависимости от используемых параметров. Значения, которые не были изменены, не сохраняются и не реплицируются. Если значения не были изменены, операция сохранения не выполняет никаких действий. При сбое сохранения измененное состояние удаляется и загружается исходное состояние.

Состояние также можно сохранить вручную, вызвав метод `SaveStateAsync` в базовом субъекте:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

### <a name="removing-state"></a>Удаление состояния
Состояние можно удалить из диспетчера состояний субъекта без возможности восстановления, вызвав метод *Remove*. Этот метод вызывает исключение `KeyNotFoundException`(C#) или `NoSuchElementException`(Java) при попытке удалить ключ, который не существует.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Состояние также можно удалить без возможности восстановления с помощью метода *TryRemove*, который не вызывает исключение при попытке удалить несуществующий ключ.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Состояние, которое хранится в Reliable Actors, должно быть сериализовано перед записью на диск и реплицировано для обеспечения высокого уровня доступности. Узнайте больше о [сериализации типа субъекта](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Затем ознакомьтесь с [диагностикой и мониторингом производительности субъекта](service-fabric-reliable-actors-diagnostics.md).

