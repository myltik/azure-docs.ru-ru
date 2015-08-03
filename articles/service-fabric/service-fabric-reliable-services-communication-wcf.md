<properties
   pageTitle="Стек связи на основе WCF, предоставляемый интерфейсом API надежных служб"
   description="В этой статье описывается стек связи на основе WCF, предоставляемый интерфейсом API надежных служб."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Стек связи на основе WCF для надежных служб
Платформа надежных служб позволяет разработчикам служб решать, какой стек связи следует использовать в службе. Они могут подключить выбранный стек связи посредством объекта `ICommunicationListener`, возвращаемого методом [`CreateCommunicationListener`](../service-fabric-reliable-service-communication.md). Платформа предоставляет реализацию стека связи на основе WCF для разработчиков служб, которым требуется использовать связь на основе WCF.

## Прослушиватель связи WCF
Ориентированная на WCF реализация `ICommunicationListener` предоставляется классом `WcfCommunicationListener`.

```csharp

public WcfCommunicationListener(
    Type communicationInterfaceType,
    Type communicationImplementationType);

protected override ICommunicationListener CreateCommunicationListener()
    {
        WcfCommunicationListener communicationListener = new WcfCommunicationListener(typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // which identifies the endpoint that the wcf servicehost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        };

        return communicationListener;
    }

```

## Написание клиентов для стека связи WCF
Для написания клиентов для взаимодействия со службами с помощью WCF платформа предоставляет `WcfClientCommunicationFactory` — ориентированную на WCF реализацию [`ClientCommunicationFactoryBase`](../service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

Доступ к каналу связи WCF можно получить из объекта `WcfCommunicationClient`, созданного `WcfCommunicationClientFactory`

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

Чтобы определить конечную точку службы и обеспечить взаимодействие со службой, в клиентском коде можно использовать `WcfCommunicationClientFactory` вместе с `ServicePartitionClient`.

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // donot retry Exception types


//
// Create a client for communicating with the calc service which has been created with
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;


```
 

<!---HONumber=July15_HO4-->