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
   ms.date="03/25/2016"
   ms.author="vturecek"/>

# Использование API связи служб Reliable Services

Обмен данными между службами совершенно не влияет на работу платформы Azure Service Fabric. Допускается использование любых протоколов и стеков, от UDP до HTTP. Способ взаимодействия служб зависит только от выбора разработчика. Платформа приложений служб Reliable Services предоставляет несколько встроенных стеков связи, а также API-интерфейсы для создания пользовательских компонентов связи.

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
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

Для служб с отслеживанием состояния:

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

В обоих случаях возвращается коллекция прослушивателей. Это позволяет службе прослушивать несколько конечных точек, потенциально с помощью различных протоколов, используя несколько прослушивателей. Например, можно использовать прослушиватель HTTP и отдельный прослушиватель WebSocket. Каждый прослушиватель получает имя, а получившаяся коллекция пар *имя:адрес* представляется как объект JSON, когда клиент запрашивает адреса ожидания для секции или экземпляра службы.

Для службы без отслеживания состояния переопределение возвращает коллекцию объектов ServiceInstanceListener. Объект ServiceInstanceListener может создавать интерфейс ICommunicationListener и присваивать ему имя. Для служб с отслеживанием состояния переопределение возвращает коллекцию объектов ServiceReplicaListener. Здесь есть небольшое отличие от служб без отслеживания состояния, так как ServiceReplicaListener может открывать интерфейс ICommunicationListener для вторичных реплик. Это позволяет не только использовать несколько прослушивателей связи в одной службе, но также указывать, какие прослушиватели принимают запросы для вторичных реплик, а какие — прослушивают только первичные реплики.

Например, у вас есть прослушиватель ServiceRemotingListener, который принимает вызовы RPC только для первичных реплик, и настраиваемый прослушиватель, который принимает запросы на чтение для вторичных реплик:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomListener(context),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

Наконец, вы можете описать конечные точки, которые требуются для службы, в [манифесте служб](service-fabric-application-model.md) в разделе конечных точек.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Прослушиватель связи может обращаться к ресурсам конечной точки, выделенным ему из `CodePackageActivationContext` в `ServiceContext`, и начинать прослушивать запросы при открытии.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] Ресурсы конечных точек являются общими для всего пакета служб. Они выделяются платформой Service Fabric при активации пакета служб. Множество реплик, размещенных на одном и том же объекте ServiceHost, используют один и тот же порт. Это означает, что прослушиватель связи должен поддерживать совместное использование портов. Для этого при создании адреса прослушивания мы рекомендуем использовать для прослушивателя связи идентификатор раздела и идентификатор реплики или экземпляра.

### Регистрация адреса службы

Системная *служба именования* работает на кластерах Service Fabric. Служба именования — это регистратор для служб и их адресов, которые прослушивает каждый экземпляр или реплика службы. Когда метод `OpenAsync` `ICommunicationListener` завершает выполнение, возвращаемое значение регистрируется в службе именования. Значение, которое публикуется в службе именования, — это строка, значением которой может что угодно. Это строковое значение определяет, что клиенты будут видеть при запросе адреса службы у службы именования.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

Платформа Service Fabric предоставляет API-интерфейсы, которые позволяют клиентам и другим службам в дальнейшем запрашивать этот адрес по имени службы. Это важно, так как у службы нестатический адрес. Перемещение служб в кластере осуществляется для балансировки ресурсов и обеспечения их доступности. Это механизм, позволяющий клиентам распознавать адрес прослушивания для службы.

> [AZURE.NOTE] Полное пошаговое руководство по созданию `ICommunicationListener` см. в статье [Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md).

## Взаимодействие со службой
API-интерфейс служб Reliable Services предоставляет следующие библиотеки для написания клиентов, взаимодействующих со службами.

### Разрешение конечной точки службы
Первый шаг взаимодействия со службой — разрешение адреса конечной точки раздела или экземпляра службы, с которой вы хотите связаться. Служебный класс `ServicePartitionResolver` — это базовый примитивный тип, который помогает клиенту определить конечную точку во время выполнения. При использовании Service Fabric процесс определения конечной точки службы называется *разрешением конечной точки службы*.

Для подключения к службам в кластере можно создать `ServicePartitionResolver` без параметров:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver();
```

Для подключения к службам в другом кластере `ServicePartitionResolver` можно создать с набором конечных точек шлюза кластера. Обратите внимание, что конечные точки шлюза — это просто другие конечные точки для подключения к тому же кластеру. Например:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

`ServicePartitionResolver` можно передать функцию, чтобы создать `FabricClient` для внутреннего использования.
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient` — это объект, который используется для взаимодействия с кластером Service Fabric в рамках различных операций управления в кластере. Это полезно, когда требуется больший контроль над тем, как `ServicePartitionClient` взаимодействует с кластером. `FabricClient` выполняет внутреннее кэширование и обычно требует много ресурсов для создания, поэтому важно повторно использовать как можно больше экземпляров `FabricClient`.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

