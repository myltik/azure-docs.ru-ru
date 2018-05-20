---
title: Драйвер тома службы файлов Azure для Service Fabric (предварительная версия) | Документация Майкрософт
description: Service Fabric поддерживает использование службы файлов Azure для резервного копирования томов в контейнере. Сейчас этот компонент доступен в предварительной версии.
services: service-fabric
documentationcenter: other
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/30/2018
ms.author: subramar
ms.openlocfilehash: d4751182cac9b5b952ef9a9dd125408267c1f8d0
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Драйвер тома службы файлов Azure для Service Fabric (предварительная версия)
Подключаемый модуль тома службы файлов Azure — это [подключаемый модуль тома Docker](https://docs.docker.com/engine/extend/plugins_volume/), предоставляющий тома [службы файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) для контейнеров Docker. Этот подключаемый модуль тома Docker упакован в виде приложения Service Fabric, которое можно развернуть в кластерах Service Fabric. Он позволяет предоставить тома службы файлов Azure для других контейнерных приложений Service Fabric, развернутых в кластере.

> [!NOTE]
> Версия 6.255.389.9494 подключаемого модуля тома службы файлов Azure является предварительным выпуском, который доступен в этом документе. Как и любой предварительный выпуск, эта версия **не** предназначена для рабочих сред.
>

## <a name="prerequisites"></a>предварительным требованиям
* Версия подключаемого модуля тома службы файлов Azure для Windows работает только в операционных системах [Windows Server версии 1709](https://docs.microsoft.com/en-us/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 версии 1709](https://docs.microsoft.com/en-us/windows/whats-new/whats-new-windows-10-version-1709) или более поздних версий. Версия подключаемого модуля тома службы файлов Azure для Linux работает во всех версиях операционной системы, поддерживаемых Service Fabric.

* Следуйте инструкциям в [документации по службе файлов Azure](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-create-file-share), чтобы создать файловый ресурс для контейнерного приложения Service Fabric, который будет использован в качестве тома.

* Вам потребуется установить [PowerShell с модулем Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started) или [SFCTL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli).

## <a name="deploy-the-service-fabric-azure-files-application"></a>Развертывание приложения Service Fabric для службы файлов Azure

Приложение Service Fabric, которое предоставляет тома для контейнеров, можно скачать с помощью этой [ссылки](https://aka.ms/sfvolume). Это приложение можно развернуть в кластере с помощью [PowerShell](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications), [интерфейса командной строки](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-lifecycle-sfctl) или [интерфейсов API FabricClient](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. С помощью командной строки перейдите в корневой каталог скачанного пакета приложения. 

    ```powershell 
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Скопируйте пакет приложения в хранилище образов. Выполните команду, приведенную ниже, указав соответствующие значения [ApplicationPackagePath] и [ImageStoreConnectionString].

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. регистрация типа приложения;

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Создайте приложение, выполнив приведенную ниже команду, и запишите значение параметра **ListenPort**. Значение данного параметра приложения — это порт, через который подключаемый модуль тома службы файлов Azure ожидает передачи запросов из управляющей программы Docker. Очень важно убедиться, что порт, предоставленный приложению, не конфликтует с другим портом, используемым кластером или приложениями.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter не поддерживает сопоставление подключений SMB с контейнерами ([поддерживается только в Windows Server версии 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Это ограничение предотвращает сопоставление сетевых томов и использование драйверов томов службы файлов Azure в версиях, предшествующих версии 1709. 
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Развертывание приложения в локальном кластере разработки
Число экземпляров службы по умолчанию для приложения подключаемого модуля тома службы файлов Azure равно –1. Это означает, что экземпляр службы развернут на каждом узле в кластере. Тем не менее при развертывании приложения подключаемого модуля тома службы файлов Azure в локальном кластере разработки нужно указать число экземпляров, равное 1. Это можно сделать с помощью параметра приложения **InstanceCount**. В этом случае команда для развертывания приложения подключаемого модуля тома службы файлов Azure в локальном кластере разработки будет следующей.

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.255.389.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.255.389.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Настройка приложений для использования тома
В следующем фрагменте кода показано, как том службы файлов Azure можно указать в манифесте приложения. Отдельного внимания заслуживает тег **Volume**.

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
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
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

Имя драйвера для подключаемого модуля тома службы файлов Azure — **sfazurefile**. Это значение устанавливается для атрибута **Driver** элемента **Volume** в манифесте приложения.

В элементе **Volume** в приведенном выше фрагменте кода для подключаемого модуля тома службы файлов Azure требуются следующие теги. 
- **Source**. Указывает исходную папку, которой может быть папка на виртуальной машине, в которой размещаются контейнеры, или постоянное удаленное хранилище.
- **Destination**. Этот тег указывает расположение, к которому подключается исходная папка (**Source**) в работающем контейнере. Таким образом, конечной папкой не может быть существующее в контейнере расположение.

Как показано в элементах **DriverOption** в приведенном выше фрагменте кода, подключаемый модуль тома службы файлов Azure поддерживает следующие параметры драйвера.

- **shareName**. Имя файлового ресурса службы файлов Azure, предоставляющего том для контейнера.
- **storageAccountName**. Имя учетной записи хранения Azure, которая содержит файловый ресурс службы файлов Azure.
- **storageAccountKey**. Ключ доступа учетной записи хранения Azure, которая содержит файловый ресурс службы файлов Azure.

Все перечисленные выше параметры драйвера являются обязательными. 

## <a name="using-your-own-volume-or-logging-driver"></a>Использование драйвера собственного тома или ведения журнала
Service Fabric также дает возможность использовать драйверы собственных пользовательских томов или ведения журнала. Если в кластере не установлен драйвер тома или ведения журнала Docker, его можно установить вручную с помощью протоколов RDP и SSH. Используя эти протоколы, можно выполнить установку с помощью [скрипта запуска масштабируемого набора виртуальных машин](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) или [скрипта SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Пример скрипта установки [драйвера тома Docker для Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/):

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Чтобы использовать установленный драйвер тома или ведения журнала в своих приложениях, потребуется указать соответствующие значения в элементах **Volume** и **LogConfig** в разделе  **ContainerHostPolicies** в манифесте приложения. 

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

## <a name="next-steps"></a>Дополнительная информация
* Примеры контейнеров, включая драйвер тома, приведены в разделе [Примеры контейнеров Service Fabric](https://github.com/Azure-Samples/service-fabric-containers).
* Развертывание контейнеров в кластере Service Fabric описывается в разделе [Развертывание контейнера в Service Fabric](service-fabric-deploy-container.md).


