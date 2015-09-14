<properties
   pageTitle="Обзор модели взаимодействия со службами"
	description="В этой статье описаны основы модели взаимодействия, поддерживаемой интерфейсом API надежных служб."
	services="service-fabric"
	documentationCenter=".net"
	authors="BharatNarasimman"
	manager="vipulm"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="required"
	ms.date="08/27/2015"
	ms.author="bharatn@microsoft.com"/>

# Модель взаимодействия со службами

Модель программирования надежных служб позволяет разработчикам служб указывать механизм обмена данными, который следует использовать для предоставления конечных точек службы. Кроме того, эта модель дает возможность пользоваться абстракциями, с помощью которых клиенты могут обнаруживать конечные точки службы и взаимодействовать с ними.

## Стек связи службы: настройка

Интерфейс API надежных служб позволяет создателям служб подключать к службе выбранный стек связи путем реализации в этой службе приведенного ниже метода.

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

Интерфейс `ICommunicationListener` определяет интерфейс, который должен быть реализован прослушивателем связи для службы.

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
Конечные точки, которые требуются для службы, описываются посредством [манифеста службы](service-fabric-application-model.md) в разделе конечных точек.

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

## Взаимодействие клиента со службой
Интерфейс API надежных служб дает возможность использовать указанные ниже абстракции, упрощающие написание клиентов для взаимодействия со службами.

## ServicePartitionResolver
Класс ServicePartitionResolver помогает клиенту определить конечную точку службы Service Fabric во время выполнения.

> [AZURE.TIP]В терминологии Service Fabric процесс определения конечной точки службы называется разрешением конечной точки службы.

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

## CommunicationClientFactory
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
    protected override void AbortClient(MyCommunicationClient1 client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

## ServicePartitionClient
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
* [Стек связи, предоставляемый по умолчанию платформой надежных служб](service-fabric-reliable-services-communication-default.md)

* [Стек связи на основе WCF, предоставляемый платформой надежных служб](service-fabric-reliable-services-communication-wcf.md)

* [Написание службы с помощью интерфейса API надежных служб, использующего стек связи WebAPI](service-fabric-reliable-services-communication-webapi.md)
 

<!---HONumber=September15_HO1-->