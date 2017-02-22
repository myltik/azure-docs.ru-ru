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
ms.date: 08/30/2016
ms.author: amanbha
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: 92df9505416c5b4326f007dbf4b920f2c7ec3bd8


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

Объявите события, публикуемые субъектом, в интерфейсе субъекта.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
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

Также на стороне клиента создайте прокси-сервер для субъекта, который публикует события, и подпишитесь на его события.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

В случае отработки отказа субъект может переключиться на другой процесс или узел. Прокси-сервер субъекта управляет активными подписками и автоматически повторно выполняет подписывание на них. Чтобы изменить интервал между повторными операциями подписывания, используйте API `ActorProxyEventExtensions.SubscribeAsync<TEvent>` . Для отмены подписки используйте API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` .

На стороне субъекта достаточно просто публиковать события, когда они происходят. При наличии подписчиков на событие субъекта среда выполнения будет отправлять им уведомление.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Дальнейшие действия
* [Повторный вход субъекта](service-fabric-reliable-actors-reentrancy.md)
* [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Пример кода](https://github.com/Azure/servicefabric-samples)




<!--HONumber=Jan17_HO4-->


