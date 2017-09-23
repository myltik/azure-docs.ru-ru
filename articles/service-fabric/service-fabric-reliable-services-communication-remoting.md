
---
title: "Удаленное управление службой в Service Fabric | Документация Майкрософт"
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
ms.date: 04/20/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 92a8894f24c234fbf38eda086531b524cceccfc1
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017

---
# <a name="service-remoting-with-reliable-services"></a>Удаленное взаимодействие службы с Reliable Services
Для служб, которые не привязаны к определенному протоколу обмена данными или стеку, например веб-API, Windows Communication Foundation (WCF) или др., платформа Reliable Services предоставляет механизм удаленного взаимодействия для быстрой и простой настройки удаленного вызова процедур.

## <a name="set-up-remoting-on-a-service"></a>Настройка удаленного доступа в службе
Процесс настройки удаленного доступа для службы состоит из двух простых этапов.

1. Создание интерфейса для реализации в службе. Этот интерфейс определяет методы, которые будут доступны для удаленного вызова процедур в службе. Эти методы должны быть асинхронными методами, возвращающими задачи. Интерфейс должен реализовать `Microsoft.ServiceFabric.Services.Remoting.IService` , чтобы показать, что служба имеет интерфейс удаленного взаимодействия.
2. Используйте прослушиватель удаленного взаимодействия в службе. Это реализация `ICommunicationListener` , которая предоставляет возможности удаленного взаимодействия. Пространство имен `Microsoft.ServiceFabric.Services.Remoting.Runtime` содержит метод расширения `CreateServiceRemotingListener` для служб без отслеживания и с отслеживанием состояния, который можно использовать, чтобы создать прослушиватель удаленного взаимодействия с использованием транспортного протокола удаленного взаимодействия по умолчанию.

Примечание. Пространство имен `Remoting` доступно как отдельный пакет NuGet `Microsoft.ServiceFabric.Services.Remoting`. 

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
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
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

## <a name="service-proxy-lifetime"></a>Время существования ServiceProxy
Создание ServiceProxy — упрощенная операция, поэтому пользователь может создать столько прокси-серверов, сколько нужно. Прокси-сервер службы можно использовать повторно, если он нужен пользователю. Пользователь может повторно использовать один и тот же прокси-сервер при возникновении исключения. Каждый ServiceProxy содержит клиент обмена данными, используемый для отправки сообщений по сети. При вызове API выполняется внутренняя проверка допустимости клиента обмена данными. В зависимости от ее результата можно будет повторно создать клиент обмена данными. Поэтому пользователю не требуется заново создавать ServiceProxy при возникновении исключения.

### <a name="serviceproxyfactory-lifetime"></a>Время существования ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) — это фабрика, которая создает прокси-сервер для различных интерфейсов удаленного взаимодействия. Если вы используете API ServiceProxy.Create для создания прокси-сервера, то платформа создает одноэлементную ServiceProxyFactory.
При необходимости переопределить свойства [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) имеет смысл создать фабрику вручную.
Создание фабрики — ресурсоемкая операция. ServiceProxyFactory хранит кэш клиента обмена данными.
Рекомендуется кэшировать ServiceProxyFactory на как можно больший период времени.

## <a name="remoting-exception-handling"></a>Обработка исключений удаленного взаимодействия
Все исключения удаленного взаимодействия, порождаемые API службы, отправляются обратно в клиент как AggregateException. Исключения RemoteException должны быть сериализуемыми в формат DataContract, иначе API прокси-сервера получит исключение [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) с ошибкой сериализации.

ServiceProxy обрабатывает все исключения отработки отказа для секции службы, для которого он создан. Он повторно разрешает конечные точки в случае исключений отработки отказа (повторяющихся исключений) и повторяет вызов к правильной конечной точке. Число повторных попыток для исключения отработки отказа не ограничено.
В случае исключений TransientException только повторяется попытка вызова.

Параметры повтора по умолчанию определяются [OperationRetrySettings] (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings). Пользователь может настроить эти значения, передав объект OperationRetrySettings в конструктор ServiceProxyFactory.

## <a name="next-steps"></a>Дальнейшие действия
* [Веб-API с OWIN в модели Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Взаимодействие WCF с Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Защита обмена данными для Reliable Services](service-fabric-reliable-services-secure-communication.md)

