---
title: Создание изолированного кластера Azure Service Fabric | Документация Майкрософт
description: Создание кластера Azure Service Fabric на любом компьютере (физическом сервере или виртуальной машине) под управлением Windows Server, расположенном в локальной системе или любом облаке.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: dekapur
ms.openlocfilehash: efa48aa90806b45c99237404af24cb8aba762d15
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209160"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Создание изолированного кластера под управлением Windows Server
Azure Service Fabric позволяет создавать кластеры Service Fabric на любых виртуальных машинах или компьютерах под управлением Windows Server. Это означает, что вы сможете разворачивать и запускать приложения Service Fabric в любой среде с набором подключенных друг к другу компьютеров с Windows Server как в локальной среде, так и у любого поставщика облачных служб. Service Fabric предоставляет установочный пакет для создания кластеров Service Fabric, который называется изолированным пакетом Windows Server.

В этой статье описывается процесс создания изолированного кластера Service Fabric.

> [!NOTE]
> Этот изолированный пакет Windows Server сейчас доступен на коммерческой основе и может использоваться для рабочих развертываний. Этот пакет может содержать новые функции Service Fabric, которые находятся на этапе предварительной версии. Прокрутите список вниз до пункта [Возможности на этапе предварительной версии в этом пакете](#previewfeatures_anchor). в разделе "Возможности на этапе предварительной версии в этом пакете". Вы можете [загрузить копию лицензионного соглашения](http://go.microsoft.com/fwlink/?LinkID=733084).
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Получение поддержки для Service Fabric для пакета Windows Server
* Спросите у сообщества об изолированном пакете Service Fabric для Windows Server на [форуме Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Откройте запрос на [техническую поддержку для Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Узнайте больше о [профессиональной технической поддержке Майкрософт](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Поддержка для этого пакета также доступна в рамках [поддержки Microsoft Premier](https://support.microsoft.com/en-us/premier).
* Дополнительные сведения см. в разделе [Варианты поддержки Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Чтобы собирать журналы в целях поддержки, запустите [изолированный сборщик журналируемых данных Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Скачивание Service Fabric для пакета Windows Server
Чтобы создать кластер, используйте Service Fabric для пакета Windows Server (2012 R2 и более поздней версии), доступный здесь: <br>
[Ссылка для скачивания изолированного пакета Service Fabric для Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Подробные сведения о содержимом пакета можно получить [здесь](service-fabric-cluster-standalone-package-contents.md).

Пакет среды выполнения Service Fabric автоматически скачивается во время создания кластера. В случае развертывания с компьютера, не подключенного к Интернету, скачайте внешний пакет среды выполнения отсюда: <br>
[Ссылка для скачивания среды выполнения Service Fabric для Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Примеры конфигурации изолированного кластера доступны здесь: <br>
[Примеры конфигурации изолированного кластера](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Создание кластера
Service Fabric можно развернуть на кластере с одним компьютером разработки с помощью файла *ClusterConfig.Unsecure.DevCluster.json* из [примеров](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Распакуйте изолированный пакет на свой компьютер, скопируйте пример файла конфигурации на локальный компьютер, а затем в папке изолированного пакета запустите сценарий *CreateServiceFabricCluster.ps1* в сеансе PowerShell с правами администратора.
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Шаг 1А. Создание незащищенного локального кластера разработки
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Дополнительные сведения об устранении неполадок указаны в подразделе "Настройка среды" статьи [Планирование и подготовка развертывания изолированного кластера Service Fabric](service-fabric-cluster-standalone-deployment-preparation.md).

После завершения выполнения сценариев разработки кластер Service Fabric можно будет удалить с компьютера с помощью действий, описанных в разделе [Удаление кластера](#removecluster_anchor). 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Шаг 1Б. Создание кластера с несколькими компьютерами
Выполнив этапы планирования и подготовки, как описано в разделе, доступном по ссылке ниже, вы будете готовы создать рабочий кластер с помощью своего файла конфигурации кластера. <br>
[Планирование и подготовка развертывания кластера](service-fabric-cluster-standalone-deployment-preparation.md)

1. Проверьте созданный файл конфигурации, запустив сценарий *TestConfiguration.ps1* в папке изолированного пакета.  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Вы должны увидеть результат следующего вида. Если для нижнего поля "Passed" (Выполнено) возвращено значение True, значит, проверка работоспособности пройдена и кластер готов к развертыванию на основе входной конфигурации.

    ```
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Создайте кластер: запустите сценарий *CreateServiceFabricCluster.ps1* для развертывания кластера Service Fabric на каждом компьютере в конфигурации. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Трассировки развертывания записываются на компьютер или виртуальную машину, на которой запущен сценарий PowerShell CreateServiceFabricCluster.ps1. Их можно найти во вложенной папке DeploymentTraces в каталоге, в котором был запущен сценарий. Чтобы узнать, правильно ли развернут кластер Service Fabric на компьютере, найдите установленные файлы в каталоге FabricDataRoot, как описано в разделе FabricSettings файла конфигурации кластера (по умолчанию он находится в C:\ProgramData\SF). Кроме того, процессы FabricHost.exe и Fabric.exe должны отображаться в диспетчере задач.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Шаг 1в. Создание кластера в автономном состоянии (не подключенного к Интернету)
Пакет среды выполнения Service Fabric автоматически скачивается во время создания кластера. При развертывании кластера на компьютерах, не подключенных к Интернету, необходимо скачать пакет среды выполнения Service Fabric отдельно и указать путь к нему во время создания кластера.
Пакет среды выполнения можно скачать отдельно, с другого компьютера, подключенного к Интернету, по [ссылке для скачивания среды выполнения Service Fabric для Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Скопируйте пакет среды выполнения туда, откуда вы развертываете не подключенный к Интернету кластер, и создайте кластер, выполнив `CreateServiceFabricCluster.ps1` с включенным параметром `-FabricRuntimePackagePath`, как показано ниже: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
`.\ClusterConfig.json` и `.\MicrosoftAzureServiceFabric.cab` — это пути к файлу конфигурации кластера и CAB-файлу среды выполнения соответственно.


### <a name="step-2-connect-to-the-cluster"></a>Шаг 2. Подключение к кластеру
Сведения о подключении к защищенному кластеру см. в статье [Подключение Service Fabric к защищенному кластеру](service-fabric-connect-to-secure-cluster.md).

Для подключения к незащищенному кластеру выполните следующую команду PowerShell.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
Пример:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>Шаг 3. Вызов Service Fabric Explorer
Теперь к кластеру можно подключиться при помощи Service Fabric Explorer непосредственно с одного из компьютеров по адресу http://localhost:19080/Explorer/index.html или удаленно по адресу http://<*IP-адрес_компьютера*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Добавление и удаление узлов
Вы можете добавить узлы в изолированный кластер Service Fabric или удалить их из него в соответствии с изменениями потребностей компании. Подробные инструкции статье [Добавление узлов в автономный кластер Service Fabric или удаление узлов из него](service-fabric-cluster-windows-server-add-remove-nodes.md) .

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Удаление кластера
Чтобы удалить кластер, запустите сценарий Powershell *RemoveServiceFabricCluster.ps1* из папки пакета и передайте в него путь к файлу конфигурации JSON. При необходимости можно указать расположение журнала удаления.

Этот сценарий может выполняться на любом компьютере, у которого есть доступ администратора ко всем компьютерам, перечисленным в качестве узлов в файле конфигурации кластера. Компьютер, на котором выполняется этот сценарий, может и не входить в кластер.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Собираемые данные телеметрии и отказ от них
По умолчанию продукт собирает данные телеметрии по использованию Service Fabric для последующего усовершенствования продукта. Анализатор соответствия рекомендациям, который запускается в рамках установки, проверяет возможность подключения к [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Если это расположение недоступно, установку выполнить не удастся, если только вы не отказались от сбора данных телеметрии.

1. Конвейер телеметрии ежедневно выполняет попытку отправить указанные ниже данные по адресу [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Эта отправка является необходимой и не влияет на функциональные возможности кластера. Данные телеметрии отправляется только с узла, на котором первоначально выполняется диспетчер отработки отказа. С других узлов данные телеметрии не отправляются.
2. Данные телеметрии включают:

* количество служб;
* количество типов служб;
* количество приложений;
* количество обновлений приложений;
* количество единиц с отработкой отказа;
* количество единиц со встроенной отработкой отказа;
* количество единиц с неработоспособной отработкой отказа;
* количество реплик;
* количество встроенных реплик;
* количество реплик в режиме ожидания;
* количество автономных реплик;
* длина общей очереди;
* длина очереди запросов;
* длина очереди единиц с отработкой отказа;
* длина очереди фиксаций;
* количество узлов;
* выполнен ли контекст: значение True или False;
* идентификатор кластера — это глобальный уникальный идентификатор, который случайным образом формируется для каждого кластера;
* версия Service Fabric;
* IP-адрес виртуальной машины или компьютера, с которых отправляются данные телеметрии.

Чтобы отключить сбор данных телеметрии, добавьте следующий фрагмент кода к элементу *properties* в конфигурации кластера: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Возможности на этапе предварительной версии в этом пакете
Отсутствует.


> [!NOTE]
> Начиная с новой [Общедоступной версии автономного кластера для Windows Server (версия 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), обновление кластера до будущих версий осуществляется вручную или автоматически. Дополнительные сведения см. в документе [Upgrade a standalone Service Fabric cluster version](service-fabric-cluster-upgrade-windows-server.md) (Обновление версии изолированного кластера Service Fabric).
> 
> 

## <a name="next-steps"></a>Дополнительная информация
* [Развертывание и удаление приложений с помощью PowerShell](service-fabric-deploy-remove-applications.md)
* [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md)
* [Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Upgrade a standalone Service Fabric cluster version](service-fabric-cluster-upgrade-windows-server.md) (Обновление версии изолированного кластера Service Fabric)
* [Create a three node standalone Service Fabric cluster with Azure virtual machines running Windows Server (Создание автономного кластера Service Fabric с тремя узлами на виртуальных машинах Azure под управлением Windows)](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Защита автономного кластера под управлением Windows с помощью системы безопасности Windows](service-fabric-windows-cluster-windows-security.md)
* [Защита автономного кластера под управлением Windows с помощью сертификатов](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
