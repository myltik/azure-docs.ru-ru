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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Приступая к работе со службами веб-API Service Fabric с саморазмещением OWIN

Платформа Azure Service Fabric дает вам возможность решать, как разрабатываемые службы должны взаимодействовать с пользователями и друг с другом. Это руководство посвящено реализации взаимодействия служб с помощью веб-API ASP.NET с саморазмещением Open Web Interface for .NET (OWIN) в API Reliable Services на платформе Service Fabric. Мы подробно рассмотрим API для взаимодействия со службами Reliable Services. Кроме того, здесь приведен пример веб-API с пошаговыми инструкциями, чтобы показать, как настроить пользовательский прослушиватель связи.


## Общие сведения о веб-API в Service Fabric

Веб-API ASP.NET — это популярная мощная платформа создания API HTTP поверх .NET Framework. Если вы еще не знакомы с тем, как работает эта платформа, см. сведения в статье [Getting started with ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) (Приступая к работе с веб-API ASP.NET 2).

Веб-API в Service Fabric — это уже известный вам веб-API ASP.NET. Различие состоит в том, как *размещается* приложение веб-API. Этот процесс не предусматривает использование Microsoft IIS. Чтобы лучше понять разницу, разобьем систему на две части.

 1. Приложение веб-API (в том числе контроллеры и модели).
 2. Хост (веб-сервер, обычно IIS).

Само приложение веб-API не меняется. Оно не отличается от приложений веб-API, которые вы писали ранее. Кроме того, можно просто переместить сюда большую часть кода приложения. Если раньше вы использовали IIS, процесс размещения приложения может немного отличаться от привычного. Прежде чем мы перейдем к размещению, начнем с более привычного действия — создания приложения веб-API.


## Создание приложения

Для начала в Visual Studio 2015 создайте новое приложение Service Fabric с одной службой без отслеживания состояния:

