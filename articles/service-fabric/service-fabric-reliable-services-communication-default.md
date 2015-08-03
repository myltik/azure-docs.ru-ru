<properties
   pageTitle="Стек связи, предоставляемый по умолчанию Service Fabric"
   description="В этой статье описывается стек связи по умолчанию, предоставляемый платформой надежных служб для взаимодействия служб и клиентов."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Стек связи, предоставляемый по умолчанию платформой надежных служб
Для создателей служб, которые не привязаны к конкретной реализации стека связи (WebAPI, WCF и т. д.), платформа предоставляет элементы связи на стороне клиента и службы, с помощью которых можно установить взаимодействие между службой и клиентом.

> [AZURE.NOTE]Для получения возможностей, упомянутых ниже, необходимо выполнить обновление до новейших пакетов Nuget.

## Прослушиватель связи со службами
Прослушиватель связи по умолчанию, предназначенный для службы, реализован в классе `ServiceCommunicationListener`.

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
В интерфейсе, наследуемом от интерфейса `IService`, методы, которые служба реализует и желает предоставить клиентам, определяются как асинхронные методы. Затем служба может просто создать экземпляр объекта `ServiceCommunicationListener` и вернуть его в метод [`CreateCommunicationListener`](service-fabric-reliable-services-communication.md). Например, код службы HelloWorld для настройки данного стека связи можно определить следующим образом.

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]Аргументы и возвращаемые типы в интерфейсе службы, например упомянутого выше класса Message, должны быть сериализуемыми с помощью объекта .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).


## Написание клиентов для взаимодействия с ServiceCommunicationListener
Для того чтобы клиенты могли взаимодействовать со службами с помощью `ServiceCommunicationListener`, платформа предоставляет класс `ServiceProxy`.

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

Клиенты могут создавать экземпляр прокси-объекта службы, который реализует соответствующий интерфейс службы, и вызывать методы прокси-объекта.

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]Распространением исключений, выданных на стороне службы клиенту, занимается платформа связи. Поэтому логика обработки исключений на стороне клиента с использованием ServiceProxy может непосредственно обрабатывать исключения, которые служба потенциально может выдавать.
 

<!---HONumber=July15_HO4-->