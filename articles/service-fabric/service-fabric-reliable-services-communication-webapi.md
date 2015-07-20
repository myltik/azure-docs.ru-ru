<properties
   pageTitle="Службы веб-API Service Fabric с саморазмещением OWIN | Microsoft Azure"
   description="Эта статья о Service Fabric содержит сведения о том, как реализовать взаимодействие служб с использованием веб-API ASP.NET с саморазмещением OWIN в надежных службах."
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
   ms.date="05/18/2015"
   ms.author="vturecek"/>

# Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN

Платформа Service Fabric дает вам возможность решать, как разрабатываемые службы должны взаимодействовать с пользователями и друг с другом. Это руководство посвящено реализации взаимодействия служб с помощью веб-API ASP.NET с саморазмещением OWIN в интерфейсе API *надежных служб* Service Fabric. Мы подробно рассмотрим интерфейс API подключаемой связи *надежных служб* и шаг за шагом покажем, как настроить специальный прослушиватель связи для вашей службы с использованием веб-API в качестве примера. Чтобы просмотреть полный пример прослушивателя связи веб-API, извлеките [образец Service Fabric WebApplication на GitHub](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication).


## Введение в веб-API в Service Fabric

Веб-API ASP.NET — это популярная и мощная платформа для построения интерфейсов HTTP API поверх .NET Framework. На сайте [www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) можно узнать больше о веб-API, если вы еще не ознакомились с этим интерфейсом.

Веб-API в Service Fabric — это тот же веб-API ASP.NET, который вы знаете и любите. Разница в том, как выполняется *размещение* приложения веб-API (подсказка: при этом не используется IIS). Чтобы лучше понять разницу, разобьем систему на две части.

 1. Приложение веб-API (ваши контроллеры, модели и т. д.).
 2. Хост (веб-сервер, обычно IIS).

Само приложение веб-API здесь не меняется — оно не отличается от приложений веб-API, которые вы писали в прошлом, и есть возможность просто переместить сюда большую часть кода приложения. Размещение приложения может немного отличаться от привычного, если раньше вы для этого пользовались IIS. Но прежде чем мы перейдем к размещению, давайте начнем с более привычного — приложения веб-API.


## Настройка приложения веб-API

Начните с создания новой службы без отслеживания состояния в Visual Studio 2015.

![](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Это даст нам пустую службу без отслеживания состояния, в которой будет размещено приложение веб-API. Мы настроим приложение с нуля, чтобы увидеть, как все компоненты согласуются между собой.

На первом шаге потребуется задействовать некоторые пакеты NuGet для веб-API. Нужный нам пакет — **Microsoft.AspNet.WebApi.OwinSelfHost**. Данный пакет содержит все необходимые пакеты веб-API и пакеты *хоста* — это будет важно в дальнейшем.

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

После установки пакетов мы можем начать формировать базовую структуру проекта веб-API. Если вы уже использовали веб-API, структура проекта покажется очень знакомой. Начните с создания базовых каталогов веб-API:

 + App_Start;
 + Controllers;
 + Models.

Добавьте базовые классы конфигурации веб-API в каталог App_Start:

 + FormatterConfig.cs;

```csharp

namespace WebApi
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

 + RouteConfig.cs.

```csharp

namespace WebApi
{
    using System.Web.Http;

    public static class RouteConfig
    {
        public static void RegisterRoutes(HttpRouteCollection routes)
        {
            routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { controller = "Default", id = RouteParameter.Optional }
                );
        }
    }
}

```

Добавьте контроллер по умолчанию в каталог Controllers:

 + DefaultController.cs.

```csharp

namespace WebApi.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    public class DefaultController : ApiController
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

Наконец, добавьте класс Startup в корень проекта для регистрации маршрутизации, средств форматирования и другой конфигурационной настройки. Это также точка подключения веб-API к *хосту*, что мы еще раз рассмотрим позднее. При настройке класса Startup создайте интерфейс *IOwinAppBuilder* для класса Startup, который определяет метод Configuration. Хотя технически это не требуется для работы веб-API, это предоставит дополнительную гибкость при использовании класса Startup в дальнейшем.

 + Startup.cs.

