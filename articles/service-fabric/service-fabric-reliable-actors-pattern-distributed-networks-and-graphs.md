<properties
   pageTitle="Шаблон: распределенные сети и графы | Microsoft Azure"
   description="Конструктивный шаблон, предусматривающий использование субъектов Reliable Actors, предоставляемых платформой Service Fabric, для моделирования приложения с помощью распределенных сетей и графов."
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
   ms.date="09/29/2015"
   ms.author="claudioc"/>

# Конструктивный шаблон Reliable Actors: распределенные сети и графы
Платформа Azure Service Fabric предоставляет модель программирования с использованием субъектов Reliable Actors. Эта модель удобна не только при моделировании комплексных решений с учетом связей, но и при моделировании этих связей в качестве объектов.

![Моделирование с использованием субъектов Reliable Actors в Azure Service Fabric][1]

Как видно на схеме, моделирование пользователя как экземпляра субъекта (узел в сети) не представляет сложности. Например, лента друзей (иногда ее называют "проблемой подписчиков") позволяет пользователям просматривать обновления статусов людей, с которыми эти пользователи связаны (как в Facebook или Twitter).

Модель Reliable Actors предоставляет гибкий подход к решению проблемы материализации. Мы можем заполнять ленту друзей во время события, обновляя ее для всех друзей во время публикации обновления, как показано ниже.

![Модель Reliable Actors и заполнение ленты друзей][2]


## Образец кода смарт-кэша: лента друзей в социальной сети (время события)

Образец кода для заполнения ленты друзей:

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

public class SocialPerson : StatefulActor<SocialPersonState>, ISocialPerson
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

В качестве альтернативы вы можете смоделировать субъекты, чтобы развернуть и скомпилировать ленту друзей во время запроса (когда пользователь открывает свою ленту). Можно также отображать ленту друзей по таймеру (например, каждые пять минут). И еще один вариант — оптимизировать модель, объединив обработку по времени события и по времени запроса с моделью на основе таймера. Эта модель учитывает привычки пользователя (например, как часто он входит в систему или публикует обновления).

При моделировании субъекта в социальной сети следует учитывать пользователей с миллионами подписчиков. Разработчикам следует моделировать состояние и поведение таких пользователей в соответствии с более жесткими требованиями.

Также можно моделировать действие, при котором большое количество субъектов пользователей подключаются к одному субъекту действия (веерная топология). Два примера — групповой чат и размещение игр. Рассмотрим пример с групповым чатом. Несколько участников создают субъект группового чата, который может передавать сообщения от одного участника в группу. Это показано в следующем примере:

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

public class GroupChatParticipant : StatefulActor<GroupChatParticipantState>, IGroupParticipant
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


public class GroupChat : StatefulActor<GroupChatState>, IGroupChat
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

В рамках этого подхода модель Reliable Actors разрешает любому субъекту обращаться к другим субъектам в кластере по идентификатору. Для обеспечения взаимодействия субъектов вам не нужно ломать голову над размещением, адресацией, кэшированием, обменом сообщениями, сериализацией или маршрутизацией.

## Дальнейшие действия
[Конструктивный шаблон Reliable Actors: смарт-кэш](service-fabric-reliable-actors-pattern-smart-cache.md)

[Конструктивный шаблон Reliable Actors: управление ресурсами](service-fabric-reliable-actors-pattern-resource-governance.md)

[Конструктивный шаблон Reliable Actors в Service Fabric: построение служб с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Шаблон: Интернет вещей](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Конструктивный шаблон Reliable Actors: распределенные вычисления](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Конструктивный шаблон Reliable Actors: некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

[Общие сведения о субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=AcomDC_0121_2016-->