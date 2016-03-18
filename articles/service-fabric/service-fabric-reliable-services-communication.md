<properties
   pageTitle="Общие сведения об обмене данными с Reliable Services | Microsoft Azure"
   description="Общие сведения о модели обмена данными с Reliable Services, включая открытие прослушивателей для служб, разрешение конечных точек и обмен данными между службами."
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

# Модель обмена данными Reliable Services

Обмен данными между службами совершенно не влияет на работу платформы Azure Service Fabric. Допускается использование любых протоколов и стеков, от UDP до HTTP. Способ взаимодействия служб зависит только от выбора разработчика. Платформа приложений Reliable Services предоставляет несколько готовых стеков связи, а также средства для развертывания пользовательского стека связи. К ним относятся абстракции, которые клиенты могут использовать для обнаружения и обмена данными с конечными точками служб.

## Настройка обмена данными между службами

API Reliable Services использует простой интерфейс для взаимодействия служб. Чтобы открыть конечную точку для службы, просто реализуйте этот интерфейс:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Затем добавьте реализацию прослушивателя связи, вернув его в переопределении метода класса на основе службы.

Для служб без отслеживания состояния:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Для служб с отслеживанием состояния:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

В обоих случаях возвращается коллекция прослушивателей. Это позволяет службе без проблем использовать несколько прослушивателей. Например, можно использовать прослушиватель HTTP и отдельный прослушиватель WebSocket. Каждый прослушиватель получает имя, а получившаяся коллекция пар *имя:адрес* представляется как объект JSON, когда клиент запрашивает адреса ожидания для секции или экземпляра службы.

Для службы без отслеживания состояния переопределение возвращает коллекцию объектов ServiceInstanceListener. Объект ServiceInstanceListener может создавать интерфейс ICommunicationListener и присваивать ему имя. Для служб с отслеживанием состояния переопределение возвращает коллекцию объектов ServiceReplicaListener. Здесь есть небольшое отличие от служб без отслеживания состояния, так как ServiceReplicaListener может открывать интерфейс ICommunicationListener для вторичных реплик. Это позволяет не только использовать несколько прослушивателей связи в одной службе, но также указывать, какие прослушиватели принимают запросы для вторичных реплик, а какие — прослушивают только первичные реплики.

Например, у вас есть прослушиватель ServiceRemotingListener, который принимает вызовы RPC только для первичных реплик, и настраиваемый прослушиватель, который принимает запросы на чтение для вторичных реплик:

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

Наконец, вы можете описать конечные точки, которые требуются для службы, в [манифесте служб](service-fabric-application-model.md) в разделе конечных точек.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

Прослушиватель связи может обращаться к ресурсам конечной точки, выделенным ему из `CodePackageActivationContext` в `ServiceInitializationParameters`, и начинать прослушивать запросы при открытии.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Ресурсы конечных точек являются общими для всего пакета служб. Они выделяются платформой Service Fabric при активации пакета служб. Таким образом, все реплики, размещенные на одном и том же объекте ServiceHost, используют один и тот же порт. Это означает, что прослушиватель связи должен поддерживать совместное использование портов. Для этого при создании адреса прослушивания мы рекомендуем использовать для прослушивателя связи идентификатор раздела и идентификатор реплики или экземпляра.

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

Полное пошаговое руководство по созданию `ICommunicationListener` см. в статье [Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md).

### Обмен данными между клиентом и службой
API-интерфейс Reliable Services предоставляет следующие абстракции, которые упрощают написание клиентов, взаимодействующих со службами.

#### ServicePartitionResolver
Этот служебный класс помогает клиенту определить конечную точку службы Service Fabric во время выполнения. При использовании Service Fabric процесс определения конечной точки службы называется *разрешением конечной точки службы*.

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
> [AZURE.NOTE]FabricClient — это объект, который используется для взаимодействия с кластером Service Fabric в рамках различных операций управления в кластере.

Обычно в коде клиента не предусмотрена непосредственная работа с классом `ServicePartitionResolver`. Он создается и передается другим вспомогательным классам в API служб Reliable Services. Эти классы используют сопоставитель внутри себя и помогают клиенту обмениваться данными со службой.

#### CommunicationClientFactory
Класс `ICommunicationClientFactory` определяет базовый интерфейс, реализуемый фабрикой клиента связи, которая создает клиенты, способные обмениваться данными со службой Service Fabric. Реализация CommunicationClientFactory зависит от стека связи, используемого службой Service Fabric, с которой клиенту требуется связаться. API-интерфейс Reliable Services предоставляет `CommunicationClientFactoryBase<TCommunicationClient>`. Это обеспечивает базовую реализацию интерфейса `ICommunicationClientFactory` и выполнение задач, которые являются общими для всех стеков связи. (Эти задачи включают в себя использование `ServicePartitionResolver` для определения конечной точки службы). Клиенты обычно реализуют абстрактный класс CommunicationClientFactoryBase для обработки специфической логики стека связи.

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

#### ServicePartitionClient
Класс `ServicePartitionClient` использует CommunicationClientFactory (а также ServicePartitionResolver внутри себя). Он также помогает взаимодействовать со службой, обрабатывая повторные попытки при общем обмене данными и временных исключениях Service Fabric.

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
* [Удаленное взаимодействие службы с Reliable Services](service-fabric-reliable-services-communication-remoting.md)

* [Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md)

* [Коммуникационный стек WCF для надежных служб](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0121_2016-->