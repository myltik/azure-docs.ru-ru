<properties
   pageTitle="Взаимодействие со службами через веб-API ASP.NET | Microsoft Azure"
   description="Пошаговые инструкции по реализации взаимодействия со службами с помощью веб-API ASP.NET и саморазмещения OWIN в API Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# Приступая к работе со службами веб-API Service Fabric с саморазмещением OWIN

Платформа Azure Service Fabric дает вам возможность решать, как разрабатываемые службы должны взаимодействовать с пользователями и друг с другом. Это руководство посвящено реализации взаимодействия служб с помощью веб-API ASP.NET с саморазмещением Open Web Interface for .NET (OWIN) в API Reliable Services на платформе Service Fabric. Мы подробно рассмотрим API для взаимодействия со службами Reliable Services. Кроме того, здесь приведен пример веб-API с пошаговыми инструкциями, чтобы показать, как настроить пользовательский прослушиватель связи.


## Общие сведения о веб-API в Service Fabric

Веб-API ASP.NET — это популярная и мощная платформа для построения интерфейсов HTTP API поверх .NET Framework. Если вы еще не знакомы с тем, как работает эта платформа, см. сведения в статье [Getting started with ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) (Приступая к работе с веб-API ASP.NET 2).

Веб-API в Service Fabric — это тот же веб-API ASP.NET, который вы знаете и любите. Различие состоит в том, как *размещается* приложение веб-API. Этот процесс не предусматривает использование Microsoft IIS. Чтобы лучше понять разницу, разобьем систему на две части.

 1. Приложение веб-API (в том числе контроллеры и модели).
 2. Хост (веб-сервер, обычно IIS).

Само приложение веб-API не меняется. Оно не отличается от приложений веб-API, которые вы писали ранее. Кроме того, можно просто переместить сюда большую часть кода приложения. Если раньше вы использовали IIS, процесс размещения приложения может немного отличаться от привычного. Прежде чем мы перейдем к размещению, начнем с более привычного действия — создания приложения веб-API.


## Создание приложения

Для начала в Visual Studio 2015 создайте новое приложение Service Fabric с одной службой без отслеживания состояния:

