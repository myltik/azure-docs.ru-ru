---
title: "Обновление автономного кластера Service Fabric в Windows Server | Документация Майкрософт"
description: "Обновление кода и (или) конфигурации Service Fabric, под управлением которой работает автономный кластер Service Fabric, включая изменение режима обновления кластера"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 3d7e28c1cd221d704cf9cfec66da535e079fb472
ms.openlocfilehash: 30044abc0d7d42b11ddd210dfb9ea3eadb94dda6
ms.lasthandoff: 02/27/2017


---
# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Обновление автономного кластера Service Fabric в Windows Server
> [!div class="op_single_selector"]
> * [Кластер Azure](service-fabric-cluster-upgrade.md)
> * [Автономный кластер](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Для любой современной системы разработка с учетом возможности модернизации является неотъемлемой составляющей успеха продукта. Кластер Service Fabric — это ресурс, владельцем которого являетесь вы. В этой статье описывается, как настроить в кластере выполнение только поддерживаемых версий кода и конфигурации Service Fabriс.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Управление версиями Service Fabric в кластере
Вы можете настроить для кластера скачивание обновлений Service Fabric по мере выпуска корпорацией Майкрософт новых версий продукта. Или же можно выбрать нужную версию в списке поддерживаемых. 

Это можно сделать, присвоив конфигурации кластера fabricClusterAutoupgradeEnabled значение true или false.

> [!NOTE]
> Обязательно следите за тем, чтобы кластер всегда работал под управлением поддерживаемой версии Service Fabric. Когда мы объявляем о выпуске новой версии Service Fabric, для предыдущей версии определяется срок завершения жизненного цикла. Этот срок составляет по меньшей мере 60 дней. О доступности новых выпусков сообщается в [блоге группы разработчиков Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/), после чего вы можете использовать их. 
> 
> 

Кластер можно обновить до новой версии, только если используется конфигурация узла с настройками рабочей среды, где каждый узел Service Fabric выделяется для отдельного физического или виртуального устройства. Если несколько узлов Service Fabric выполняется на отдельном физическом или виртуальном устройстве в кластере разработки, вам нужно удалить такой кластер и создать его заново с использованием новой версии.

Обновить кластер до последней или поддерживаемой версии Service Fabric можно с помощью двух рабочих процессов. Один используется для кластеров с возможностью подключения для автоматического скачивания последней версии, а второй — для кластеров без возможности подключения для скачивания последней версии Service Fabric.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Обновление кластеров с возможностью подключения для скачивания последней версии кода и конфигурации
Выполните следующие действия, чтобы обновить до поддерживаемой версии кластер с возможностью подключения к Интернету на сайте [http://download.microsoft.com](http://download.microsoft.com). 

Для кластеров с возможностью доступна на сайт [http://download.microsoft.com](http://download.microsoft.com) мы рекомендуем периодически проверять наличие новых версий Service Fabric.

Когда выходит новая версия Service Fabric, пакет скачивается в кластер и начинается подготовка к обновлению. Наряду с информированием клиентов о доступности новой версии система выдает явное предупреждение о состоянии работоспособности кластера следующего содержания:

"Поддержка текущей версии кластера [номер версии] заканчивается [дата]". После запуска последней версии кластера это предупреждение исчезнет.

#### <a name="cluster-upgrade-workflow"></a>Рабочий процесс обновления кластера
Как только вы увидите предупреждение о работоспособности кластера, сделайте следующее.

1. Подключитесь к кластеру с любой виртуальной машины, на которой есть доступ администратора ко всем ВМ, перечисленным в качестве узлов в файле конфигурации кластера. Виртуальная машина, на которой выполняется этот скрипт, может и не входить в кластер.
   
    ```powershell
   
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
2. Получив список версий Service Fabric, можно выполнить обновление до версии
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```
   
    Должен отобразиться примерной такой результат:
   
    ![Получение версий Service Fabric][getfabversions]
3. Запустите обновление кластера до одной из доступных версий с помощью командлета PowerShell [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   За ходом обновления можно следить в Service Fabric Explorer. Также можно выполнить следующую команду PowerShell:
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Если требования политик работоспособности кластера не реализуются, выполняется откат обновления. Пользовательские политики работоспособности можно настроить при выполнении командлета Start-ServiceFabricClusterUpgrade. О том, как это сделать, см. [здесь](https://msdn.microsoft.com/library/mt125872.aspx). 

Устранив проблемы, которые привели к откату, запустите обновление снова, выполнив те же действия.

### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Обновление кластеров <U>без возможности подключения</u> для скачивания последней версии кода и конфигурации
Выполните следующие действия, чтобы обновить до поддерживаемой версии кластер **без возможности** подключения к Интернету на сайте [http://download.microsoft.com](http://download.microsoft.com). 

> [!NOTE]
> Если вы используете кластер без возможности подключения к Интернету, вам нужно отслеживать блог группы Service Fabric, чтобы узнавать о новых выпусках. Система **не будет** создавать сообщения о состоянии работоспособности кластера.  
> 
> 

1. Измените конфигурацию кластера, присвоив следующим свойствам значение false.
   
        "fabricClusterAutoupgradeEnabled": false,

Затем запустите обновление конфигурации. См. дополнительные сведения об использовании командлета [Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Обязательно обновите clusterConfigurationVersion в JSON, прежде чем запускать обновление конфигурации.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```

#### <a name="cluster-upgrade-workflow"></a>Рабочий процесс обновления кластера
1. Скачайте последнюю версию пакета отсюда: [Создание кластера под управлением Windows Server и управление им](service-fabric-cluster-creation-for-windows-server.md).
2. Подключитесь к кластеру с любой виртуальной машины, на которой есть доступ администратора ко всем ВМ, перечисленным в качестве узлов в файле конфигурации кластера. Виртуальная машина, на которой выполняется этот скрипт, может и не входить в кластер. 
   
    ```powershell
   
    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
3. Скопируйте скачанный пакет в хранилище образов кластера.
   
    ```powershell
   
   ###### Get the list of available service fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"
   
   ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

4. Зарегистрируйте скопированный пакет. 
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 
   
    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
   
     ```
5. Запустите обновление кластера до одной из доступных версий. 
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   За ходом обновления можно следить в Service Fabric Explorer. Также можно выполнить следующую команду PowerShell:
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Если требования политик работоспособности кластера не реализуются, выполняется откат обновления. Пользовательские политики работоспособности можно настроить при выполнении командлета Start-ServiceFabricClusterUpgrade. О том, как это сделать, см. [здесь](https://msdn.microsoft.com/library/mt125872.aspx). 

Устранив проблемы, которые привели к откату, запустите обновление снова, выполнив те же действия.


## <a name="cluster-configuration-upgrade"></a>Обновление конфигурации кластера
Для обновления конфигурации кластера выполните командлет ServiceFabricClusterConfigurationUpgrade. Обновление конфигурации обрабатывает домен обновления.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```


## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как настроить некоторые [параметры Service Fabric для кластера](service-fabric-cluster-fabric-settings.md)
* Ознакомьтесь с концепцией [масштабирования кластера](service-fabric-cluster-scale-up-down.md)
* Узнайте об [обновлениях приложений](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

