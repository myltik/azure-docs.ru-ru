---
title: Взаимодействие со службами через ASP.NET Core | Документация Майкрософт
description: Сведения об использовании ASP.NET Core в службах Reliable Services c отслеживанием и без отслеживания состояния.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 7786e08e04d2ebce757b4c47b8ed599036c95958
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207865"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core в Service Fabric Reliable Services

ASP.NET Core — это новая кроссплатформенная среда с открытым кодом для создания современных облачных приложений с подключением к Интернету, таких как веб-приложения, приложения для Интернета вещей и серверные части мобильных приложений. 

Эта статья представляет собой подробное руководство по размещению служб ASP.NET Core в Service Fabric Reliable Services с использованием набора пакетов NuGet **Microsoft.ServiceFabric.AspNetCore.*** .

Вводное руководство по ASP.NET Core в Service Fabric и инструкции по настройке среды разработки см. в статье [Создание и развертывание приложения с интерфейсной службой веб-API ASP.NET Core и серверной службой с отслеживанием состояния](service-fabric-tutorial-create-dotnet-app.md).

В оставшейся части этой статьи предполагается, что вы знакомы с размещением в ASP.NET Core. В противном случае мы рекомендуем ознакомиться с [базовыми понятиями ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core в среде Service Fabric

Хотя приложения ASP.NET Core можно запускать на .NET Core или полной версии платформы .NET Framework, службы Service Fabric в настоящее время могут работать только на полной версии .NET Framework. Это значит, что при создании службы ASP.NET Core Service Fabric по-прежнему необходимо ориентироваться на полную версию .NET Framework.

ASP.NET Core можно использовать двумя различными способами в Service Fabric:
 - **Разместить в виде гостевого исполняемого файла**. В основном это используется для запуска существующих приложений ASP.NET Core в Service Fabric без изменения кода.
 - **Выполнять внутри надежной службы**. Это обеспечивает более эффективную интеграцию со средой выполнения Service Fabric и позволяет использовать службы ASP.NET Core с отслеживанием состояния.

Далее в этой статье объясняется, как использовать ASP.NET Core внутри Reliable Service с помощью компонентов интеграции ASP.NET, поставляемых с пакетом SDK для Service Fabric. 

## <a name="service-fabric-service-hosting"></a>Размещение службы Service Fabric

В Service Fabric один или несколько экземпляров и (или) реплик службы выполняются в *хост-процессе службы* — исполняемом файле, который выполняет код службы. Создателю службы принадлежит хост-процесс службы, который Service Fabric активирует и отслеживает.

Обычно ASP.NET (до MVC 5) тесно связан с IIS через System.Web.dll. ASP.NET Core разделяет веб-сервер и веб-приложение. Это позволяет переносить веб-приложение между различными веб-серверами, а также позволяет веб-серверам быть *автономно размещаемыми*. Это означает, что веб-сервер можно запустить в собственном процессе, в отличие от процесса, который принадлежит выделенному ПО веб-сервера, такому как IIS. 

Чтобы объединить службу Service Fabric и ASP.NET в Reliable Service или в качестве гостевого исполняемого приложения, необходимо запустить ASP.NET в хост-процессе службы. Автономное размещение ASP.NET Core позволяет это сделать.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Размещение ASP.NET Core в Reliable Service
Как правило, автономно размещаемые приложения ASP.NET Core создают WebHost в точке входа приложения, например метод `static void Main()` в `Program.cs`. В таком случае жизненный цикл WebHost привязан к жизненному циклу процесса.

![Размещение ASP.NET Core в процессе][0]

Точка входа приложения не подходит для создания WebHost в Reliable Service, так как она используется только для регистрации типа службы в среде выполнения Reliable Service, чтобы иметь возможность создавать экземпляры этого типа службы. WebHost должен быть создан в Reliable Service автоматически. В хост-процессе службы экземпляры службы и (или) реплики могут проходить несколько жизненных циклов. 

Экземпляр Reliable Service представлен с помощью класса службы, производного от `StatelessService` или `StatefulService`. Стек связи для службы содержится в реализации `ICommunicationListener` класса службы. Пакеты NuGet `Microsoft.ServiceFabric.Services.AspNetCore.*` содержат реализации `ICommunicationListener`, которые запускают и администрируют ASP.NET Core WebHost для Kestrel или HttpSys в Reliable Service.

![Размещение ASP.NET Core в Reliable Service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Объекты ICommunicationListener в ASP.NET Core
Реализации `ICommunicationListener` Kestrel и HttpSys в пакетах NuGet `Microsoft.ServiceFabric.Services.AspNetCore.*` имеют похожие шаблоны использования, но выполняют несколько различные действия для каждого веб-сервера. 

Оба прослушивателя связи предоставляют конструктор, который принимает следующие аргументы:
 - **`ServiceContext serviceContext`**. Объект `ServiceContext`, содержащий сведения о выполняемой службе.
 - **`string endpointName`**. Имя конфигурации `Endpoint` в файле ServiceManifest.xml. Это основное отличие прослушивателей связи: для HttpSys **требуется** конфигурация `Endpoint`, а для Kestrel — нет.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**. Реализуемое лямбда-выражение, в котором создается и возвращается `IWebHost`. Это позволяет настроить `IWebHost` так же, как в приложении ASP.NET Core. Лямбда-выражение содержит URL-адрес, который создается в зависимости от используемых параметров интеграции Service Fabric и предоставляемой конфигурации `Endpoint`. Этот URL-адрес можно изменить или использовать "как есть" для запуска веб-сервера.

## <a name="service-fabric-integration-middleware"></a>ПО промежуточного уровня для интеграции Service Fabric
Пакет NuGet `Microsoft.ServiceFabric.Services.AspNetCore` содержит метод расширения `UseServiceFabricIntegration` в `IWebHostBuilder`, который добавляет ПО промежуточного слоя, поддерживающее Service Fabric. Это ПО промежуточного слоя настраивает `ICommunicationListener` Kestrel или HttpSys, чтобы зарегистрировать уникальный URL-адрес службы с помощью службы именования Service Fabric, а затем проверяет запросы клиентов, чтобы клиенты подключались к правильной службе. Это необходимо в среде общего узла, например Service Fabric, где несколько веб-приложений могут работать на одном физическом компьютере или виртуальной машине, но не используют уникальные имена узлов, чтобы предотвратить ошибочные подключения клиентов к неверной службе. Данный сценарий описан более подробно в следующем разделе.

### <a name="a-case-of-mistaken-identity"></a>Причина ошибочный идентификации
Реплики службы, независимо от протокола, прослушивают уникальное сочетание "IP-адрес:порт". Начав прослушивать, реплика службы конечной точки "IP-адрес:порт" сообщает адрес этой конечной точки службе именования Service Fabric, в которой его могут обнаружить клиенты или другие службы. Если службы используют динамически назначаемые порты приложения, реплика службы может случайно использовать ту же конечную точку "IP-адрес:порт" другой службы, которая ранее находилась на том же физическом компьютере или виртуальной машине. Это может привести к ошибочному подключению клиента к неверной службе, что может произойти, если возникает следующая последовательность событий:

 1. Служба А прослушивает 10.0.0.1:30000 по протоколу HTTP. 
 2. Клиент разрешает службу А и возвращает адрес 10.0.0.1:30000.
 3. Служба А перемещается на другой узел.
 4. Служба Б помещается в 10.0.0.1 и случайно использует тот же порт 30000.
 5. Клиент пытается подключиться к службе А с помощью кэшированного адреса 10.0.0.1:30000.
 6. Теперь клиент подключен к службе Б, не зная, что подключен к неверной службе.

Это может периодически вызывать ошибки, которые трудно диагностировать. 

### <a name="using-unique-service-urls"></a>Использование уникальных URL-адресов службы
Во избежание этого службы могут разместить конечную точку в службе имен с уникальным идентификатором, а затем проверять его во время обработки клиентских запросов. Это совместное действие между службами в доверенной среде дружественного клиента. Это не обеспечивает безопасную проверку подлинности службы в среде недружественного клиента.

В доверенной среде ПО промежуточного слоя, которое добавляется с помощью метода `UseServiceFabricIntegration`, автоматически добавляет уникальный идентификатор к адресу, размещенному в службе именования, и проверяет этот идентификатор в каждом запросе. Если идентификатор не совпадает, ПО промежуточного слоя немедленно возвращает ответ HTTP 410 — потеряно.

Службы, использующие динамически назначаемый порт, должны использовать это ПО промежуточного слоя.

Службы, использующие постоянный уникальный порт, не имеют такой проблемы в совместной среде. Постоянный уникальный порт обычно используется для внешних служб, которым необходим известный порт для подключения клиентских приложений. Например, большинство веб-приложений с доступом к Интернету используют порт 80 или 443 для подключений веб-браузера. В этом случае не следует включать уникальный идентификатор.

На схеме ниже показан поток запроса с включенным ПО промежуточного слоя.

![Интеграция Service Fabric ASP.NET Core][2]

Реализации `ICommunicationListener` Kestrel и HttpSys используют этот механизм точно таким же образом. Несмотря на то что HttpSys может внутренне различать запросы на основе уникальных URL-адресов с помощью базовой функции совместного использования портов *http.sys*, эта функция *не* используется реализацией `ICommunicationListener` HttpSys, так как это приведет к ошибкам с кодами состояния HTTP 503 и HTTP 404 в сценарии, описанном выше. В свою очередь для клиентов это усложняет определение причины ошибки, так как HTTP 503 и HTTP 404 часто используются для указания других ошибок. Таким образом, реализации `ICommunicationListener` Kestrel и HttpSys стандартизируют ПО промежуточного слоя, предоставляемое методом расширения `UseServiceFabricIntegration`, чтобы клиенты выполняли только одно действие повторного разрешения конечной точки службы в ответах HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HttpSys в Reliable Services
HttpSys можно использовать в Reliable Service, импортировав пакет NuGet **Microsoft.ServiceFabric.AspNetCore.HttpSys**. Этот пакет содержит `HttpSysCommunicationListener`, реализацию `ICommunicationListener`, которая позволяет создавать WebHost ASP.NET Core внутри Reliable Service с помощью HttpSys в качестве веб-сервера.

HttpSys основан на [API сервера HTTP Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). При этом используется драйвер ядра *http.sys*, применяемый IIS для обработки HTTP-запросов и их перенаправления в процессы, выполняющие веб-приложения. Это позволяет нескольким процессам на одном физическом компьютере или виртуальной машине размещать на одном порту веб-приложения, которые отличаются уникальным URL-адресом или именем узла. Эти функции в Service Fabric полезны для размещения нескольких веб-сайтов в одном кластере.

На следующей схеме показано, как HttpSys использует драйвер ядра *http.sys* в Windows для совместного использования портов:

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys в службе без отслеживания состояния
Для использования `HttpSys` в службе без отслеживания состояния переопределите метод `CreateServiceInstanceListeners` и верните экземпляр `HttpSysCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HttpSys в службе с отслеживанием состояния

`HttpSysCommunicationListener` в настоящее время не предназначен для использования в службах с отслеживанием состояния из-за сложности с базовой функцией совместного использования портов *http.sys*. Дополнительные сведения о динамическом назначении портов HttpSys см. в следующем разделе. Для служб с отслеживанием состояния рекомендуемым веб-сервером является Kestrel.

### <a name="endpoint-configuration"></a>Настройка конечной точки

Для веб-серверов, использующих API сервера HTTP Windows (включая HttpSys), требуется конфигурация `Endpoint`. Для веб-серверов, использующих API сервера HTTP Windows, сначала необходимо зарезервировать URL-адрес в *http.sys* (обычно это осуществляется с помощью средства [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx)). Для этого действия требуются повышенные привилегии, которых по умолчанию нет у служб. Параметры HTTP или HTTPS для свойства `Protocol` конфигурации `Endpoint` в файле *ServiceManifest.xml* используются специально для того, чтобы сообщить среде выполнения Service Fabric о необходимости регистрации URL-адреса в *http.sys* от вашего имени с помощью [*надежного шаблона*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) префикса URL-адреса.

Например, чтобы зарезервировать `http://+:80` для службы, в файле ServiceManifest.xml следует использовать следующую конфигурацию:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Также необходимо передать имя конечной точки в конструктор `HttpSysCommunicationListener`.

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Использование статического порта с HttpSys
Чтобы использовать статический порт с HttpSys, укажите номер порта в конфигурации `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Использование динамического порта с HttpSys
Чтобы использовать динамически назначаемый порт с HttpSys, опустите свойство `Port` в конфигурации `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Обратите внимание, что динамический порт, выделенный с помощью конфигурации `Endpoint`, предоставляет только один порт *каждому хост-процессу*. Текущая модель размещения Service Fabric позволяет размещать в одном процессе несколько экземпляров службы и (или) реплик. Это означает, что они будут совместно использовать один и тот же порт при выделении с помощью конфигурации `Endpoint`. Множество экземпляров HttpSys могут совместно использовать порт с помощью базовой функции совместного использования портов *http.sys*. Это не поддерживается `HttpSysCommunicationListener` из-за сложности обработки клиентских запросов. Для использования динамических портов рекомендуемым веб-сервером является Kestrel.

## <a name="kestrel-in-reliable-services"></a>Kestrel в Reliable Services
Kestrel можно использовать в Reliable Service, импортировав пакет NuGet **Microsoft.ServiceFabric.AspNetCore.Kestrel**. Этот пакет содержит `KestrelCommunicationListener`, реализацию `ICommunicationListener`, которая позволяет создавать WebHost ASP.NET Core внутри Reliable Service с помощью Kestrel в качестве веб-сервера.

Kestrel — это кроссплатформенный веб-сервер для ASP.NET Core на основе libuv, кроссплатформенной библиотеки асинхронных операций ввода-вывода. В отличие от HttpSys, Kestrel не использует централизованный диспетчер конечных точек, такой как *http.sys*, и не поддерживает совместное использование порта между несколькими процессами. Каждый экземпляр Kestrel должен использовать уникальный порт.

![Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel в службе без отслеживания состояния
Для использования `Kestrel` в службе без отслеживания состояния переопределите метод `CreateServiceInstanceListeners` и верните экземпляр `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel в службе с отслеживанием состояния
Для использования `Kestrel` в службе с отслеживанием состояния переопределите метод `CreateServiceReplicaListeners` и верните экземпляр `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

В этом примере одноэлементный экземпляр `IReliableStateManager` предоставляется контейнеру внедрения зависимостей WebHost. Это не является обязательным, но позволяет использовать `IReliableStateManager` и надежные коллекции в методах действий контроллера MVC.

Обратите внимание, что имя конфигурации `Endpoint` **не** предоставляется `KestrelCommunicationListener` в службе с отслеживанием состояния. Это объясняется более подробно в следующем разделе.

### <a name="endpoint-configuration"></a>Настройка конечной точки
Для использования Kestrel конфигурация `Endpoint` не требуется. 

Kestrel является простым автономным веб-сервером, для которого, в отличие от HttpSys (или HttpListener), не требуется конфигурация `Endpoint` в файле *ServiceManifest.xml*, так как для запуска регистрация URL-адреса не требуется. 

#### <a name="use-kestrel-with-a-static-port"></a>Использование Kestrel и статического порта
Можно настроить статический порт в конфигурации `Endpoint` файла ServiceManifest.xml для использования с Kestrel. Хотя это не является обязательным, это дает два потенциальных преимущества:
 1. Если порт не попадает в диапазон портов для приложений, он открывается в брандмауэре ОС с помощью Service Fabric.
 2. URL-адрес, предоставленный с помощью `KestrelCommunicationListener`, будет использовать этот порт.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Если `Endpoint` настроена, ее имя необходимо передать в конструктор `KestrelCommunicationListener`. 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Если конфигурация `Endpoint` не используется, не указывайте имя конечной точки в конструкторе `KestrelCommunicationListener`. В этом случае будет использоваться динамический порт. Этот процесс описан в следующем разделе.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Использование Kestrel и динамического порта
Kestrel не может использовать автоматическое назначение порта из конфигурации `Endpoint` в файле ServiceManifest.xml, так как автоматическое назначение порта из конфигурации `Endpoint` назначает уникальный порт каждому *хост-процессу*. В результате один хост-процесс может содержать несколько экземпляров Kestrel. Так как Kestrel не поддерживает совместное использование портов, это не будет работать, потому что каждый экземпляр Kestrel должен быть открыт на уникальном порте.

Чтобы использовать динамическое назначение порта в Kestrel, достаточно полностью опустить конфигурацию `Endpoint` в файле ServiceManifest.xml и не передавать имя конечной точки в конструктор `KestrelCommunicationListener`.

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

В этой конфигурации `KestrelCommunicationListener` автоматически выберет неиспользуемый порт из диапазона портов приложения.

## <a name="scenarios-and-configurations"></a>Сценарии и конфигурации
В этом разделе описаны следующие сценарии и приведено рекомендуемое сочетание веб-сервера, конфигурации порта, параметров интеграции Service Fabric и прочих параметров для обеспечения правильной работы службы.
 - Доступная извне служба ASP.NET Core без отслеживания состояния
 - Служба ASP.NET Core без отслеживания состояния только для внутреннего использования
 - Служба ASP.NET Core с отслеживанием состояния только для внутреннего использования

**Доступная извне** служба предоставляет конечную точку, доступную за пределами кластера, обычно через балансировщик нагрузки.

Служба **только для внутреннего использования** — это служба, конечная точка которой доступна только в пределах кластера.

> [!NOTE]
> Конечные точки службы с отслеживанием состояния не следует делать доступными в Интернете. Кластеры, находящиеся за балансировщиками нагрузки, которые не поддерживают разрешение служб Service Fabric (например, Azure Load Balancer), не смогут предоставить службы с отслеживанием состояния, так как балансировщик нагрузки не сможет обнаруживать и направлять трафик в соответствующую реплику службы с отслеживанием состояния. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Доступные извне службы ASP.NET Core без отслеживания состояния
HttpSys является рекомендуемым веб-сервером для внешних служб, которые предоставляют внешние конечные точки HTTP с доступом в Интернет. В Windows HttpSys дает возможность совместно использовать порты, что позволяет размещать несколько веб-служб на одном и том же наборе узлов с использованием одного и того же порта, дифференцированного по имени узла или пути, не полагаясь на внешний прокси-сервер или шлюз для маршрутизации HTTP-трафика.
 
Доступная через Интернет, служба без отслеживания состояния должна использовать хорошо известную и стабильную конечную точку, доступную через балансировщик нагрузки. Это URL-адрес, который будет предоставляться пользователям приложения. Рекомендуется следующая конфигурация:

|  |  | **Примечания** |
| --- | --- | --- |
| Веб-сервер | Kestrel | Kestrel является предпочтительным веб-сервером, так как он поддерживается в Windows и Linux. |
| Конфигурация порта | static | Хорошо известный статический порт необходимо настроить в конфигурации `Endpoints` файла ServiceManifest.xml, например 80 для HTTP и 443 для HTTPS. |
| ServiceFabricIntegrationOptions | None | Параметр `ServiceFabricIntegrationOptions.None` должен использоваться при настройке ПО промежуточного слоя интеграции Service Fabric, чтобы служба не пыталась проверять входящие запросы для уникального идентификатора. Внешние пользователи приложения не будут знать уникальные идентификаторы, используемые ПО промежуточного слоя. |
| Число экземпляров | -1 | При стандартных вариантах использования значение количества экземпляров должно быть равно "-1", чтобы экземпляр становился доступным на всех узлах, получающих трафик от балансировщика нагрузки. |

Если несколько служб, предоставляемых извне, совместно используют один и тот же набор узлов, HttpSys следует использовать с уникальным, но стабильным URL-адресом. Это можно сделать, изменив URL-адрес, указанный при настройке IWebHost. Обратите внимание, что это относится только к HttpSys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Служба ASP.NET Core без отслеживания состояния только для внутреннего использования
Службы без отслеживания состояния, которые вызываются только из кластера, должны использовать уникальные URL-адреса и динамически назначаемые порты для обеспечения взаимодействия между несколькими службами. Рекомендуется следующая конфигурация:

|  |  | **Примечания** |
| --- | --- | --- |
| Веб-сервер | Kestrel | Несмотря на то что HttpSys может использоваться для внутренних служб без отслеживания состояния, Kestrel является рекомендуемым сервером, на котором разрешено совместно использовать узел нескольким экземплярам службы.  |
| Конфигурация порта | динамическое назначение | Множество реплик службы с отслеживанием состояния могут совместно использовать хост-процесс или операционную систему, и поэтому для них требуются уникальные порты. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | При динамическом назначении порта этот параметр предотвращает ошибочную идентификацию, описанную ранее. |
| InstanceCount | любой | Для параметра количества экземпляров может быть присвоено любое значение, необходимое для работы службы. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Служба ASP.NET Core с отслеживанием состояния только для внутреннего использования
Службы с отслеживанием состояния, которые вызываются только из кластера, должны использовать динамически назначаемые порты для обеспечения взаимодействия между несколькими службами. Рекомендуется следующая конфигурация:

|  |  | **Примечания** |
| --- | --- | --- |
| Веб-сервер | Kestrel | `HttpSysCommunicationListener` не предназначен для служб с отслеживанием состояния, в которых реплики совместно используют хост-процесс. |
| Конфигурация порта | динамическое назначение | Множество реплик службы с отслеживанием состояния могут совместно использовать хост-процесс или операционную систему, и поэтому для них требуются уникальные порты. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | При динамическом назначении порта этот параметр предотвращает ошибочную идентификацию, описанную ранее. |

## <a name="next-steps"></a>Дополнительная информация
[Отладка приложения Service Fabric с помощью Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
