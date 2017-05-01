---
title: "Удаленное управление службой в Azure Service Fabric | Документация Майкрософт"
description: "Удаленное взаимодействие Service Fabric позволяет осуществлять обмен данными между клиентами и службами с помощью удаленного вызова процедур."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8e06b3f2f6347468b197f2e90912a5d0facc5404
ms.lasthandoff: 04/03/2017


---
# <a name="service-remoting-with-reliable-services"></a>Удаленное взаимодействие службы с Reliable Services
> [!div class="op_single_selector"]
> * [C# в Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java в Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Для служб, которые не привязаны к определенному протоколу обмена данными или стеку, например веб-API, Windows Communication Foundation (WCF) или др., платформа Reliable Services предоставляет механизм удаленного взаимодействия для быстрой и простой настройки удаленного вызова процедур.

## <a name="set-up-remoting-on-a-service"></a>Настройка удаленного доступа в службе
Процесс настройки удаленного доступа для службы состоит из двух простых этапов.

1. Создание интерфейса для реализации в службе. Этот интерфейс определяет методы, которые будут доступны для удаленного вызова процедур в службе. Эти методы должны быть асинхронными методами, возвращающими задачи. Интерфейс должен реализовать `Microsoft.ServiceFabric.Services.Remoting.IService` , чтобы показать, что служба имеет интерфейс удаленного взаимодействия.
2. Используйте прослушиватель удаленного взаимодействия в службе. Это реализация `ICommunicationListener` , которая предоставляет возможности удаленного взаимодействия. Пространство имен `Microsoft.ServiceFabric.Services.Remoting.Runtime` содержит метод расширения `CreateServiceRemotingListener` для служб без отслеживания и с отслеживанием состояния, который можно использовать, чтобы создать прослушиватель удаленного взаимодействия с использованием транспортного протокола удаленного взаимодействия по умолчанию.

Например, приведенная ниже служба без отслеживания состояния предоставляет один метод для получения "Hello World" посредством удаленного вызова процедуры.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>
            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Аргументы и возвращаемые данные в интерфейсе службы могут иметь простые, сложные или настраиваемые типы, однако они должны быть сериализуемыми с помощью объекта .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Вызов удаленных методов службы
Вызов методов в службе с помощью стека удаленного взаимодействия осуществляется с помощью локального прокси-сервера для службы через класс `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . Метод `ServiceProxy` создает локальный прокси-сервер, используя тот же интерфейс, который реализует служба. С помощью этого прокси можно без труда удаленно вызвать методы в интерфейсе.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Платформа удаленного взаимодействия распространяет исключения, созданные в службе, на клиент. Поэтому логика обработки исключений на стороне клиента с использованием `ServiceProxy` может напрямую обрабатывать порождаемые службой исключения.

## <a name="next-steps"></a>Дальнейшие действия
* [Веб-API с OWIN в модели Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Взаимодействие WCF с Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Защита обмена данными для Reliable Services](service-fabric-reliable-services-secure-communication.md)

