---
title: "События в микрослужбах Azure на основе субъектов | Документация Майкрософт"
description: "Общие сведения о событиях для Reliable Actors Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 779d39e2910d0de25ee07b8fae3ca7a0e9772776
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2017
---
# <a name="actor-events"></a>События субъекта
События субъекта — это способ отправки уведомлений от субъекта клиентам без гарантии доставки. Они предназначены для взаимодействия между субъектом и клиентами, и их не следует использовать для обмена данными между субъектами.

В следующих фрагментах кода показано, как использовать события субъекта в приложении.

Определите интерфейс, который описывает события, публикуемые субъектом. Он должен быть производным от интерфейса `IActorEvents` . Аргументы методов должны относиться к типу [сериализуемых контрактов данных](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Методы не должны ничего возвращать, так как уведомления являются однонаправленными и их доставка не гарантируется.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Объявите события, публикуемые субъектом, в интерфейсе субъекта.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
На стороне клиента реализуйте обработчик событий.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Также на стороне клиента создайте прокси-сервер для субъекта, который публикует события, и подпишитесь на его события.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

В случае отработки отказа субъект может переключиться на другой процесс или узел. Прокси-сервер субъекта управляет активными подписками и автоматически повторно выполняет подписывание на них. Чтобы изменить интервал между повторными операциями подписывания, используйте API `ActorProxyEventExtensions.SubscribeAsync<TEvent>` . Для отмены подписки используйте API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` .

На стороне субъекта достаточно публиковать события, когда они происходят. При наличии подписчиков на событие субъекта среда выполнения отправляет им уведомление.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Дальнейшие действия
* [Повторный вход субъекта](service-fabric-reliable-actors-reentrancy.md)
* [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Пример кода C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Пример кода C# .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Пример кода Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)
