<properties
   pageTitle="Стек связи WCF Reliable Services | Microsoft Azure"
   description="Встроенный стек связи WCF в Service Fabric обеспечивает связь со службой клиента WCF для Reliable Services."
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

# Коммуникационный стек WCF для надежных служб
Платформа надежных служб Reliable Services позволяет разработчикам служб решать, какой стек связи следует использовать в службе. Любой стек связи можно подключить с помощью интерфейса **ICommunicationListener**, возвращаемого методами [CreateServiceReplicaListeners или CreateServiceInstanceListeners](service-fabric-reliable-service-communication.md). Платформа предоставляет реализацию стека связи на основе Windows Communication Foundation (WCF) для разработчиков служб, которым требуется использовать связь на основе WCF.

## Прослушиватель связи WCF
Реализация интерфейса **ICommunicationListener**, ориентированная на WCF, обеспечивается классом **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(ServiceInitializationParameters,typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
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
Для написания клиентов для взаимодействия со службами с помощью WCF платформа предоставляет объект **WcfClientCommunicationFactory**, который является ориентированной на WCF реализацией [ClientCommunicationFactoryBase](service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

К каналу связи WCF можно обращаться из клиента **WcfCommunicationClient**, созданного объектом **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

Клиентский код может использовать **WcfCommunicationClientFactory** вместе с **ServicePartitionClient** для определения конечной точки службы и взаимодействия со службой.

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
    null);          // do not retry Exception types


//
// Create a client for communicating with the calc service that has been created with the
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

<!---HONumber=AcomDC_0128_2016-->