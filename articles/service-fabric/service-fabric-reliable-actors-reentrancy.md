---
title: "Повторный вход в микрослужбах Azure на основе субъектов | Документация Майкрософт"
description: "Общие сведения о повторном входе для надежных субъектов Service Fabric"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 00fcccb379bf1ba3875fbaba57a05b00fa228622
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017


---
# <a name="reliable-actors-reentrancy"></a>Повторный вход надежных субъектов
Среда выполнения Reliable Actors по умолчанию разрешает повторный вход на основе логического контекста вызова. Это позволяет субъектам выполнять повторный вход, если они находятся в той же цепочке контекста вызова. Например, субъект A отправляет сообщение субъекту B, который отправляет сообщение субъекту C. В ходе обработки сообщения, если субъект C осуществит вызов субъекта A, сообщение будет означать повторный вход, поэтому будет разрешено. Любые другие сообщения, являющиеся частью другого контекста вызова, будут заблокированы на субъекте A до тех пор, пока он не завершит обработку.

Для реализации повторного входа субъектов доступно два варианта (определено в перечислении `ActorReentrancyMode` ):

* `LogicalCallContext` (поведение по умолчанию);
* `Disallowed` — отключает повторный вход.

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
Повторный вход можно настроить в параметрах `ActorService`во время регистрации. Параметр применяется ко всем экземплярам субъекта, созданным в службе субъектов.

В следующем примере показана служба субъекта, которая задает `ActorReentrancyMode.Disallowed`в качестве режима повторного входа. Следовательно, если субъект отправляет сообщение повторного входа другому субъекту, будет выдано исключение типа `FabricException` .

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о возможностях повторного входа в [справочной документации по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx).