```csharp

namespace WebApi
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            FormatterConfig.ConfigureFormatters(config.Formatters);
            RouteConfig.RegisterRoutes(config.Routes);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs.

```csharp

namespace WebApi
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

Теперь со стороны приложения все готово. К этому моменту мы только настроили базовую структуру проекта веб-API. Она не сильно должна отличаться от проектов веб-API, которые вы могли создавать в прошлом, или базового шаблона веб-API. Бизнес-логика реализуется в контроллерах и моделях как обычно.

Что же теперь следует сделать с размещением для реального запуска?


## Размещение службы

В Service Fabric служба запускается в *хост-процессе службы*, исполняемом модуле, который выполняет код службы. При написании службы с использованием интерфейса API надежных служб, а фактически в большинстве случаев при написании службы для Service Fabric на .NET, проект службы просто компилируется в EXE-файл, который регистрирует тип службы и выполняет код. Если открыть **Program.cs** в проекте службы без отслеживания состояния, отобразится приведенный ниже код.

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType(Service.ServiceTypeName, typeof(Service));

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

![](media/service-fabric-reliable-services-communication-webapi/webapi-projectproperties.png)

Подробности хост-процесса службы и регистрации службы выходят за пределы этой статьи, но на данном этапе важно знать, что **код вашей службы выполняется в собственном процессе**.

## Саморазмещение веб-API с использованием хоста OWIN

