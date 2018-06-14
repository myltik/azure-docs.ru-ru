---
title: Обмен данными между ролями в облачных службах | Документация Майкрософт
description: Для экземпляров ролей в облачных службах могут быть определены конечные точки (http, https, tcp, udp), взаимодействующие с внешней средой или с другими экземплярами ролей.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
ms.openlocfilehash: 96ca9bb2d7a9f30a7d6492be43bfb44edc02fd93
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2018
ms.locfileid: "27701896"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Включение обмена данными между экземплярами роли в Azure
Ролей облачной службы взаимодействуют через внутренние и внешние подключения. Внешние подключения называются **входными конечными точками**, а внутренние подключения — **внутренними конечными точками**. В этом разделе описывается изменение [определения службы](cloud-services-model-and-package.md#csdef) для создания конечных точек.

## <a name="input-endpoint"></a>Входная конечная точка
Входная конечная точка используется, когда необходимо предоставить порт вовне. Необходимо указать тип протокола и порт конечной точки, которые затем применяются для внешнего и внутреннего портов конечной точки. Если требуется, для конечной точки можно указать другой внутренний порт с помощью атрибута [localPort](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint).

Входная конечная точка может использовать следующие протоколы: **http, https, tcp, udp**.

Чтобы создать входную конечную точку, добавьте дочерний элемент **InputEndpoint** в элемент **Endpoints** веб-роли или рабочей роли.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Входная конечная точка экземпляра
Входные конечные точки экземпляра похожи на входные конечные точки, однако позволяют сопоставить определенные общедоступные порты для каждого отдельного экземпляра роли с помощью перенаправления портов в балансировщик нагрузки. Можно указать один общедоступный порт или диапазон портов.

Входная конечная точка экземпляра может использовать только протокол **TCP** или **UDP**.

Чтобы создать входную конечную точку экземпляра, добавьте дочерний элемент **InstanceInputEndpoint** в элемент **Endpoints** веб-роли или рабочей роли.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Внутренняя конечная точка
Внутренние конечные точки доступны для подключения между экземплярами. Порт является необязательным, и если он не указан, конечной точке назначается динамический порт. Можно использовать диапазон портов. Существует ограничение: до 5 внутренних конечных точек на роль.

Внутренняя конечная точка может использовать следующие протоколы: **http, tcp, udp, any**.

Чтобы создать внутреннюю входную конечную точку, добавьте дочерний элемент **InternalEndpoint** в элемент **Endpoints** веб-роли или рабочей роли.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Можно также использовать диапазон портов.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Рабочие роли и Веб-роли
При работе с веб-ролями и рабочими ролями существует одно незначительное отличие, связанное с конечными точками. У веб-роли должна быть как минимум одна выходная конечная точка, использующая протокол **HTTP** .

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Использование пакета SDK для .NET для доступа к конечной точке
Управляемая библиотека Azure предоставляет методы для обмена данными между экземплярами роли во время выполнения. Из кода, выполняемого в экземпляре роли, можно получить информацию о существовании других экземпляров роли и их конечных точек, а также сведения о текущем экземпляре роли.

> [!NOTE]
> Можно получать сведения только о тех экземплярах, которые запущены в облачной службе и для которых определена по крайней мере одна внутренняя конечная точка. Нельзя получить данные об экземплярах роли, запущенных в другой службе.
> 
> 

Для получения экземпляров роли можно использовать свойство [Instances](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) . Сначала используйте [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx), чтобы вернуть ссылку на текущий экземпляр роли, а затем используйте свойство [Role](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx), чтобы вернуть ссылку на саму роль.

При программном подключении к экземпляру роли с помощью пакета SDK для .NET сравнительно легко получить доступ к информации о конечной точке. Например, после подключения к среде определенной роли можно получить порт определенной конечной точки с помощью этого кода:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

Свойство **Instances** возвращает коллекцию объектов **RoleInstance**. Эта коллекция всегда содержит текущий экземпляр. Если роль не определяет внутреннюю конечную точку, коллекция содержит текущий экземпляр, но не содержит других экземпляров. Число экземпляров роли в коллекции всегда будет равно 1, когда для роли не определена внутренняя конечная точка. Если роль определяет внутреннюю конечную точку, ее экземпляры можно обнаружить во время выполнения, и число экземпляров в коллекции будет соответствовать числу экземпляров, указанных для роли в файле конфигурации службы.

> [!NOTE]
> Управляемая библиотека Azure не предоставляет средства определения работоспособности других экземпляров роли, но вы можете реализовать такие оценки работоспособности самостоятельно, если службе нужна подобная функциональность. Можно использовать [систему диагностики Azure](cloud-services-dotnet-diagnostics.md) для получения информации о выполняемых экземплярах роли.
> 
> 

Чтобы определить номер порта внутренней конечной точки экземпляра роли, можно использовать свойство [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) , чтобы вернуть объект Dictionary, содержащий имена конечных точек и их IP-адреса и порты. Свойство [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) возвращает IP-адрес и порт указанной конечной точкой. Свойство **PublicIPEndpoint** возвращает порт для конечной точки с балансировкой нагрузки. Часть IP-адреса в свойстве **PublicIPEndpoint** не используется.

Вот пример, в котором выполняется итерация экземпляров роли.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Ниже приведен пример рабочей роли, которая получает конечную точку, предоставляемую через определение службы, и начинает прослушивание подключений.

> [!WARNING]
> Этот код будет работать только для развернутой службы. При выполнении в эмуляторе вычислений Azure элементы конфигурации службы, создающие конечные точки прямых портов (элементы**InstanceInputEndpoint** ), игнорируются.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Правила сетевого трафика для управления обменом данными между ролями
После определения внутренних конечных точек можно добавить правила сетевого трафика (на основании созданных конечных точек), чтобы управлять обменом данными между экземплярами роли. На следующей схеме показаны некоторые общие сценарии управления обменом данными между ролями:

![Сценарии использования правил сетевого трафика](./media/cloud-services-enable-communication-role-instances/scenarios.png "Сценарии использования правил сетевого трафика")

В следующем примере кода показаны определения ролей, показанных на предыдущей схеме. В каждом определении роли определена по крайней мере одна внутренняя конечная точка:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Обмен данными между ролями с внутренним конечными точками с фиксированными и автоматически назначенными портами может быть ограничен.
> 
> 

По умолчанию после определения внутренней конечной точки данные могут передаваться из одной роли во внутреннюю конечную точку другой роли без каких-либо ограничений. Чтобы ограничить обмен данными, необходимо добавить элемент **NetworkTrafficRules** в элемент **ServiceDefinition** в файле определения службы.

### <a name="scenario-1"></a>Сценарий 1
Разрешен только сетевой трафик из **WebRole1** в **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Сценарий 2
Разрешен только сетевой трафик из **WebRole1** в **WorkerRole1** и **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Сценарий 3
Разрешен только сетевой трафик из **WebRole1** в **WorkerRole1** и из **WorkerRole1** в **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Сценарий 4
Разрешен только сетевой трафик из **WebRole1** в **WorkerRole1**, из **WebRole1** в **WorkerRole2** и из **WorkerRole1** в **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Справочник по схеме XML для элементов, используемых выше, можно найти [здесь](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Дополнительная информация
Дополнительная информация о [модели](cloud-services-model-and-package.md)облачной службы

