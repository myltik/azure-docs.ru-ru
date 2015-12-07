<properties
   pageTitle="Стек связи WCF Reliable Services | Microsoft Azure"
   description="Встроенный стек связи WCF в Services Fabric обеспечивает связь со службой клиента WCF для Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="bharatn@microsoft.com"/>

# Стек связи на основе WCF для надежных служб
Платформа надежных служб позволяет разработчикам служб решать, какой стек связи следует использовать в службе. Любой стек связи можно подключить с помощью команды `ICommunicationListener`, возвращаемой методами [CreateServiceReplicaListeners или CreateServiceInstanceListeners](service-fabric-reliable-service-communication.md). Платформа предоставляет реализацию стека связи на основе WCF для разработчиков служб, которым требуется использовать связь на основе WCF.

## Прослушиватель связи WCF
Ориентированная на WCF реализация `ICommunicationListener` предоставляется классом `Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener`.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(typeof(ICalculator), this)
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
        }
    )};
}

```

## Написание клиентов для стека связи WCF
Для написания клиентов для взаимодействия со службами с помощью WCF платформа предоставляет `WcfClientCommunicationFactory` — ориентированную на WCF реализацию [`ClientCommunicationFactoryBase`](service-fabric-reliable-service-communication.md).

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
 
## Дальнейшие действия
* [Удаленный вызов процедур с использованием удаленного взаимодействия Reliable Services](service-fabric-reliable-services-communication-remoting.md)

* [Веб-интерфейс API с OWIN в Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_1125_2015-->