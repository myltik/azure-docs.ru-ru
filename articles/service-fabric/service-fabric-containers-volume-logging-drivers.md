---
title: "Средство Docker Compose для Azure Service Fabric (предварительная версия) | Документация Майкрософт"
description: "Azure Service Fabric принимает формат Docker Compose, упрощая управление существующими контейнерами с помощью платформы Service Fabric. Средство Docker Compose сейчас доступно в режиме предварительной версии."
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
ms.openlocfilehash: cbe7e338ac7da9dc7e8d03cb1bb07a69af70cb17
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>Использование подключаемых модулей томов и драйверов ведения журналов Docker в контейнере
Azure Service Fabric позволяет указывать [подключаемые модули томов Docker](https://docs.docker.com/engine/extend/plugins_volume/) и [драйверы ведения журналов Docker](https://docs.docker.com/engine/admin/logging/overview/) для службы контейнеров. Так вы можете хранить данные в [службе "Файлы Azure"](https://azure.microsoft.com/services/storage/files/), даже если ваш контейнер перемещен на другой узел или перезапущен на нем.

Сейчас поддерживаются только драйверы тома для контейнеров Linux. Если вы используете контейнеры Windows, можно подключить том к [общему ресурсу SMB3](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) в службе "Файлы Azure" без драйвера тома. Для этого необходимо обновить виртуальные машины в кластере до последней версии Windows Server 1709.


## <a name="install-the-docker-volumelogging-driver"></a>Установка драйвера тома или ведения журналов Docker

Если на компьютере не установлен драйвер тома или ведения журналов Docker, его можно установить вручную с помощью протоколов RDP/SSH. Используя эти протоколы, можно выполнить установку с помощью [скрипта запуска масштабируемого набора виртуальных машин](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) или [скрипта SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Пример скрипта установки [драйвера тома Docker для Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/):

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> Windows Server 2016 Datacenter не поддерживает сопоставление подключений SMB с контейнерами ([поддерживается только в Windows Server версии 1709](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/container-storage)). Это ограничение предотвращает сопоставление сетевых томов и использование драйверов томов службы файлов Azure в версиях, предшествующих версии 1709. 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>Указание имени подключаемого модуля или драйвера в манифесте
Подключаемые модули указываются в манифесте приложения следующим образом:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
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
        <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
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

Тег **Source** для элемента **Volume** указывает на исходную папку. Исходной папкой может быть папка в виртуальной машине, в которой размещаются контейнеры, или постоянное удаленное хранилище. Тег **Destination** указывает на расположение, к которому подключается исходная папка (**Source**) в работающем контейнере. Таким образом, конечной папкой не может быть существующее в контейнере расположение.

Параметры приложения поддерживаются для томов, как показано в предыдущем фрагменте кода манифеста (пример использования можно найти по тексту `MyStoreVar`).

При указании подключаемого модуля тома Service Fabric автоматически создает том, используя заданные параметры. Тег **Source** указывает на имя тома, а тег **Driver** — на подключаемый модуль драйвера тома. Параметры можно указать с помощью тега **DriverOption** следующим образом:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Если задан драйвер ведения журналов Docker, необходимо развернуть агенты (или контейнеры) для обработки журналов в кластере. Чтобы указать параметры драйвера ведения журналов можно использовать тег **DriverOption**.

## <a name="next-steps"></a>Дополнительная информация
См. дополнительные сведения о [развертывании контейнеров в Service Fabric](service-fabric-deploy-container.md).