![Создание нового приложения Service Fabric](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![Создание единой службы без отслеживания состояния](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

В результате мы получим пустую службу без отслеживания состояния, в которой будет размещено приложение веб-API. Мы настроим приложение с нуля, чтобы увидеть, как компоненты согласуются между собой.

На первом шаге потребуется задействовать некоторые пакеты NuGet для веб-API. Нужный нам пакет — Microsoft.AspNet.WebApi.OwinSelfHost. В нем содержатся все необходимые пакеты веб-API и пакеты *размещения*. Это будет важно в дальнейшем.

![Создание веб-API с помощью диспетчера пакетов NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

После установки пакетов можно начать создание базовой структуры проекта веб-API. Если вы уже использовали веб-API, структура проекта покажется очень знакомой. Начните с создания базовых каталогов веб-API:

 + App\_Start;
 + Controllers;
 + Models.

Добавьте базовые классы конфигурации веб-API в каталог App\_Start. Пока мы просто добавим пустую конфигурацию модуля форматирования типа мультимедиа:

**FormatterConfig.cs;**

```csharp

namespace WebApiService
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

Добавьте контроллер по умолчанию в каталог Controllers:

**DefaultController.cs.**

```csharp

namespace WebApiService.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    [RoutePrefix("api")]
    public class DefaultController : ApiController
    {
        // GET api/values
        [Route("values")]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        [Route("values/{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        [Route("values")]
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        [Route("values/{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        [Route("values/{id}")]
        public void Delete(int id)
        {
        }
    }
}

```

Наконец, добавьте класс Startup в корень проекта для регистрации маршрутизации, средств форматирования и другой конфигурационной настройки. Это также точка подключения веб-API к *хосту*, что мы еще раз рассмотрим далее. При настройке класса Startup создайте для него интерфейс IOwinAppBuilder, который определяет метод конфигурации. Хотя технически это не требуется для работы веб-API, интерфейс предоставит дополнительную гибкость при использовании класса Startup в дальнейшем.

**Startup.cs.**

```csharp

namespace WebApiService
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            config.MapHttpAttributeRoutes();
            FormatterConfig.ConfigureFormatters(config.Formatters);

            appBuilder.UseWebApi(config);
        }
    }
}

```

**IOwinAppBuilder.cs.**

```csharp

namespace WebApiService
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

Теперь со стороны приложения все готово. Сейчас мы только настроили базовую структуру проекта веб-API. Она должна не сильно отличаться от структуры проектов веб-API, которые вы могли создавать в прошлом, или базового шаблона веб-API. Бизнес-логика реализуется в контроллерах и моделях как обычно.

Какие шаги по размещению следует выполнить для реального запуска?


## Размещение службы

В Service Fabric служба запускается в *хост-процессе службы*, исполняемом файле, который выполняет код службы. При написании службы с использованием API Reliable Services проект службы просто компилируется в EXE-файл, который регистрирует тип службы и выполняет код. Так происходит в большинстве случаев при написании службы для Service Fabric на платформе .NET. Если открыть Program.cs в проекте службы без отслеживания состояния, отобразится такой код:

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType("WebApiServiceType", typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

Если он подозрительно похож на точку входа в консольное приложение, скорее всего, он ею и является.

Дополнительные сведения о хост-процессе и регистрации службы в этой статье не рассматриваются. Тем не менее на текущем этапе важно знать, что *код вашей службы выполняется в отдельном процессе*.

## Саморазмещение веб-API с использованием хоста OWIN

Если код приложения веб-API размещается с помощью отдельного процесса, как связать его с веб-сервером? Для этого есть [OWIN](http://owin.org/). OWIN — это просто контракт между веб-приложениями .NET и веб-серверами. Обычно в ASP.NET (до MVC 5) веб-приложение тесно связано с IIS через System.Web. Но интерфейс веб-API реализует OWIN, который позволяет написать веб-приложение, не связанное с веб-сервером, на котором оно размещается. Таким образом, вы можете использовать веб-сервер OWIN с *саморазмещением*, который можно запускать отдельным процессом, что идеально подходит для описанной выше модели размещения Service Fabric.

В этой статье мы будем использовать Katana в качестве хоста OWIN для приложения веб-API. Katana — это реализация хоста OWIN с открытым исходным кодом.

> [AZURE.NOTE]Дополнительные сведения о Katana см. на [сайте Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Чтобы быстро ознакомиться с тем, как использовать Katana для саморазмещения веб-API, см. статью [Use OWIN to Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api) (Использование OWIN для саморазмещения веб-API 2 для ASP.NET).


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

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

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

 - *OpenAsync* — запуск прослушивания запросов.
 - *CloseAsync* — остановка прослушивания запросов, завершение всех активных запросов и правильное завершение работы.
 - *Abort* — отмена всех операций и немедленная остановка.

Для начала добавьте закрытые члены класса для префикса пути URL-адреса и класса Startup, созданного ранее. Они будут инициализированы с помощью конструктора и в дальнейшем будут использоваться при настройке URL-адреса прослушивания. Кроме того, добавьте закрытые члены класса, чтобы сохранить адрес прослушивания, созданный во время инициализации, а также дескриптор сервера, созданный при запуске сервера.

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;
    private readonly ServiceInitializationParameters serviceInitializationParameters;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
    {
        this.startup = startup;
        this.appRoot = appRoot;
        this.serviceInitializationParameters = serviceInitializationParameters;
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
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

Этот шаг важен, так как при выполнении хост-процесса службы используются учетные данные с ограничением по доступу (для сетевой службы в Windows). Это означает, что у службы не будет доступа для самостоятельной настройки конечной точки HTTP. Платформа Service Fabric использует конфигурацию конечной точки, чтобы определить, как настроить соответствующий список управления доступом (ACL) для URL-адреса, который будет прослушивать служба. Service Fabric также обеспечивает создание стандартного места для настройки конечных точек.


Теперь в OwinCommunicationListener.cs можно начать реализацию OpenAsync. Так начнется запуск веб-сервера. Во-первых, получите информацию о конечной точке и создайте URL-адрес, который будет прослушивать служба.

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
    ...

```

Обратите внимание, что здесь используется адрес «http://+». Это позволяет обеспечить прослушивание веб-сервером всех доступных адресов, в том числе localhost, полного доменного имени и IP-адреса компьютера.

Реализация OpenAsync — одна из важнейших причин, почему веб-сервер (или любой стек связи) реализуется в качестве ICommunicationListener, а не просто открывается напрямую с помощью `RunAsync()` в службе. Значение, возвращаемое OpenAsync, представляет собой адрес, который прослушивается веб-сервером. Когда этот адрес возвращается в систему, она регистрирует адрес в службе. Платформа Service Fabric дает возможность использовать API, который позволяет клиентам и другим службам в дальнейшем запрашивать этот адрес по имени службы. Это важно, так как у службы нестатический адрес. Перемещение служб в кластере осуществляется для балансировки ресурсов и обеспечения их доступности. Это механизм, позволяющий клиентам распознавать адрес прослушивания для службы.

С учетом этого OpenAsync запускает веб-сервер и возвращает адрес, который прослушивает. Обратите внимание, что сервер прослушивает http://+, но перед возвратом адреса от OpenAsync знак "+" заменяется IP-адресом или полным доменным именем узла, на котором он в настоящее время находится. Адрес, возвращаемый с помощью этого метода, зарегистрирован в системе. Его увидят клиенты и другие службы при запросе адреса службы. Для правильного подключения к нему клиентов им требуется фактический IP-адрес или полное доменное имя в адресе.

```csharp

    ...

    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
    string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

    return Task.FromResult(publishAddress);
}

```

Обратите внимание, что при этом выполняется обращение к классу Startup, переданному в OwinCommunicationListener в конструкторе. Этот экземпляр Startup веб-сервер использует для начальной загрузки приложения веб-API.

Позже при запуске приложения в окне "События диагностики" появится строка `ServiceEventSource.Current.Message()` в подтверждение успешного запуска веб-сервера.

## Реализация CloseAsync и Abort

Наконец, необходимо реализовать как CloseAsync, так и Abort для остановки веб-сервера. Веб-сервер может быть остановлен путем утилизации дескриптора сервера, который был создан при выполнении OpenAsync.

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
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

Теперь вы готовы создать и вернуть экземпляр OwinCommunicationListener для запуска веб-сервера. Вернувшись в класс Service (Service.cs), переопределите метод `CreateServiceInstanceListeners()`:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new[]
    {
        new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
    };
}

```

Здесь наконец встречаются *приложение* веб-API и *хост* OWIN. У хоста (OwinCommunicationListener) теперь есть экземпляр *приложения* (веб-API, полученный с использованием Startup). Его жизненным циклом управляет Service Fabric. Такого же шаблона можно придерживаться при использовании любого стека связи.

## Сборка

В этом примере не нужно предпринимать никаких действий в методе `RunAsync()`, поэтому переопределение можно просто удалить.

Окончательная реализация службы будет очень простой, так как для этого требуется только создать прослушиватель связи:

```csharp

namespace WebApiService
{
    using System.Collections.Generic;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;
    using Microsoft.ServiceFabric.Services.Runtime;

    public class WebApiService : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            return new[]
            {
                new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
            };
        }
    }
}

```

Полный класс `OwinCommunicationListener`:

```csharp

namespace WebApiService
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private readonly ServiceInitializationParameters serviceInitializationParameters;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
        {
            this.startup = startup;
            this.appRoot = appRoot;
            this.serviceInitializationParameters = serviceInitializationParameters;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');

            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
            string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

            return Task.FromResult(publishAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            ServiceEventSource.Current.Message("Close");

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            ServiceEventSource.Current.Message("Abort");

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
    }
}

```

Теперь все элементы на своих местах и приложение веб-API должно выглядеть аналогично обычному приложению веб-API с точками входа интерфейса API Reliable Services и хостом OWIN.


![Веб-API с точками входа API Reliable Services и хостом OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Запуск и подключение через веб-браузер

Если это еще не сделано, [настройте среду разработки](service-fabric-get-started.md).


Теперь можно построить и развернуть службу. Нажмите клавишу **F5** в Visual Studio, чтобы выполнить построение и развертывание приложения. В окне "События диагностики" вы увидите сообщение о том, что веб-сервер открыт на http://localhost:80/webapp/api.


![Окно "События диагностики" в Visual Studio](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]Если порт уже открыт в результате работы другого процесса на компьютере, может появиться сообщение об ошибке. Это значит, что прослушиватель не удалось открыть. В этом случае попробуйте использовать другой порт в конфигурации конечной точки в файле ServiceManifest.xml.


После запуска службы откройте браузер и перейдите по адресу [http://localhost/webapp/api/values](http://localhost/webapp/api/values), чтобы проверить ее работу.

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

## ASP.NET 5

В ASP.NET 5 концепция и модель программирования для отделения *приложения* от *хоста* в веб-приложениях остается такой же. Ее можно применять также к другим видам связи. Кроме того, несмотря на то, что *хост* в ASP.NET 5 может отличаться, слой *приложения* веб-API остается тем же. Это место фактического размещения большей части логики приложения.

## Дальнейшие действия

[Отладка приложения Service Fabric с помощью Visual Studio](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0121_2016-->