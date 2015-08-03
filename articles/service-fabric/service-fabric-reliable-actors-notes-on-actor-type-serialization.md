<properties
   pageTitle="Примечания о сериализация типов субъектов Azure Service Fabric"
   description="Базовые требования к определению сериализуемых классов, которые можно использовать для определения интерфейсов и состояний надежных субъектов в Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/02/2015"
   ms.author="claudioc"/>


# Примечания о сериализации типов субъектов в Azure Service Fabric

Есть важный аспект, который необходимо иметь в виду при определении интерфейсов и состояний субъектов: они должны относиться к типу сериализуемых контрактов данных. Дополнительные сведения о контрактах данных см. на веб-сайте [MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## Типы, используемые в интерфейсах субъектов

Аргументы всех методов и результат задачи, возвращаемой каждым методом, определенным в [интерфейсе субъекта](service-fabric-reliable-actors-introduction.md#actors), должны относиться к типу сериализуемых контрактов данных. Это также относится к аргументам методов, определенных в [интерфейсах событий субъектов](service-fabric-reliable-actors-events.md#actor-events). (Методы интерфейсов для событий субъектов всегда возвращают void.) Например, интерфейс `IVoiceMail` определяет метод так:

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

В этом случае `List<T>` относится к стандартному типу .NET, который уже является сериализуемым контрактом данных. Тип `Voicemail` должен быть сериализуемым контрактом данных.

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

Состояние субъекта должно быть сериализуемым контрактом данных. Например, у нас есть определение класса субъекта (см. пример ниже).

```csharp

public class VoiceMailActor : Actor<VoicemailBox>, IVoiceMail
{
...

```

В этом случае класс состояния будет определен с помощью класса и его членов, аннотированных атрибутами DataContract и DataMember соответственно.

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
 

<!---HONumber=July15_HO4-->