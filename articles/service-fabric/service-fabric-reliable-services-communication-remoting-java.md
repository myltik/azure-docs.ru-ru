---
title: "Удаленное управление службой в Azure Service Fabric | Документация Майкрософт"
description: "Удаленное взаимодействие Service Fabric позволяет осуществлять обмен данными между клиентами и службами с помощью удаленного вызова процедур."
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 03/09/2017
ms.author: pakunapa
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 11e300b3b1d0433bd4790332593ada2d3eede883
ms.lasthandoff: 04/03/2017


---
# <a name="service-remoting-with-reliable-services"></a>Удаленное взаимодействие службы с Reliable Services
> [!div class="op_single_selector"]
> * [C# в Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java в Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Платформа Reliable Services предоставляет механизм удаленного взаимодействия для быстрой и простой настройки удаленного вызова процедур.

## <a name="set-up-remoting-on-a-service"></a>Настройка удаленного доступа в службе
Процесс настройки удаленного доступа для службы состоит из двух простых этапов.

1. Создание интерфейса для реализации в службе. Этот интерфейс определяет методы, которые будут доступны для удаленного вызова процедур в службе. Эти методы должны быть асинхронными методами, возвращающими задачи. Интерфейс должен реализовать `microsoft.serviceFabric.services.remoting.Service` , чтобы показать, что служба имеет интерфейс удаленного взаимодействия.
2. Используйте прослушиватель удаленного взаимодействия в службе. Это реализация `CommunicationListener` , которая предоставляет возможности удаленного взаимодействия. `FabricTransportServiceRemotingListener` можно использовать, чтобы создать прослушиватель удаленного взаимодействия с использованием транспортного протокола удаленного взаимодействия по умолчанию.

Например, приведенная ниже служба без отслеживания состояния предоставляет один метод для получения "Hello World" посредством удаленного вызова процедуры.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Аргументы и возвращаемые данные в интерфейсе службы могут иметь простые, сложные или настраиваемые типы, но они должны быть сериализуемыми.
>
>

## <a name="call-remote-service-methods"></a>Вызов удаленных методов службы
Вызов методов в службе с помощью стека удаленного взаимодействия осуществляется с помощью локального прокси-сервера для службы через класс `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` . Метод `ServiceProxyBase` создает локальный прокси-сервер, используя тот же интерфейс, который реализует служба. С помощью этого прокси можно без труда удаленно вызвать методы в интерфейсе.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Платформа удаленного взаимодействия распространяет исключения, созданные в службе, на клиент. Поэтому логика обработки исключений на стороне клиента с использованием `ServiceProxyBase` может напрямую обрабатывать порождаемые службой исключения.

## <a name="next-steps"></a>Дальнейшие действия
* [Защита обмена данными для Reliable Services](service-fabric-reliable-services-secure-communication.md)

