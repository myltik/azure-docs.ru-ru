---
title: Удаленное управление службой в Service Fabric | Документация Майкрософт
description: Удаленное взаимодействие Service Fabric позволяет осуществлять обмен данными между клиентами и службами с помощью удаленного вызова процедур.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 672bdd3ddb5b32b82d83322eadce2a594b13ce5b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643538"
---
# <a name="service-remoting-with-reliable-services"></a>Удаленное взаимодействие службы с Reliable Services
Для служб, которые не привязаны к определенному протоколу обмена данными или стеку, например веб-API, Windows Communication Foundation (WCF) или др., платформа Reliable Services предоставляет механизм удаленного взаимодействия для быстрой и простой настройки удаленного вызова процедур.

## <a name="set-up-remoting-on-a-service"></a>Настройка удаленного взаимодействия в службе
Процесс настройки удаленного доступа для службы состоит из двух простых этапов.

1. Создание интерфейса для реализации в службе. Этот интерфейс определяет методы, которые будут доступны для удаленного вызова процедур в службе. Эти методы должны быть асинхронными методами, возвращающими задачи. Интерфейс должен реализовать `Microsoft.ServiceFabric.Services.Remoting.IService` , чтобы показать, что служба имеет интерфейс удаленного взаимодействия.
2. Используйте прослушиватель удаленного взаимодействия в службе. Прослушиватель удаленного взаимодействия — это реализация `ICommunicationListener`, которая предоставляет возможности удаленного взаимодействия. Пространство имен `Microsoft.ServiceFabric.Services.Remoting.Runtime` содержит метод расширения `CreateServiceRemotingListener` для служб без отслеживания и с отслеживанием состояния, который можно использовать, чтобы создать прослушиватель удаленного взаимодействия с использованием транспортного протокола удаленного взаимодействия по умолчанию.

>[!NOTE]
>Пространство имен `Remoting` доступно как отдельный пакет NuGet `Microsoft.ServiceFabric.Services.Remoting`.

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

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Аргументы и возвращаемые данные в интерфейсе службы могут иметь простые, сложные или настраиваемые типы, однако они должны быть сериализуемыми с помощью объекта .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Вызов удаленных методов службы
Вызов методов в службе с помощью стека удаленного взаимодействия осуществляется с помощью локального прокси-сервера для службы через класс `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . Метод `ServiceProxy` создает локальный прокси-сервер, используя тот же интерфейс, который реализует служба. С помощью этого прокси-сервера можно удаленно вызвать методы в интерфейсе.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Платформа удаленного взаимодействия распространяет исключения, созданные службой, на клиент. Поэтому при использовании метода `ServiceProxy` клиент отвечает за обработку исключений, которые создает служба.

## <a name="service-proxy-lifetime"></a>Время существования ServiceProxy
Создание ServiceProxy не требует больших ресурсов, поэтому такие объекты можно создавать без каких-либо ограничений. Экземпляры ServiceProxy можно использовать повторно. Если удаленный вызов процедуры создает исключение, пользователи по-прежнему могут использовать тот же экземпляр прокси-сервера. Каждый объект ServiceProxy содержит клиент обмена данными, используемый для отправки сообщений по сети. При запуске удаленных вызовов мы проверяем работоспособность этого клиента и в случае необходимости создаем его повторно. Поэтому при возникновении исключения пользователю не нужно повторно создавать `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Время существования ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) — это фабрика, которая создает экземпляры прокси-сервера для различных интерфейсов удаленного взаимодействия. Если для создания прокси-сервера вы используете API `ServiceProxy.Create`, платформа создает одноэлементный ServiceProxy.
При необходимости переопределить свойства [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) имеет смысл создать фабрику вручную.
Создание фабрики — ресурсоемкая операция. ServiceProxyFactory хранит внутренний кэш клиента обмена данными.
Рекомендуется кэшировать ServiceProxyFactory на как можно больший период времени.

## <a name="remoting-exception-handling"></a>Обработка исключений удаленного взаимодействия
Все исключения удаленного взаимодействия, порождаемые API службы, отправляются обратно в клиент как AggregateException. Исключения RemoteException должны быть сериализуемыми в формат DataContract. В противном случае API прокси-сервера получит исключение [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) с ошибкой сериализации.

Объект ServiceProxy обрабатывает все исключения отработки отказа для секции службы, для которого он создан. Он повторно разрешает конечные точки в случае исключений отработки отказа (повторяющихся исключений) и повторяет вызов к правильной конечной точке. Число повторных попыток для исключений отработки отказа не ограничено.
Если порождаются временные исключения, прокси-сервер повторяет вызов.

