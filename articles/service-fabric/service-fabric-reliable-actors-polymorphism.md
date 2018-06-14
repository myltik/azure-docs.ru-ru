---
title: Полиморфизм на платформе Reliable Actors | Документация Майкрософт
description: Создание иерархий интерфейсов .NET и типов на платформе Reliable Actors для многократного использования функций и определений API-интерфейсов.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f03330bbbf2c1c7f92310211058f7ef8858f1941
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209344"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Полиморфизм на платформе надежных субъектов
Платформа Reliable Actors позволяет создавать субъекты с использованием многих приемов, применяемых в объектно-ориентированном проектировании. Одним из них является полиморфизм, благодаря которому типы и интерфейсы могут наследоваться от более обобщенных родительских элементов. Наследование на платформе надежных субъектов обычно соответствует модели .NET с несколькими дополнительными ограничениями. В случае Java или Linux наследование соответствует модели Java.

## <a name="interfaces"></a>Интерфейсы
Для использования платформы надежных субъектов требуется определить по крайней мере один интерфейс, который будет реализован типом субъекта. Этот интерфейс применяется для создания прокси-класса, используемого клиентами для взаимодействия с вашими субъектами. Интерфейсы могут наследовать от других интерфейсов, пока каждый интерфейс реализуется типом субъекта и все его родительские элементы в конечном счете являются производными от IActor(C#) или Actor(Java). IActor(C#) и Actor(Java) — это определенные платформой базовые интерфейсы для субъектов на платформах .NET и Java соответственно. Таким образом, классический пример полиморфизма, использующий фигуры, может выглядеть примерно следующим образом:

![Иерархия интерфейса для формирования субъектов][shapes-interface-hierarchy]

## <a name="types"></a>Типы
Также можно создать иерархию типов субъектов, производных от базового класса Actor, предоставляемого платформой. В случае использования фигур у вас может быть базовый тип `Shape`(C#) или `ShapeImpl`(Java).

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Подтипы `Shape`(C#) или `ShapeImpl`(Java) могут переопределять методы из базового типа.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Обратите внимание на атрибут `ActorService` в типе субъекта. Этот атрибут сообщает платформе Reliable Actors, что ей необходимо автоматически создать службу для размещения субъектов этого типа. В некоторых случаях может потребоваться создать базовый тип, который предназначен исключительно для использования функциональных возможностей совместно с подтипами и никогда не будет использоваться для создания конкретных субъектов. Здесь следует применять ключевое слово `abstract` , указывающее, что вы никогда не будете создавать субъект на основе этого типа.

## <a name="next-steps"></a>Дополнительная информация
* Узнайте, [каким образом платформа Reliable Actors использует платформу Service Fabric](service-fabric-reliable-actors-platform.md) для обеспечения надежности, масштабируемости и согласованности состояния.
* Узнайте о [жизненном цикле субъекта](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
