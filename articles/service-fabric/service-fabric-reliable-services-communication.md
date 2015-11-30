<properties
   pageTitle="Обзор взаимодействия с Reliable Services | Microsoft Azure"
   description="Общие сведения о модели взаимодействия с Reliable Services, включая открытие прослушивателей для служб, разрешение конечных точек и обмен данными между службами."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# Модель взаимодействия Reliable Services

Service Fabric, как платформа, совершенно не зависит от взаимодействия между службами. Допускается использование любых протоколов и стеков, от UDP до HTTP. Способ взаимодействия служб зависит только от выбора разработчика. Платформа приложений Reliable Services предоставляет несколько готовых стеков взаимодействия, а также средства для развертывания собственного стека взаимодействия, например абстракции, которые клиенты могут использовать для обнаружения и взаимодействия с конечными точками служб.

## Настройка взаимодействия службы

API Reliable Services использует простой интерфейс для взаимодействия служб. Чтобы открыть конечную точку для службы, просто реализуйте этот интерфейс:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Затем добавьте реализацию прослушивателя взаимодействия, вернув его в переопределении метода базового класса службы.

Для служб без отслеживания состояния:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Для служб с отслеживанием состояния:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

В обоих случаях возвращается коллекция прослушивателей, что позволяет службе использовать несколько прослушивателей, например прослушиватель HTTP и отдельный прослушиватель WebSocket. Каждый прослушиватель получает имя, а получившаяся коллекция пар "имя-адрес" представляется как объект JSON, когда клиент запрашивает ожидающие адреса для раздела или экземпляра службы.

Для службы без отслеживания состояния переопределение возвращает коллекцию объектов ServiceInstanceListener. Объект ServiceInstanceListener просто содержит функции для создания интерфейса ICommunicationListener и присваивает ему имя. Для служб с отслеживанием состояния переопределение возвращает коллекцию объектов ServiceReplicaListener. Здесь наблюдается небольшое отличие от аналога без отслеживания состояния, поскольку ServiceReplicaListener имеет возможность открыть интерфейс ICommunicationListener для вторичных реплик. Это позволяет не только использовать несколько прослушивателей взаимодействия в одной службе, но также указать, какие из них принимают запросы для вторичных реплик, а какие — прослушивают только первичные реплики.

Например, у нас есть прослушиватель ServiceRemotingListener, который принимает вызовы RPC только для первичных реплик, и второй, собственный прослушиватель, который принимает запросы на чтение для вторичных реплик:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

Наконец, рассмотрим конечные точки, которые требуются для службы в [манифесте службы](service-fabric-application-model.md) в разделе конечных точек.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

Прослушиватель связи может обращаться к ресурсам конечной точки, выделенным ему из `CodePackageActivationContext` в `ServiceInitializationParameters`, и прослушивать запросы при его открытии.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Ресурсы конечных точек являются общими для всего пакета служб и выделяются Service Fabric при активации пакета служб. Таким образом, все реплики, размещенные на одном и том же объекте ServiceHost, используют один и тот же порт. Это означает, что прослушиватель связи должен поддерживать совместное использование портов. Для этого мы рекомендуем при создании адреса прослушивания использовать прослушиватель связи идентификатора раздела и идентификатора реплики или экземпляра.

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
   replicaOrInstanceId = parameters.InstanceId;
}
else
{
   replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

Полное пошаговое руководство по написанию `ICommunicationListener` см. в разделе [Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md).

## Взаимодействие клиента со службой
Интерфейс API надежных служб дает возможность использовать указанные ниже абстракции, упрощающие написание клиентов для взаимодействия со службами.

### ServicePartitionResolver
Этот служебный класс помогает клиенту определить конечную точку службы Service Fabric во время выполнения. В терминологии Service Fabric процесс определения конечной точки службы называется разрешением конечной точки службы.

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
   long partitionKey,
   CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
   CancellationToken cancellationToken);


```
> [AZURE.NOTE]FabricClient — объект, который используется для взаимодействия с кластером Service Fabric в рамках различных операций управления в кластере Service Fabric.

Обычно в коде клиента не требуется непосредственная работа с `ServicePartitionResolver`. Он создается и передается другим вспомогательным классам в API надежных служб, которые во внутренней логике используют сопоставитель и помогают клиенту взаимодействовать со службой.

### CommunicationClientFactory
Объект `ICommunicationClientFactory` определяет базовый интерфейс, реализуемый фабрикой клиентов связи, которая создает клиенты, способные обмениваться данными со службой ServiceFabric. Реализация CommunicationClientFactory зависит от стека связи, используемого службой Service Fabric, с которой клиенту требуется связаться. Интерфейс API надежных служб дает возможность использовать объект CommunicationClientFactoryBase<TCommunicationClient>, который позволяет задействовать базовую реализацию данного интерфейса `ICommunicationClientFactory` и выполняет задачи, которые являются общими для всех стеков связи (как при использовании `ServicePartitionResolver` для определения конечной точки службы). Клиенты обычно реализуют абстрактный класс CommunicationClientFactoryBase для обработки специфической логики стека связи.

```csharp

protected CommunicationClientFactoryBase(
   ServicePartitionResolver servicePartitionResolver = null,
   IEnumerable<IExceptionHandler> exceptionHandlers = null,
   IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
   public MyCommunicationClient(MyCommunicationChannel communicationChannel)
   {
      this.CommunicationChannel = communicationChannel;
   }
   public MyCommunicationChannel CommunicationChannel { get; private set; }
   public ResolvedServicePartition ResolvedServicePartition;
}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

### ServicePartitionClient
Объект `ServicePartitionClient` использует CommunicationClientFactory (а во внутренней логике — ServicePartitionResolver) и помогает при связи со службой, обрабатывая повторные попытки общего взаимодействия и временные исключения Service Fabric.

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

Типичная схема использования выглядит следующим образом.

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(
   client =>
   {
      // Communicate with the service using the client.
      throw new NotImplementedException();
   },
   CancellationToken.None);


... other client code ...

```

## Дальнейшие действия
* [Удаленный вызов процедур с использованием удаленного взаимодействия Reliable Services](service-fabric-reliable-services-communication-remoting.md)

* [Веб-API с OWIN в модели Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Взаимодействие WCF с Reliable Services](service-fabric-reliable-services-communication-wcf.md)

 

<!---HONumber=Nov15_HO4-->