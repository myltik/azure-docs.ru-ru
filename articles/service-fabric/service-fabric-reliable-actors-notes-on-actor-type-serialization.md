<properties
   pageTitle="Reliable Actors: примечания о сериализации типов субъектов | Microsoft Azure"
   description="В этой статье приведены сведения о базовых требованиях к определению сериализуемых классов, которые можно использовать для определения интерфейсов и состояний Reliable Actors в Service Fabric."
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
   ms.date="07/06/2015"
   ms.author="vturecek"/>

# Примечания о сериализации типов надежных субъектов Service Fabric


Аргументы всех методов, типы результатов задач, возвращаемых каждым методом в интерфейсе субъекта, и объекты, хранящиеся в диспетчере состояния субъекта, должны быть [сериализуемыми в контракт данных](https://msdn.microsoft.com/library/ms731923.aspx). Это также относится к аргументам методов, определенных в [интерфейсах событий субъекта](service-fabric-reliable-actors-events.md#actor-events). (Методы интерфейсов для событий субъектов всегда возвращают значение void.)

## Пользовательские типы данных

В этом примере приведенный ниже интерфейс субъекта определяет метод, возвращающий пользовательский тип данных `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

Этот интерфейс реализован субъектом, который использует диспетчер состояния для хранения объекта `VoicemailBox`.

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

В этом примере объект `VoicemailBox` сериализуется в следующих случаях.
 - Объект передается между экземпляром субъекта и вызывающим объектом.
 - Объект сохраняется в диспетчере состояния, где он сохраняется на диск и реплицируются на другие узлы.
 
Платформа Reliable Actors использует сериализацию DataContract. Таким образом, пользовательские объекты данных и их члены должны быть аннотированы атрибутами **DataContract** и **DataMember**, соответственно.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## Дальнейшие действия
 - [Жизненный цикл субъектов и сбор мусора](service-fabric-reliable-actors-lifecycle.md)
 - [Таймеры и напоминания субъекта](service-fabric-reliable-actors-timers-reminders.md)
 - [События субъекта](service-fabric-reliable-actors-events.md)
 - [Повторный вход субъекта](service-fabric-reliable-actors-reentrancy.md)
 - [Полиморфизм субъекта и объектно-ориентированные шаблоны проектирования](service-fabric-reliable-actors-polymorphism.md)
 - [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)

<!---HONumber=AcomDC_0713_2016-->