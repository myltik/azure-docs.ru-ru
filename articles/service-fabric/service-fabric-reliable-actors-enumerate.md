---
title: Перечисление субъектов в Azure Service Fabric | Документация Майкрософт
description: Узнайте, как перечислить Reliable Actors и их метаданные.
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
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 037138f68e5c18822e4d7b3fa47591411e8a2407
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Перечисление Reliable Actors в Service Fabric
Служба Reliable Actors позволяет клиенту перечислять метаданные о размещенных службой субъектах. Так как служба субъектов — это секционированная служба с отслеживанием состояния, перечисление выполняется по каждому разделу. Так как каждая секция может содержать большое количество субъектов, перечисление возвращается как набор постраничных результатов. Страницы зацикливаются до тех пор, пока все они не будут прочитаны. В следующем примере показано, как создать список всех активных субъектов в одной секции службы субъектов:

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