Учитывая, что код приложения веб-API размещается в собственном процессе, как мы его подключим к веб-серверу? Для этого есть [OWIN](http://owin.org/). OWIN — это просто контракт между веб-приложениями .NET и веб-серверами. Традиционно в ASP.NET до MVC 5 веб-приложение было тесно связано с IIS через System.Web. Однако интерфейсом веб-API реализуется контракт OWIN, который позволяет написать веб-приложение, отвязанное от веб-сервера, на котором оно размещается. Это позволяет использовать веб-сервер OWIN с *саморазмещением*, который можно запускать в собственном процессе, что идеально подходит для только что описанной модели размещения Service Fabric.

В этой статье мы будем использовать Katana в качестве хоста OWIN для приложения веб-API. Katana — это реализация хоста OWIN с открытым исходным кодом.

> [AZURE.NOTE]Дополнительные сведения о Katana можно узнать на [сайте Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana), а чтобы быстро ознакомиться с тем, как использовать Katana для саморазмещения веб-API, см. статью [Использование OWIN для саморазмещения веб-API 2 для ASP.NET](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## Настройка веб-сервера

Интерфейс API надежных служб предоставляет две точки входа для бизнес-логики.

 + Метод точки входа с открытым окончанием, где можно начать выполнение любой требуемой рабочей нагрузки. Этот метод предназначен в основном для долго выполняющихся вычислений.

```csharp

protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}

```

 + Точка входа связи, где можно подключить любой стек связи по выбору.

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

Веб-сервер, как и любой другой стек связи, который может использоваться в будущем, например WebSockets, должен использовать интерфейс ICommunicationListener для корректной интеграции с системой. Причины для этого станут понятны на следующих шагах.

Сначала создайте класс с именем OwinCommunicationListener, который реализует ICommunicationListener:

 + OwinCommunicationListener.cs:

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
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

Интерфейс ICommunicationListener предоставляет 4 метода для управления прослушивателем связи для службы:

 + **Initialize** — здесь обычно настраивается адрес, который будет прослушиваться службой. Для веб-сервера здесь настраивается URL-адрес.
 + **OpenAsync** — начало прослушивания запросов.
 + **CloseAsync** — остановка прослушивания запросов, завершение всех активных запросов и корректное завершение работы.
 + **Abort** — отмена всех операций и немедленная остановка.

Для начала добавьте закрытые члены класса для префикса пути URL-адреса и класса **Startup**, который был создан ранее. Они будут инициализированы посредством конструктора и использованы в дальнейшем при настройке URL-адреса прослушивания. Кроме того, добавьте закрытые члены класса для сохранения адреса прослушивания и дескриптора сервера, созданных соответственно во время инициализации и последующего запуска сервера.

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
    {
        this.startup = startup;
        this.appRoot = appRoot;
    }

    ...

```

### Initialize

Здесь настраивается URL-адрес веб-сервера. Для этого необходима такая информация:

 + **Префикс пути URL-адреса**. Хотя это необязательно, его рекомендуется настроить сейчас, чтобы можно было безопасно разместить в приложении несколько веб-служб.
 + **Порт**.

Прежде чем назначить порт для веб-сервера, важно понимать, что платформа Service Fabric предоставляет слой приложения, который служит буфером между приложением и базовой операционной системой, в котором оно выполняется. Таким образом, Service Fabric предоставляет способ настройки *конечных точек* для ваших служб. Service Fabric обеспечивает доступность конечной точки для службы, чтобы вам не нужно было настраивать ее самостоятельно в среде базовой ОС. Это позволяет легко размещать приложение Service Fabric в различных средах без необходимости вносить изменения в приложение (например, можно разместить одно и то же приложение как в Azure, так и в собственном центре обработки данных).

Настройте конечную точку HTTP в файле PackageRoot\\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

Этот шаг важен, так как хост-процесс службы выполняется с ограниченными учетными данными (Network Service в Windows), что означает, что у службы не будет доступа для самостоятельной настройки конечной точки HTTP. Используя конфигурацию конечной точки, платформа Service Fabric знает, как настроить соответствующий ACL для URL-адреса, который будет прослушиваться службой, во время предоставления стандартного места для настройки конечных точек.

Вернувшись к файлу OwinCommunicationListener.cs, получите сведения о конечной точке в методе Initialize, чтобы получить порт. Создайте URL-адрес, который будет прослушивать служба, и сохраните его в ранее созданной переменной, которая является членом класса. Этот адрес будет использоваться в OpenAsync для запуска веб-сервера.

```csharp

public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
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
}

```

Обратите внимание, что здесь используется адрес «http://+». Это позволяет обеспечить прослушивание веб-сервером всех доступных адресов, включая localhost, полное доменное имя и IP-адрес компьютера.

### OpenAsync

OpenAsync вызывается платформой после инициализации. Здесь используется адрес, созданный в методе Initialize, для открытия веб-сервера.

Реализация OpenAsync является одной из важнейших причин, почему веб-сервер (или любой стек связи) реализуется как ICommunicationListener, а не просто открывается непосредственно из RunAsync() в Service. Значение, возвращаемое OpenAsync, представляет собой адрес, который прослушивается веб-сервером. Когда этот адрес возвращается в систему, она регистрирует адрес в службе. Платформа Service Fabric дает возможность использовать интерфейс API, позволяющий клиентам или другим службам в дальнейшем запрашивать этот адрес по имени службы. Это важно, так как службы перемещаются по кластеру в целях доступности и балансировки ресурсов, и поэтому адрес службы не является статическим. Это механизм, позволяющий клиентам распознавать адрес прослушивания для службы.

С учетом этого OpenAsync запускает веб-сервер и возвращает адрес, который им прослушивается. Обратите внимание, что прослушивается адрес «http://+», но перед его возвратом знак «+» заменяется IP-адресом или полным доменным именем узла, на котором он в настоящее время находится. Дело в том, что адрес, возвращаемый методом, является адресом, который зарегистрирован в системе и который увидят клиенты или другие службы при запросе адреса данной службы. Для правильного подключения к нему клиентов им требуется фактический IP-адрес или полное доменное имя в адресе.

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

    string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

    return Task.FromResult(resultAddress);
}

```

Обратите внимание, что при этом производится обращение к классу **Startup**, который был передан в OwinCommunicationListener в конструкторе. Этот экземпляр Startup используется веб-сервером для начальной загрузки приложения веб-API.

Строка ServiceEventSource.Current.Message() появится в окне событий диагностики позже при запуске приложения, чтобы сообщить, что веб-сервер успешно запущен.

### CloseAsync и Abort

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

В данном примере реализации как CloseAsync, так и Abort просто останавливают веб-сервер. Вы можете завершить работу веб-сервера в CloseAsync более скоординировано. Например, вы можете дождаться завершения выполняющихся запросов перед возвратом.

## Запуск веб-сервера

Теперь вы готовы создать и вернуть экземпляр OwinCommunicationListener для запуска веб-сервера. Вернувшись в класс Service (Service.cs), переопределите метод **CreateCommunicationListener()**:

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
	return new OwinCommunicationListener("api", new Startup());
}

