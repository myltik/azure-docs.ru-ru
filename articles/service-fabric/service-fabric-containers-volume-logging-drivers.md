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
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 955f84e5656bbf568234cbaf69faa4dd0a741206
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>Использование подключаемых модулей томов и драйверов ведения журналов в контейнере
Service Fabric поддерживает указание [подключаемых модулей томов Docker](https://docs.docker.com/engine/extend/plugins_volume/) и [драйверов ведения журналов Docker](https://docs.docker.com/engine/admin/logging/overview/) для службы контейнеров.  Это позволяет хранить данные в службе [Файлы Azure](https://azure.microsoft.com/en-us/services/storage/files/) даже в том случае, если ваш контейнер перемещен на другой узел или перезапущен на нем.

Пока существуют только драйверы томов для контейнеров Linux.  Если вы работаете с контейнерами Windows, вы можете подключить том к [общему ресурсу SMB3](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) в службе "Файлы Azure" без драйвера тома, используя Windows Server версии 1709. Для этого потребуется обновить виртуальные машины в кластере до Windows Server версии 1709.


## <a name="install-volumelogging-driver"></a>Установка драйвера тома или ведения журнала

Если драйвер Docker для тома или ведения журналов не установлен на виртуальной машине, его можно установить вручную, подключившись к ВМ по протоколу RDP или SSH, либо с помощью сценария [создания масштабируемого набора виртуальных машин](https://azure.microsoft.com/en-us/resources/templates/201-vmss-custom-script-windows/) или [SetupEntryPoint](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-model#describe-a-service). Выбрав один из предложенных способов, можно написать сценарий установки [драйвера тома Docker для Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/).


```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>Укажите в манифесте имя подключаемого модуля или драйвера
Подключаемые модули указываются в манифесте приложения, как показано в следующем примере:

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
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
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

В приведенном выше примере тег `Source` для `Volume` указывает на исходную папку. Исходной папкой может быть папка в виртуальной машине, в которой размещаются контейнеры, или постоянное удаленное хранилище. Тег `Destination` — это расположение, с которым сопоставляется `Source` в работающем контейнере.  Таким образом конечной папкой не может быть существующее расположение в контейнере.

При указании подключаемого модуля тома Service Fabric автоматически создает том, используя заданные параметры. Тег `Source` — это имя тома, а тег `Driver` указывает подключаемый модуль драйвера тома. Параметры можно указать с помощью тега `DriverOption`, как показано в следующем фрагменте:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Если задан драйвер ведения журналов Docker, необходимо развернуть агенты (или контейнеры) для обработки журналов в кластере.  Тег `DriverOption` также может использоваться для указания параметров драйвера ведения журналов.

Сведения о развертывании контейнеров в кластере Service Fabric см. в следующих статьях:


[Развертывание контейнера в Service Fabric](service-fabric-deploy-container.md)

