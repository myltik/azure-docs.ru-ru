<properties
   pageTitle="Шаблон разработки субъектов Azure Service Fabric: распределенные сети и графы"
   description="Шаблон разработки, показывающий, как можно использовать субъекты Service Fabric для моделирования приложения с помощью распределенных сетей и графов."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Шаблон разработки надежных субъектов: распределенные сети и графы
Надежные субъекты Service Fabric очень удобны для моделирования комплексных решений на основе связей, в которых связи моделируются с помощью объектов.

![][1]

Как видно из диаграммы, смоделировать пользователя как экземпляр субъекта (узел в сети) не представляет сложности. Например, лента друзей (иногда ее называют проблемой подписчиков) позволяет пользователям просматривать обновления статусов лиц, с которыми пользователи связаны (так же как в Facebook или Twitter). Модель субъектов предоставляет гибкий подход к решению проблемы материализации. Мы можем заполнять ленту друзей в момент возникновения события, обновляя ее для всех друзей в момент публикации обновления, как показано ниже.

![][2]


## Образец кода смарт-кэша: лента друзей в социальной сети (время события)

Образец кода для заполнения ленты друзей

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : Actor, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

В качестве альтернативы вы можете смоделировать субъекты, чтобы развернуть и скомпилировать ленту друзей во время запроса (другими словами, когда пользователь открывает свою ленту). Третий способ — выполнять материализацию ленты друзей по таймеру, например каждые 5 минут. И еще один вариант — оптимизировать модель, объединив обработку по времени события и по времени запроса с моделью на основе таймера, учитывающую привычки пользователя (например, как часто он входит в систему или публикует обновления). При моделировании субъекта в социальной сети следует учитывать «суперпользователей», то есть пользователей с миллионами подписчиков. Разработчикам необходимо моделировать состояние и поведение таких пользователей в соответствии с конкретной ситуацией. Аналогичным образом можно моделировать действие, при котором большое количество субъектов подключаются к одному субъекту действия (звездообразная топология). Примером может служить групповой чат или хостинг игры. Рассмотрим пример с групповым чатом. Несколько участников создают субъект группового чата, который может передавать сообщения от одного участника в группу, как показано в следующем примере.

## Образец кода смарт-кэша: групповой чат

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : Actor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : Actor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

Код в примере использует надежные субъекты, чтобы разрешить любому субъекту обращаться по идентификатору к любому другому субъекту в кластере и взаимодействовать с ним без проблем с размещением, адресацией, кэшированием, отправкой сообщений, сериализацией или маршрутизацией.

## Дальнейшие действия
[Шаблон: смарт-кэш](service-fabric-reliable-actors-pattern-smart-cache.md)

[Шаблон: управление ресурсами](service-fabric-reliable-actors-pattern-resource-governance.md)

[Шаблон: построение службы с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Шаблон: Интернет вещей](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Шаблон: распределенные вычисления](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=Oct15_HO3-->