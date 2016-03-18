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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Примечания о сериализации типов надежных субъектов Service Fabric

При определении интерфейсов и состояний субъектов учитывайте следующее. Типы должны поддерживать сериализацию контрактов данных. Дополнительные сведения о контрактах данных см. на веб-сайте [MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## Типы интерфейса субъекта

Аргументы всех методов и типы результатов задач, которые возвращаются каждым методом, определенным в [интерфейсе субъекта](service-fabric-reliable-actors-introduction.md#actors), должны поддерживать сериализацию контрактов данных. Это также относится к аргументам методов, определенных в [интерфейсах событий субъектов](service-fabric-reliable-actors-events.md#actor-events). (Методы интерфейсов для событий субъектов всегда возвращают значение void.) Например, интерфейс `IVoiceMail` определяет метод так:

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

В этом случае `List<T>` относится к стандартному типу .NET, который уже является сериализуемым контрактом данных. Тип `Voicemail` также должен быть сериализуемым контрактом данных.

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

## Класс состояния субъекта

Состояние субъекта должно быть сериализуемым контрактом данных. Например, определение класса субъекта может выглядеть так:

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

В этом случае класс состояния будет определен с помощью класса и его членов, аннотированных атрибутами **DataContract** и **DataMember** соответственно.

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

<!---HONumber=AcomDC_0121_2016-->