Метод resolve затем используется для получения адреса службы или раздела секционированных служб.

```csharp
ServicePartitionResolver resolver = new ServicePartitionResolver();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

Адрес службы можно разрешить с помощью `ServicePartitionResolver`, но потребуется дополнительная работа, чтобы разрешенный адрес можно было использовать правильно. Клиенту необходимо определить, произошел ли сбой из-за временной ошибки и можно ли его повторить (например, служба была перемещена или временно недоступна) или из-за постоянной ошибки (например, служба была удалена или запрошенный ресурс больше не существует). Экземпляры или реплики службы можно перемещать между узлами в любое время по различным причинам. Адрес службы, который разрешается с помощью `ServicePartitionResolver`, может устареть к тому времени, когда клиентский код попытается к ней подключиться. В этом случае клиент должен повторно разрешить адрес. Предыдущий `ResolvedServicePartition` указывает, что сопоставителю необходимо повторить попытку, вместо того чтобы просто получить кэшированный адрес.

Обычно в коде клиента не предусмотрена непосредственная работа с классом `ServicePartitionResolver`. Он создается и передается фабрикам клиента в API служб Reliable Services. Фабрики используют сопоставитель для создания клиентского объекта, который может применяться для взаимодействия со службами.

### Клиенты и фабрики связи

Библиотека фабрики связи реализует типичный шаблон повторных попыток обработки ошибок, упрощающий повтор подключений к конечным точкам разрешенной службы. Библиотека фабрики предоставляет механизм повтора, а вы — обработчики ошибок.

Класс `ICommunicationClientFactory` определяет базовый интерфейс, реализуемый фабрикой клиента связи, которая создает клиенты, способные обмениваться данными со службой Service Fabric. Реализация CommunicationClientFactory зависит от стека связи, используемого службой Service Fabric, с которой клиенту требуется связаться. API-интерфейс служб Reliable Services предоставляет `CommunicationClientFactoryBase<TCommunicationClient>`. Это обеспечивает базовую реализацию интерфейса `ICommunicationClientFactory` и выполнение задач, которые являются общими для всех стеков связи. (Эти задачи включают в себя использование `ServicePartitionResolver` для определения конечной точки службы.) Клиенты обычно реализуют абстрактный класс CommunicationClientFactoryBase для обработки специфической логики стека связи.

Клиент связи просто получает адрес и использует его для подключения к службе. Клиент может использовать любой протокол.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

Фабрика клиента в основном отвечает за создание клиентов связи. Для клиентов, которые не поддерживают постоянное подключение, например из клиента HTTP, фабрике требуется только создать и вернуть клиент. Другие протоколы, которые поддерживают постоянное подключение, например некоторые двоичные протоколы, также должны проверяться фабрикой, чтобы определить, нужно ли повторно создать соединение.

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

Наконец, обработчик исключений определяет, какое действие следует предпринять при возникновении исключения. Исключения классифицируются как **повторяемые** и **неповторяемые**.

 - **Неповторяемые** исключения просто возвращаются вызывающему объекту. 
 - **Повторяемые** исключения далее классифицируются как **временные** и **невременные**.
  - **Временные** исключения могут быть повторены без повторного разрешения адреса конечной точки службы. К ним относятся временные проблемы с сетью или сообщения об ошибках службы, отличные от тех, которые указывают, что адрес конечной точки службы не существует. 
  - **Невременные** исключения требуют повторного разрешения адреса конечной точки службы. К ним относятся исключения, указывающие, что не удалось подключиться к конечной точке службы, потому что служба была перемещена на другой узел. 

`TryHandleException` принимает решение о данном исключении. Если **неизвестно**, какое решение следует принять об исключении, возвращается значение **false**. Если решение **известно**, результат задается соответствующим образом, и возвращается значение **true**.
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### Сборка
`ICommunicationClient`, `ICommunicationClientFactory` и `IExceptionHandler` основаны на протоколе связи, а `ServicePartitionClient` создает совместную оболочку и предоставляет цикл обработки ошибок и разрешения адреса раздела службы вокруг этих компонентов.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## Дальнейшие действия
 - Пример HTTP-связи между службами см. в [примере проекта на GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Удаленное взаимодействие службы с Reliable Services](service-fabric-reliable-services-communication-remoting.md)

 - [Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md)

 - [Коммуникационный стек WCF для надежных служб](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0608_2016-->