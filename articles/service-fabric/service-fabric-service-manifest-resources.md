---
title: Настройка конечных точек службы Service Fabric | Документация Майкрософт
description: В этой статье поясняется, как описать ресурсы конечной точки в манифесте служб, включая настройку конечных точек HTTPS.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: a028471871754c2b9c3981ec13f5788643675a77
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="specify-resources-in-a-service-manifest"></a>Указание ресурсов в манифесте службы
## <a name="overview"></a>Обзор
Манифест служб позволяет объявлять и изменять ресурсы, используемые в службе, не меняя скомпилированный код. Azure Service Fabric поддерживает настройку ресурсов конечных точек для службы. Доступ к ресурсам, указанным в манифесте служб, можно контролировать в манифесте приложения с помощью элемента SecurityGroup. Объявление ресурсов позволяет изменять их при развертывании, т. е. службе не нужно внедрять новый механизм настройки. Определение схемы для файла ServiceManifest.xml устанавливается с пакетом SDK и средствами для Service Fabric в расположении *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Конечные точки
Если ресурс конечной точки определен в манифесте службы, Service Fabric назначает порты из диапазона зарезервированных портов приложений, если порт не указан явным образом. Например, рассмотрим конечную точку *ServiceEndpoint1* , которая указана во фрагменте кода манифеста, приведенном после абзаца. Кроме того, службы также могут запрашивать наличие в ресурсе конкретного порта. Репликам службы, которые выполняются на различных узлах кластера, можно назначить разные номера портов, а реплики службы, выполняющиеся на одном и том же узле, будут совместно используют один порт. Реплики службы при необходимости могут использовать эти порты для репликации и прослушивания клиентских запросов.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Если в одном пакете службы несколько пакетов кода, на пакет кода также нужно добавить ссылку в разделе **Конечные точки**.  Например, если **ServiceEndpoint2a** и **ServiceEndpoint2b** являются конечными точками из одного и того же пакета службы и ссылаются на разные пакеты кода, пакет кода, который соответствует каждой конечной точке, определяется, как показано ниже:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Дополнительные сведения о создании ссылок на конечные точки из файла параметров пакета конфигурации (settings.xml) см. в статье [Настройка надежных служб с отслеживанием состояния](service-fabric-reliable-services-configuration.md).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Пример. Указание конечной точки HTTP для службы
Следующий манифест служб в элементе &lt;Resources&gt; определяет один ресурс конечной точки TCP и два ресурса конечной точки HTTP.

Service Fabric автоматически создает список управления доступом (ACL) для конечных точек HTTP.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Пример. Указание конечной точки HTTPS для службы
Протокол HTTPS обеспечивает аутентификацию сервера, а также используется для шифрования данных, передаваемых между клиентом сервером. Чтобы включить протокол HTTPS в службе Service Fabric, укажите его в разделе *Ресурсы > Конечные точки > Конечная точка* манифеста служб, как показано выше для конечной точки *ServiceEndpoint3*.

> [!NOTE]
> Протокол службы невозможно изменить при обновлении приложения. Если изменить его во время обновления, то это будет считаться критическим изменением.
> 
> 

Ниже приведен пример ApplicationManifest, который необходимо задать для HTTPS. Требуется предоставить отпечаток для сертификата. EndpointRef является ссылкой на EndpointResource в ServiceManifest, для которого задается протокол HTTPS. Можно добавить несколько элементов Endpointcertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Для кластеров Linux **MY** сохраняет значения по умолчанию в папку **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Переопределение конечных точек в файле ServiceManifest.xml

В ApplicationManifest добавьте раздел ResourceOverrides, который будет находиться на одном уровне с разделом ConfigOverrides. В этом разделе можно задать параметры переопределения для раздела конечных точек в разделе ресурсов, указанном в манифесте служб. Переопределение конечных точек поддерживается в среде выполнения 5.7.217 и пакете SDK 2.7.217 и более поздних версий.

Чтобы переопределить EndPoint в ServiceManifest, используя ApplicationParameters, измените ApplicationManifest следующим образом:

Добавьте новый подраздел ResourceOverrides в раздел ServiceManifestImport.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

В раздел Parameters добавьте следующее:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Теперь при развертывании приложения вы можете передать эти значения в качестве объекта ApplicationParameters, как показано ниже.

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Примечание. Если для ApplicationParameters значения не заданы, мы возвращаемся к значению по умолчанию, предоставленному в ServiceManifest для соответствующей конечной точки.

Например: 

Допустим, в ServiceManifest заданы следующие значения:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Если в ApplicationParameters параметры Port1 и Protocol1 имеют значение или же оно не задано, порт по-прежнему определяет платформа Service Fabric, а в качестве протокола используется TCP.

Предположим, что вы задали неверное значение. Например, для порта задано строковое значение Foo вместо целого числа.  При выполнении команды New-ServiceFabricApplication произойдет ошибка: The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int' (Недопустимый параметр переопределения ServiceEndpoint1 атрибута Port1 в разделе ResourceOverrides. Указано значение Foo, а требуется целое число).