```

Здесь наконец встречаются *приложение* веб-API и *хост* OWIN: *хост* (**OwinCommunicationListener**) получает экземпляр *приложения* (веб-API через **Startup**), а Service Fabric управляет его жизненным циклом. Такого же шаблона можно придерживаться при использовании любого стека связи.

## Сборка

В этом примере не нужно предпринимать никаких действий в методе RunAsync(), поэтому переопределение можно просто удалить.

Окончательная реализация класса Service будет очень простой, так как ему требуется только создать прослушиватель связи.

```csharp

namespace WebApi
{
    using Microsoft.ServiceFabric.Services;

    public class Service : StatelessService
    {
        public const string ServiceTypeName = "WebApiType";

        protected override ICommunicationListener CreateCommunicationListener()
        {
            return new OwinCommunicationListener("api", new Startup());
        }
    }
}

```

И полный класс OwinCommunicationListener.

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
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
        {
            this.startup = startup;
            this.appRoot = appRoot;
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
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
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

            string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

            return Task.FromResult(resultAddress);
        }

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
    }
}

```

После завершения всех элементов проект будет выглядеть подобно типичному приложению веб-API с точками входа интерфейса API надежных служб и хостом OWIN.


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Запуск и подключение через веб-браузер

Если это еще не сделано, [настройте среду разработки](service-fabric-get-started.md).


Теперь можно построить и развернуть службу. Нажмите клавишу **F5** в Visual Studio, чтобы выполнить построение и развертывание приложения. В окне «События диагностики» вы увидите сообщение, указывающее, что веб-сервер открылся на **http://localhost:80/api**


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]Если порт уже открыт другим процессом на компьютере, может появиться ошибка, указывающая, что прослушиватель не удалось открыть. В таком случае попробуйте использовать другой порт в конфигурации конечной точки (Endpoint) в файле ServiceManifest.xml.


После того как служба запустится, откройте браузер и перейдите по адресу [http://localhost/api](http://localhost/api) для проверки.

## Масштабирование

Масштабирование веб-приложений без отслеживания состояния обычно означает добавление новых компьютеров и запуск на них веб-приложения. Механизм оркестровки Service Fabric может обеспечивать это всякий раз при добавлении новых узлов в кластер. При создании экземпляров службы без отслеживания состояния можно указать количество экземпляров, которые следует создать. Платформа Service Fabric разместит соответствующее количество экземпляров на узлах кластера надлежащим образом, следя за тем, чтобы не создать более одного экземпляра на каком-либо одном узле. Кроме того, вы можете настроить Service Fabric, чтобы эта платформа всегда создавала экземпляр на каждом узле, указав значение «-1» для количества экземпляров. Это гарантирует, что каждый раз, когда добавляются узлы для масштабирования кластера, на новых узлах будет создаваться экземпляр службы без отслеживания состояния. Это значение является свойством экземпляра службы, поэтому оно задается при создании экземпляра службы с помощью PowerShell.

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Оно задается также при определении службы по умолчанию в проекте службы без отслеживания состояния в Visual Studio.

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Дополнительные сведения о создании экземпляров приложений и служб см. в разделе [о развертывании и удалении приложений](service-fabric-deploy-remove-applications.md).

## ASP.NET 5

В ASP.NET 5 концепция и модель программирования для отделения *приложения* от *хоста* в веб-приложениях остается такой же. Ее можно применять также к другим видам связи. Кроме того, хотя *хост* в ASP.NET 5 может отличаться, слой *приложения* веб-API остается тем же — это место фактического размещения большей части логики приложения.

## Дальнейшие действия

[Отладка приложения Service Fabric в Visual Studio](service-fabric-debugging-your-application.md)
 

<!---HONumber=July15_HO2-->