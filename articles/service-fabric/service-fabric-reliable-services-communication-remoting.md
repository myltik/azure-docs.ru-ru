<properties
   pageTitle="Удаленное взаимодействие службы в Service Fabric | Microsoft Azure"
   description="Удаленное взаимодействие Service Fabric позволяет осуществлять обмен данными между клиентами и службами с помощью удаленного вызова процедур."
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
   ms.date="11/12/2015"
   ms.author="bharatn@microsoft.com"/>

# Удаленное взаимодействие службы с Reliable Services
Для служб, которые не привязаны к определенному протоколу обмена данными или стеку, например WebAPI, Windows Communication Foundation (WCF) или другим, платформа предоставляет механизм удаленного взаимодействия для быстрой и простой настройки удаленного вызова процедур.

## Настройка удаленного доступа в службе
Процесс настройки удаленного доступа для службы состоит из двух простых этапов.

1. Создание интерфейса для реализации в службе. Этот интерфейс определяет методы, которые будут доступны для удаленного вызова процедур в службе. Эти методы должны быть асинхронными методами, возвращающими задачи. Интерфейс должен реализовать `Microsoft.ServiceFabric.Services.Remoting.IService`, чтобы показать, что служба имеет интерфейс удаленного взаимодействия.
2. Использование `FabricTransportServiceRemotingListener` в службе. Это реализация `ICommunicationListener`, которая предоставляет возможности удаленного взаимодействия.

Например, эта служба Hello World предоставляет один метод для получения "Hello World" посредством удаленного вызова процедур:

```csharp
public interface IHelloWorldStateful : IService
{
    Task<string> GetHelloWorld();
}

internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]{
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this))};
    }

    public Task<string> GetHelloWorld()
    {
        return Task.FromResult("Hello World!");
    }
}

```
> [AZURE.NOTE] Аргументы и возвращаемые типы в интерфейсе службы могут иметь простые, сложные или настраиваемые типы, однако они должны быть сериализуемыми с помощью объекта .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).


## Вызов удаленных методов службы
Вызов методов в службе с помощью удаленного взаимодействия стека осуществляется с помощью локального прокси к службе через класс `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`. Метод `ServiceProxy` создает локальный прокси, используя тот же интерфейс, который реализует служба. С помощью этого прокси можно без труда удаленно вызвать методы в интерфейсе.


```csharp

IHelloWorldStateful helloWorldClient = ServiceProxy.Create<IHelloWorldStateful>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

Платформа удаленного взаимодействия распространяет исключения, созданные в службе, на клиент. Поэтому логика обработки исключений на стороне клиента с использованием `ServiceProxy` может напрямую обрабатывать создаваемые службой исключения.

## Дальнейшие действия

* [Веб-API с OWIN в модели Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Взаимодействие WCF с Reliable Services](service-fabric-reliable-services-communication-wcf.md)

* [Защита обмена данными для Reliable Services](service-fabric-reliable-services-secure-communication.md)

<!---HONumber=AcomDC_0330_2016-->