Параметры повтора по умолчанию определяются [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Пользователь может настроить эти значения, передав объект OperationRetrySettings в конструктор ServiceProxyFactory.
## <a name="how-to-use-remoting-v2-stack"></a>Как использовать стек удаленного взаимодействия версии 2
Пакет NuGet для удаленного взаимодействия 2.8 дает возможность использовать стек удаленного взаимодействия версии 2. Стек удаленного взаимодействие версии 2 обеспечивает более высокую производительность, а также предоставляет такие функции, как настраиваемая сериализация и дополнительные подключаемые API.
По умолчанию, если не внести приведенные ниже изменения, то используется стек удаленного взаимодействия версии 1.
Стек удаленного взаимодействия версии 2 несовместим со стеком версии 1 (предыдущим стеком удаленного взаимодействия), поэтому выполните указания ниже, чтобы обновить версию 1 до версии 2, не влияя на доступность службы.

### <a name="using-assembly-attribute-to-use-v2-stack"></a>Использование атрибута сборки для стека версии 2

Ниже приведены инструкции по переходу на использование стека версии 2.

1. Добавьте ресурс конечной точки, например ServiceEndpointV2, в манифест службы.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  Используйте метод расширения удаленного взаимодействия для создания прослушивателя удаленного взаимодействия.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Добавьте атрибут сборки в интерфейсы удаленного взаимодействия.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
Вносить изменения проект клиента не требуется.
Выполните сборку клиента со сборкой интерфейса, чтобы обеспечить использование приведенного выше атрибута сборки.

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>Использование явных классов версии 2 для создания прослушивателя и фабрики клиента
Ниже приведены инструкции, которые нужно выполнить.
1.  Добавьте ресурс конечной точки, например ServiceEndpointV2, в манифест службы.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. Используйте [прослушиватель удаленного взаимодействия версии 2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet). Используемое имя ресурса конечной точки службы по умолчанию — ServiceEndpointV2. Оно должны быть определено в манифесте службы.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Используйте [фабрику клиента](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) версии 2.
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Как выполнить обновление службы удаленного взаимодействия версии 1 до версии 2
Чтобы обновить версию 1 до версии 2, требуются выполнить двухэтапное обновление. Требуется выполнить указанные действия в приведенном порядке.

1. Обновите службу версии 1 до версии 2 с помощью атрибута CompactListener.
Это изменение гарантирует, что служба будет ожидать передачи данных от прослушивателей версии 1 и 2.

    а) Добавьте ресурс конечной точки, например ServiceEndpointV2, в манифест службы.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    б) Используйте приведенный ниже метод расширения удаленного взаимодействия для создания прослушивателя удаленного взаимодействия.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    в) Добавьте атрибут сборки в интерфейсы удаленного взаимодействия для использования CompatListener и клиента версии 2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Обновите клиент версии 1 до версии 2 с помощью атрибута клиента версии 2.
Это гарантирует, что клиент будет использовать стек версии 2.
Никаких изменений в проект службы или клиента вносить не требуется. Достаточно будет выполнить сборку проектов клиента со обновленной сборкой интерфейса.

3. Этот шаг не является обязательным. Используйте атрибут прослушивателя версии 2 и обновите службу версии 2.
Это гарантирует, что служба будет ожидать передачи данных только от прослушивателя версии 2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Как использовать настраиваемую сериализацию с помощью службы удаленного взаимодействия версии 2
В следующем примере используется сериализация JSON с помощью службы удаленного взаимодействия версии 2.
1. Реализуйте интерфейс IServiceRemotingMessageSerializationProvider для реализации настраиваемой сериализации.
    Ниже приведен фрагмент кода, содержащий данную реализацию.

 ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> requestBodyTypes)
        {
            return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> responseBodyTypes)
        {
            return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
        }

        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
            return new JsonMessageFactory();
        }
    }

    class JsonMessageFactory : IServiceRemotingMessageBodyFactory
    {
        public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
            int numberOfParameters)
        {
            return new JsonRemotingRequestBody();
        }

        public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
        {
            return new JsonRemotingResponseBody();
        }
    }

    class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
    {
        public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
        {
            if (serviceRemotingRequestMessageBody == null)
            {
                return null;
            }

            var writeStream = new MemoryStream();
            var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream));

            var serializer = JsonSerializer.Create(new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);

            jsonWriter.Flush();
            var segment = new ArraySegment<byte>(writeStream.ToArray());
            var segments = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(segments);
        }

        public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (JsonReader reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingRequestBody>(reader);
            }
        }
    }

    class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
    {
        public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
        {
            var json = JsonConvert.SerializeObject(responseMessageBody, new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            var bytes = Encoding.UTF8.GetBytes(json);
            var segment = new ArraySegment<byte>(bytes);
            var list = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(list);
        }

        public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (var reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingResponseBody>(reader);
            }
        }
    }

    internal class JsonRemotingResponseBody : IServiceRemotingResponseMessageBody
    {
        public object Value;

        public void Set(object response)
        {
            this.Value = response;
        }

        public object Get(Type paramType)
        {
            return this.Value;
        }
    }

    class JsonRemotingRequestBody : IServiceRemotingRequestMessageBody
    {
        public readonly Dictionary<string, object> parameters = new Dictionary<string, object>();        

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.parameters[parameName] = parameter;
        }

        public object GetParameter(int position, string parameName, Type paramType)
        {
            return this.parameters[parameName];
        }
    }
 ```

2.    Переопределите поставщик сериализации по умолчанию, указав JsonSerializationProvider для прослушивателя удаленного взаимодействия.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Переопределите поставщик сериализации по умолчанию, указав JsonSerializationProvider для фабрики клиента удаленного взаимодействия.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Дополнительная информация
* [Веб-API с OWIN в модели Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Взаимодействие WCF с Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Защита обмена данными для Reliable Services](service-fabric-reliable-services-secure-communication.md)

