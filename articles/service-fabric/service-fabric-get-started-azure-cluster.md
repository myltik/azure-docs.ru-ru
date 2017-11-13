---
title: "Настройка кластера Azure Service Fabric | Документация Майкрософт"
description: "Это краткое руководство поможет вам создать кластер Service Fabric для Windows или Linux в Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: ryanwi
ms.openlocfilehash: caf76bb739fa92982c511c8e3e6c6aaf2bf9d6c1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Создание первого кластера Service Fabric в Azure
[Кластер Azure Service Fabric](service-fabric-deploy-anywhere.md) — это подключенный к сети набор виртуальных машин или физических компьютеров, в котором вы развертываете микрослужбы и управляете ими. Используя это краткое руководство, вы сможете за несколько минут создать кластер с пятью узлами под управлением Windows или Linux на [портале Azure](http://portal.azure.com) или с помощью [Azure PowerShell](https://msdn.microsoft.com/library/dn135248). Для этой задачи можно также использовать Azure CLI.  

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.


## <a name="use-the-azure-portal"></a>Использование портала Azure

Войдите на портал Azure по адресу [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Создание кластера

1. Щелкните **Создать** в левом верхнем углу портала Azure.
2. Найдите **Service Fabric** и выберите **Кластер Service Fabric** из возвращенных результатов. Щелкните **Создать**.
3. Заполните форму Service Fabric **Основы**. Выберите версию **операционной системы** Windows или Linux для запуска узлов кластера. Имя пользователя и пароль понадобятся для входа на виртуальную машину. Создайте **группу ресурсов**. Группа ресурсов — это логический контейнер, в котором происходит создание ресурсов Azure и коллективное управление ими. Когда все будет готово, нажмите кнопку **ОК**.

    ![Снимок экрана с результатами настройки кластера][cluster-setup-basics]

4. Заполните форму **Конфигурация кластера**. Для параметра **Число типов узлов** введите значение **1**.

5. Выберите **Node type 1 (Primary)** (Тип узла 1 (первичный)) и заполните форму **Конфигурация типа узла**. Укажите имя типа узла и задайте для параметра [Уровень устойчивости](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) значение **Бронзовый**. Затем выберите размер виртуальной машины.

    Типы узлов определяют размер виртуальной машины, их число, пользовательские конечные точки и другие параметры для виртуальных машин этого типа. Каждый определенный тип узла настроен как отдельный масштабируемый набор виртуальных машин, используемый для развертывания виртуальных машин и управления ими в наборе. Каждый тип узла поддерживает возможность независимого увеличения или уменьшения масштаба, имеет разные наборы открытых портов и собственные метрики емкости. Первый (или первичный) тип узла определяет размещение системных служб Service Fabric. Он должен содержать не менее пяти виртуальных машин.

    Для любой рабочей развернутой службы важным шагом является [планирование загрузки](service-fabric-cluster-capacity.md). Так как в этом кратком руководстве вы не будете запускать приложения, выберите размер виртуальной машины *DS1_v2 Standard*. Для [уровня надежности](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) задайте значение **Серебряный**, а для масштабируемого набора виртуальных машин задайте начальную емкость 5.  

    Пользовательские конечные точки открывают порты в Azure Load Balancer, поэтому вы сможете подключиться к приложениям, выполняющимся в кластере.  Введите **80, 8172**, чтобы открыть порты 80 и 8172.

    Не устанавливайте флажок **Настройка дополнительных параметров**, используемый для настройки конечных точек управления протоколами TCP и HTTP, диапазонов портов приложения, [ограничений на размещение](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) и [свойств емкости](service-fabric-cluster-resource-manager-metrics.md).    
    
    ![Снимок экрана с формой "Конфигурация типа узла"][node-type-config]

    Нажмите кнопку **ОК**.

6. В форме **Конфигурация кластера** задайте значение **Вкл.** для **диагностики**. При работе с этим руководством не нужно вводить свойства [настроек структуры](service-fabric-cluster-fabric-settings.md).  В разделе **Версия Fabric** выберите режим **автоматического** обновления, чтобы корпорация Майкрософт автоматически обновляла версию кода Fabric, выполняющегося в кластере.  Если вы хотите [выбирать поддерживаемую версию](service-fabric-cluster-upgrade.md), задайте режим **Вручную**.     

    Нажмите кнопку **ОК**.

7. Заполните форму **Безопасность**. Для этого руководства выберите **Незащищенный**. Обратите внимание, что в большинстве случаев следует создавать защищенные кластеры для рабочих нагрузок. К незащищенному кластеру может анонимно подключиться любой пользователь и выполнять операции управления.  

   Service Fabric использует сертификаты, чтобы обеспечить функции аутентификации и шифрования для защиты различных аспектов кластера и его приложений. Дополнительные сведения см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md). Чтобы включить аутентификацию пользователя с помощью Azure Active Directory или настроить сертификаты для безопасности приложения, см. статью [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

    Нажмите кнопку **ОК**.

8. Проверьте сводные данные. Если вам нужно скачать шаблон Azure Resource Manager, созданный на основе указанных вами параметров, выберите **Скачать шаблон и параметры**. Выберите **Создать**, чтобы создать кластер.

    Ход создания кластера будет отображаться в области уведомлений. (Щелкните значок колокольчика рядом со строкой состояния в правом верхнем углу экрана.) Если при создании кластера вы установили флажок **Pin to Startboard** (Закрепить на начальной панели), то на **начальной панели** вы увидите закрепленный элемент **Deploying Service Fabric Cluster** (Развертывание кластера Service Fabric).

### <a name="connect-to-the-cluster-by-using-powershell"></a>Подключение к кластеру с помощью PowerShell
Убедитесь, что кластер работает, подключившись к нему с помощью PowerShell. Модуль Service Fabric PowerShell устанавливается вместе с [пакетом SDK для Service Fabric](service-fabric-get-started.md). Подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps).   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
Примеры других способов подключения к кластеру см. в статье о [безопасном подключении к кластеру](service-fabric-connect-to-secure-cluster.md). Подключившись к кластеру, используйте командлет [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps), чтобы отобразить список узлов в кластере и сведения о состоянии каждого узла. **Состояние работоспособности** каждого узла должно иметь значение *ОК*.

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>Удаление кластера
Кластер Service Fabric состоит из многих других ресурсов Azure помимо собственно ресурса кластера. Чтобы полностью удалить кластер Service Fabric, нужно также удалить и все ресурсы, из которых он состоит. Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов. Чтобы узнать о других способах удаления кластера и некоторых (не всех) ресурсов в группе ресурсов, см. статью об [удалении кластера](service-fabric-cluster-delete.md).

Удаление группы ресурсов на портале Azure
1. Перейдите к кластеру Service Fabric, который нужно удалить.
2. На странице основных компонентов кластера выберите имя **группы ресурсов**.
3. На странице **основных компонентов группы ресурсов** щелкните **Удалить группу ресурсов**. Затем выполните инструкции на этой странице, чтобы завершить удаление группы ресурсов.
    ![Снимок экрана со страницей основных компонентов группы ресурсов и выделенной кнопкой "Удалить группу ресурсов"][cluster-delete]


## <a name="use-azure-powershell"></a>Использование Azure PowerShell
Для создания кластера можно также использовать PowerShell. Этот процесс описывается далее.

1. Скачайте [модуль Azure PowerShell 4.0 или более поздней версии](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) на компьютер.

2. Выполните командлет [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster), чтобы создать кластер Service Fabric с пятью узлами, защищенный с помощью сертификата X.509. Команда создает самозаверяющий сертификат и отправляет его в новое хранилище ключей. Сертификат также копируется в локальный каталог. Укажите параметр *-OS* для выбора версии Windows или Linux, которая запущена на узлах кластера. Измените параметры, если это необходимо. 

    ```powershell
    #Provide the subscription Id
    $subscriptionId = 'yourSubscriptionId'

    # Certificate variables.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $certfolder="c:\mycertificates\"

    # Variables for VM admin.
    $adminuser="vmadmin"
    $adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

    # Variables for common values
    $clusterloc="SouthCentralUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=5 

    # Set the context to the subscription ID where the cluster will be created
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Create the Service Fabric cluster.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
    ```

    Выполнение этой команды может занять от 10 до 30 минут. В выходных данных содержатся сведения о сертификате, хранилище ключей, в которое он отправлен, и локальной папке, в которую он скопирован.     

3. Скопируйте все выходные данные и сохраните их в текстовый файл, так как позднее вы будете ссылаться на него. Запишите следующие сведения из выходных данных: 

    - CertificateSavedLocalPath
    - CertificateThumbprint
    - ManagementEndpoint
    - ClientConnectionEndpointPort

### <a name="install-the-certificate-on-your-local-machine"></a>Установка сертификата на локальном компьютере
  
Чтобы подключиться к кластеру, установите сертификат в личное хранилище (Мое хранилище) текущего пользователя. 

Выполните следующую команду:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\<certificatename>.pfx `
        -Password $certpwd
```

Теперь все готово для подключения к защищенному кластеру.

### <a name="connect-to-a-secure-cluster"></a>Безопасное подключение к кластеру 

Для подключения к защищенному кластеру выполните командлет [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Сведения о сертификате должны совпадать со сведениями сертификата, с помощью которого настраивался кластер. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <ManagementEndpoint>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <CertificateThumbprint> `
          -FindType FindByThumbprint -FindValue <CertificateThumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

В следующем примере показаны образцы параметров: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Выполните следующую команду, чтобы проверить подключение к кластеру и убедиться, что кластер работает:

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>Удаление кластера
Помимо собственных ресурсов кластер содержит другие ресурсы Azure. Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов. 

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```
## <a name="use-azure-cli"></a>Использование интерфейса командной строки Azure
Для создания кластера можно также использовать интерфейс командной строки (CLI). Этот процесс описывается далее.

1. Установите [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) на компьютере.
2. Войдите в Azure и выберите подписку, в которой нужно создать кластер.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Выполните команду [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create), чтобы создать кластер Service Fabric с пятью узлами, защищенный с помощью сертификата X.509. Команда создает самозаверяющий сертификат и отправляет его в новое хранилище ключей. Сертификат также копируется в локальный каталог. Укажите параметр *-OS* для выбора версии Windows или Linux, которая запущена на узлах кластера. Измените параметры, если это необходимо.

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster. The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Подключение к кластеру
Выполните следующую команду CLI, чтобы подключиться к кластеру, используя сертификат.  Если вы используете сертификат клиента для аутентификации, убедитесь, что сведения о сертификате соответствуют сертификату, развернутому в узлах кластера. Используйте параметр `--no-verify` для самозаверяющего сертификата.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Выполните следующую команду, чтобы проверить подключение к кластеру и убедиться, что кластер работает:

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>Подключение к узлам напрямую 

Для подключения к узлам в кластере Linux можно использовать команду Secure Shell (SSH). Укажите номера портов начиная с 3389. Например, для созданного ранее кластера с пятью узлами команды будут выглядеть следующим образом:
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
```

### <a name="remove-the-cluster"></a>Удаление кластера
Помимо собственных ресурсов кластер содержит другие ресурсы Azure. Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов. 

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы настроили кластер разработки, перейдите к следующим шагам.
* [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)
* [Удаление кластера](service-fabric-cluster-delete.md) 
* [Разверните приложения с помощью PowerShell](service-fabric-deploy-remove-applications.md)
* [Развертывание приложений с помощью CLI](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