![Создание нового приложения Service Fabric](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Доступен шаблон Visual Studio для службы без отслеживания состояния с использованием веб-API. В этом руководстве мы с нуля создадим проект веб-API, который покажет, какие результаты можно получить, выбрав этот шаблон.

Выберите пустой проект службы без отслеживания состояния, чтобы научиться создавать проект веб-API с нуля. Или воспользуйтесь шаблоном веб-API службы без отслеживания состояния.

![Создание единой службы без отслеживания состояния](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

На первом шаге потребуется задействовать некоторые пакеты NuGet для веб-API. Нужный нам пакет — Microsoft.AspNet.WebApi.OwinSelfHost. В нем содержатся все необходимые пакеты веб-API и пакеты *размещения*. Это будет важно в дальнейшем.

![Создание веб-API с помощью диспетчера пакетов NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

После установки пакетов можно начать создание базовой структуры проекта веб-API. Если вы уже использовали веб-API, структура проекта покажется очень знакомой. Начните с добавления каталога `Controllers` и контроллера простых значений.

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Затем добавьте класс Startup в корень проекта для регистрации маршрутизации, модулей форматирования и настройки других параметров конфигурации. Это также точка подключения веб-API к *узлу* (мы еще раз рассмотрим это дальше).

**Startup.cs.**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

Теперь со стороны приложения все готово. Сейчас мы только настроили базовую структуру проекта веб-API. Она должна не сильно отличаться от структуры проектов веб-API, которые вы могли создавать в прошлом, или базового шаблона веб-API. Бизнес-логика реализуется в контроллерах и моделях как обычно.

Какие шаги по размещению следует выполнить для реального запуска?

## Размещение службы

В Service Fabric служба запускается в *хост-процессе службы* — исполняемом файле, который выполняет код службы. При написании службы с использованием API Reliable Services проект службы просто компилируется в EXE-файл, который регистрирует тип службы и выполняет код. Так происходит в большинстве случаев при написании службы для Service Fabric на платформе .NET. Если открыть Program.cs в проекте службы без отслеживания состояния, отобразится такой код:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Если он подозрительно похож на точку входа в консольное приложение, скорее всего, он ею и является.

Дополнительные сведения о хост-процессе и регистрации службы в этой статье не рассматриваются. Тем не менее на текущем этапе важно знать, что *код вашей службы выполняется в отдельном процессе*.

## Саморазмещение веб-API с использованием хоста OWIN

Если код приложения веб-API размещается с помощью отдельного процесса, как связать его с веб-сервером? Для этого есть [OWIN](http://owin.org/). OWIN — это просто контракт между веб-приложениями .NET и веб-серверами. Обычно в ASP.NET (до MVC 5) веб-приложение тесно связано с IIS через System.Web. Но интерфейс веб-API реализует OWIN, который позволяет написать веб-приложение, не связанное с веб-сервером, на котором оно размещается. Таким образом вы можете использовать веб-сервер OWIN с *саморазмещением*, который можно запускать отдельным процессом, что идеально подходит для описанной выше модели размещения Service Fabric.

В этой статье мы будем использовать Katana в качестве хоста OWIN для приложения веб-API. Katana — это реализация хоста OWIN с открытым исходным кодом на основе [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) и [API сервера HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx) Windows.

> [AZURE.NOTE] Дополнительные сведения о Katana см. на [сайте Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Краткий обзор способов применения Katana для саморазмещения веб-API см. в статье [Use OWIN to Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api). (Использование OWIN для саморазмещения веб-API 2 для ASP.NET).


## Настройка веб-сервера

API Reliable Services предоставляет точку входа для обмена данными, к которой можно подключить стеки связи, чтобы пользователи и клиенты могли подключаться к службе:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Для корректной интеграции с системой веб-сервер (как и любой другой стек связи, который может использоваться в будущем, например WebSockets) должен использовать интерфейс ICommunicationListener. Причины для этого станут понятны на следующих шагах.

Сначала создайте класс с именем OwinCommunicationListener, который реализует ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

Интерфейс ICommunicationListener предоставляет три метода для управления прослушивателем связи для службы:

 - *OpenAsync*: запуск прослушивания запросов.
 - *CloseAsync*: остановка прослушивания запросов, завершение всех активных запросов и правильное завершение работы.
 - *Abort*: отмена всех операций и немедленная остановка.

Для начала добавьте члены закрытого класса, необходимые для работы прослушивателя. Они будут инициализированы с помощью конструктора и в дальнейшем будут использоваться при настройке URL-адреса прослушивания.

```csharp
public class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## Реализация OpenAsync

Чтобы настроить веб-сервер, необходимы сведения для двух параметров.

 - *Префикс пути URL-адреса*. Это необязательный параметр, но его лучше настроить сейчас, чтобы можно было безопасно разместить в приложении несколько веб-служб.
 - *Порт*.

Прежде чем назначить порт веб-серверу, важно понимать, что платформа Service Fabric предоставляет слой приложения, который служит буфером между приложением и базовой операционной системой, в которой оно выполняется. Таким образом, Service Fabric предоставляет способ настройки *конечных точек* для ваших служб. Service Fabric обеспечивает доступность конечных точек для вашей службы. Следовательно, вам не нужно настраивать ее в среде базовой ОС. Благодаря этому можно с легкостью размещать приложение Service Fabric в различных средах, и при этом не нужно вносить в него изменения. (Например, одно и то же приложение можно разместить как в Azure, так и в своем центре обработки данных.)

Настройте конечную точку HTTP в файле PackageRoot\\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Этот шаг важен, так как при выполнении хост-процесса службы используются учетные данные с ограничением по доступу (для сетевой службы в Windows). Это означает, что у службы не будет доступа для самостоятельной настройки конечной точки HTTP. Платформа Service Fabric использует конфигурацию конечной точки, чтобы определить, как настроить соответствующий список управления доступом (ACL) для URL-адреса, который будет прослушивать служба. Service Fabric также обеспечивает создание стандартного места для настройки конечных точек.


Теперь в OwinCommunicationListener.cs можно начать реализацию OpenAsync. Так начнется запуск веб-сервера. Во-первых, получите информацию о конечной точке и создайте URL-адрес, который будет прослушивать служба. URL-адрес будет отличаться в зависимости от того, используется ли прослушиватель в службе без отслеживания состояния или службе с отслеживанием состояния. В случае службы с отслеживанием состояния прослушивателю необходимо создать уникальный адрес для каждой реплики службы с отслеживанием состояния, которую он прослушивает. Для случае служб без отслеживания состояния адрес может быть гораздо проще.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Обратите внимание, что здесь используется адрес «http://+». Это позволяет обеспечить прослушивание веб-сервером всех доступных адресов, в том числе localhost, полного доменного имени и IP-адреса компьютера.

Реализация OpenAsync — одна из основных причин, по которой веб-сервер (или любой стек связи) реализуется как ICommunicationListener, а не просто открывается напрямую с помощью `RunAsync()` в службе. Значение, возвращаемое OpenAsync, представляет собой адрес, который прослушивается веб-сервером. Когда этот адрес возвращается в систему, она регистрирует адрес в службе. Платформа Service Fabric дает возможность использовать API, который позволяет клиентам и другим службам в дальнейшем запрашивать этот адрес по имени службы. Это важно, так как у службы нестатический адрес. Перемещение служб в кластере осуществляется для балансировки ресурсов и обеспечения их доступности. Это механизм, позволяющий клиентам распознавать адрес прослушивания для службы.

С учетом этого OpenAsync запускает веб-сервер и возвращает адрес, который прослушивает. Обратите внимание, что сервер прослушивает http://+, но перед возвратом адреса от OpenAsync знак "+" заменяется IP-адресом или полным доменным именем узла, на котором он в настоящее время находится. Адрес, возвращаемый с помощью этого метода, зарегистрирован в системе. Его увидят клиенты и другие службы при запросе адреса службы. Для правильного подключения к нему клиентов им требуется фактический IP-адрес или полное доменное имя в адресе.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.ServiceMessage(this.serviceContext, "Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Обратите внимание, что при этом выполняется обращение к классу Startup, переданному в OwinCommunicationListener в конструкторе. Этот экземпляр Startup веб-сервер использует для начальной загрузки приложения веб-API.

Позже при запуске приложения в окне "События диагностики" появится строка `ServiceEventSource.Current.ServiceMessage()` в подтверждение успешного запуска веб-сервера.

## Реализация CloseAsync и Abort

Наконец, необходимо реализовать как CloseAsync, так и Abort для остановки веб-сервера. Веб-сервер может быть остановлен путем утилизации дескриптора сервера, который был создан при выполнении OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.ServiceMessage(this.serviceContext, "Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.ServiceMessage(this.serviceContext, "Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

В этом примере реализации CloseAsync и Abort просто останавливают веб-сервер. Вы можете завершить работу веб-сервера в CloseAsync более скоординировано. Например, вы можете дождаться завершения выполняющихся запросов перед возвратом.

## Запуск веб-сервера

Теперь вы готовы создать и вернуть экземпляр OwinCommunicationListener для запуска веб-сервера. В классе Service (Service.cs) переопределите метод `CreateServiceInstanceListeners()`.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Здесь наконец встречаются *приложение* веб-API и *узел* OWIN. У узла (OwinCommunicationListener) теперь есть экземпляр *приложения* (веб-API, полученный с использованием Startup). Его жизненным циклом управляет Service Fabric. Такого же шаблона можно придерживаться при использовании любого стека связи.

## Сборка

В этом примере не нужно предпринимать никаких действий в методе `RunAsync()`, поэтому переопределение можно просто удалить.

Окончательная реализация службы будет очень простой, так как для этого требуется только создать прослушиватель связи:

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

Полный класс `OwinCommunicationListener`.

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }

        public bool ListenOnSecondary { get; set; }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.ServiceMessage(this.serviceContext, "Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.ServiceMessage(this.serviceContext, "Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.ServiceMessage(this.serviceContext, "Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

Теперь все элементы на своих местах и приложение веб-API должно выглядеть аналогично обычному приложению веб-API с точками входа интерфейса API Reliable Services и хостом OWIN.


![Веб-API с точками входа API Reliable Services и хостом OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Запуск и подключение через веб-браузер

Если это еще не сделано, [настройте среду разработки](service-fabric-get-started.md).


Теперь можно построить и развернуть службу. Нажмите клавишу **F5** в Visual Studio, чтобы выполнить построение и развертывание приложения. В окне "События диагностики" вы увидите сообщение о том, что веб-сервер открыт по адресу http://localhost:8281/.


![Окно "События диагностики" в Visual Studio](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Если порт уже открыт в результате работы другого процесса на компьютере, может появиться сообщение об ошибке. Это значит, что прослушиватель не удалось открыть. В этом случае попробуйте использовать другой порт в конфигурации конечной точки в файле ServiceManifest.xml.


После запуска службы откройте браузер и перейдите по адресу [http://localhost:8281/api/values](http://localhost:8281/api/values), чтобы проверить ее работу.

## Масштабирование

Масштабирование веб-приложений без отслеживания состояния обычно предусматривает добавление новых компьютеров и запуск на них веб-приложения. Механизм оркестровки Service Fabric может обеспечивать это всякий раз при добавлении новых узлов в кластер. При создании экземпляров службы без отслеживания состояния можно указать количество экземпляров, которые следует создать. Service Fabric разместит соответствующее количество экземпляров на узлах в кластере. Создание больше чем одного экземпляра на узле исключается. Кроме того, вы можете настроить Service Fabric, чтобы эта платформа всегда создавала экземпляр на каждом узле. Укажите значение **-1** для количества экземпляров. Это гарантирует, что каждый раз, когда добавляются узлы для масштабирования кластера, на новых узлах будет создаваться экземпляр службы без отслеживания состояния. Это значение является свойством экземпляра службы, поэтому оно задается при создании экземпляра службы. Его можно задать с помощью PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Его также можно задать при определении службы по умолчанию в проекте службы без отслеживания состояния в Visual Studio.

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Дополнительные сведения о создании экземпляров приложений и служб см. в статье [Развертывание приложения](service-fabric-deploy-remove-applications.md).

## Дальнейшие действия

[Отладка приложения Service Fabric с помощью Visual Studio](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0713_2016-->