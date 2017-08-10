---
title: "Средство Docker Compose для Azure Service Fabric (предварительная версия) | Документация Майкрософт"
description: "Платформа Azure Service Fabric принимает формат Docker Compose для упрощения управления существующими контейнерами с помощью Service Fabric. Сейчас эта возможность доступна в предварительной версии."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/27/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 781431dc8dd576e6fb6807ade4a0a1e909abcab7
ms.contentlocale: ru-ru
ms.lasthandoff: 07/28/2017

---

# <a name="specifying-volume-plugins-and-logging-drivers-for-your-container"></a>Указание подключаемых модулей томов и драйверов ведения журналов для контейнера

Service Fabric поддерживает указание [подключаемых модулей томов Docker](https://docs.docker.com/engine/extend/plugins_volume/) и [драйверов ведения журналов Docker](https://docs.docker.com/engine/admin/logging/overview/) для службы контейнеров. Подключаемые модули указываются в манифесте приложения, как показано в следующем примере:


```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myexternalvolume" Destination="c:\testmountlocation3" Driver="sf" IsReadOnly="true"></Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

В приведенном выше примере тег `Source` для `Volume` указывает на исходную папку. Исходной папкой может быть папка в виртуальной машине, в которой размещаются контейнеры, или постоянное удаленное хранилище. Тег `Destination` — это расположение, с которым сопоставляется `Source` в работающем контейнере. 

При указании подключаемого модуля тома Service Fabric автоматически создает том, используя заданные параметры. Тег `Source` — это имя тома, а тег `Driver` указывает подключаемый модуль драйвера тома. Параметры можно указать с помощью тега `DriverOption`, как показано в следующем фрагменте:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azurefile" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Если задан драйвер ведения журналов Docker, необходимо развернуть агенты (или контейнеры) для обработки журналов в кластере.  Тег `DriverOption` также может использоваться для указания параметров драйвера ведения журналов.

Сведения о развертывании контейнеров в кластере Service Fabric см. в следующих статьях:

[Развертывание контейнера Windows в Service Fabric на платформе Windows Server 2016](service-fabric-deploy-container.md)

[Развертывание контейнера Docker в Service Fabric на платформе Linux](service-fabric-deploy-container-linux.md)


