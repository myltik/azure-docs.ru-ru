---
title: Стек связи WCF Reliable Services | Документация Майкрософт
description: Встроенный стек связи WCF в Service Fabric обеспечивает связь со службой клиента WCF для Reliable Services.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: f5ca579b446e5d3608d53cea73fa9392cd00db06
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212230"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Коммуникационный стек WCF для надежных служб
Платформа надежных служб Reliable Services позволяет разработчикам служб решать, какой стек связи следует использовать в службе. Любой стек связи можно подключить с помощью интерфейса **ICommunicationListener** , возвращаемого методом [CreateServiceReplicaListeners или CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . Платформа предоставляет реализацию стека связи на основе Windows Communication Foundation (WCF) для разработчиков служб, которым требуется использовать связь на основе WCF.

## <a name="wcf-communication-listener"></a>Прослушиватель связи WCF
Реализация интерфейса **ICommunicationListener**, ориентированная на WCF, обеспечивается классом **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**.

Допустим, имеется контракт службы типа `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Мы можем создать прослушиватель связи WCF в службе, как описано ниже.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Написание клиентов для стека связи WCF
Для написания клиентов для взаимодействия со службами с помощью WCF платформа предоставляет объект **WcfClientCommunicationFactory**, который является ориентированной на WCF реализацией [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

К каналу связи WCF можно обращаться из клиента **WcfCommunicationClient**, созданного объектом **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Клиентский код может использовать **WcfCommunicationClientFactory** вместе с клиентом **WcfCommunicationClient**, который реализует **ServicePartitionClient**, для определения конечной точки службы и взаимодействия со службой.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> Объект ServicePartitionResolver по умолчанию предполагает, что клиент выполняется в том же кластере, что и служба. Если это не так, создайте объект ServicePartitionResolver и передайте конечные точки подключения к кластеру.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
* [Удаленный вызов процедур с использованием удаленного взаимодействия Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Веб-интерфейс API с OWIN в Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Защита обмена данными для Reliable Services](service-fabric-reliable-services-secure-communication.md)

