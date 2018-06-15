---
title: Azure Site Recovery. Настройка и тестирование аварийного восстановления для виртуальных машин Azure с помощью Azure PowerShell | Документация Майкрософт
description: Сведения о настройке аварийного восстановления в Azure Site Recovery для виртуальных машин Azure с помощью Azure PowerShell.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 05/31/2018
ms.author: bsiva
ms.openlocfilehash: 3fa9ee27a1b9717d8011b7b46a1116f1f1ac1df5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716335"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Настройка аварийного восстановления для виртуальных машин Azure с помощью Azure PowerShell

В этой статье описано, как настроить и проверить аварийное восстановление для виртуальных машин Azure с помощью Azure PowerShell. 

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> - Создайте хранилище служб восстановления,
> - настройка контекста хранилища для сеанса PowerShell;
> - подготовка хранилища к началу репликации виртуальных машин Azure;
> - создание сетевых сопоставлений;
> - Создание целевых учетных записей хранения для репликации виртуальных машин.
> - репликация виртуальных машин Azure в регион восстановления для аварийного восстановления;
> - Выполнение тестовой отработки отказа, проверка и очистка после тестовой отработки отказа.
> - отработка отказа в регион восстановления.

> [!NOTE]
> Azure PowerShell пока поддерживает не все возможности этого сценария, доступные через портал. Ниже перечислены некоторые возможности, которые пока недоступны через Azure PowerShell:
> - возможность репликации виртуальных машин Azure, которые используют управляемые диски;
> - возможность сразу выбрать для репликации все диски виртуальной машины, чтобы не указывать их отдельно по одному.  

## <a name="prerequisites"></a>предварительным требованиям

Перед началом работы:
- Вам должны быть понятны [архитектура и компоненты сценария](azure-to-azure-architecture.md).
- [Ознакомьтесь](azure-to-azure-support-matrix.md) с требованиями поддержки для всех компонентов.
- Требуется модуль AzureRm PowerShell 5.7.0 или более поздней версии. Если вам необходимо установить или обновить Azure PowerShell, ознакомьтесь с этим [руководством по установке и настройке Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Вход в подписку Microsoft Azure

Войдите в подписку Azure, используя командлет Connect-AzureRmAccount.

```azurepowershell
Connect-AzureRmAccount
```
Выберите подписку Azure. Используйте командлет Get-AzureRmSubscription, чтобы получить список подписок Azure, к которым у вас есть доступ. Выберите необходимую подписку Azure, выполнив командлет Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Получение сведений о реплицируемой виртуальной машине

В примере для этой статьи виртуальная машина из региона "Восточная часть США" будет реплицирована в регион "Западная часть США 2" и затем восстановлена в нем же. Для репликации используется виртуальная машина с одним диском ОС и одним диском данных. Виртуальная машина в этом примере имеет имя AzureDemoVM.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Получите сведения о дисках виртуальной машины. Сведения о дисках потребуются позже для запуска репликации виртуальной машины.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Создайте группу ресурсов, в которой будет создано хранилище служб восстановления. 

> [!IMPORTANT]
> * Хранилище служб восстановления и защищаемые виртуальные машины должны находиться в разных расположениях Azure.
> * Группа ресурсов для хранилища служб восстановления и защищаемые виртуальные машины должны находиться в разных расположениях Azure.
> * Хранилище служб восстановления и группа ресурсов, к которой оно относится, могут размещаться в одном расположении Azure.
 
В примере для этой статьи защищаемая виртуальная машина находится в регионе "Восточная часть США". Для аварийного восстановления выбран регион "Западная часть США 2". Хранилище служб восстановления и его группа ресурсов размещаются в регионе восстановления (Западная часть США 2).

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```
   
Создайте хранилище служб восстановления. В следующем примере создается хранилище служб восстановления с именем a2aDemoRecoveryVault в регионе "Западная часть США 2".

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 
## <a name="set-the-vault-context"></a>Задание контекста хранилища

> [!TIP]
> Модуль PowerShell для Azure Site Recovery (модуль AzureRm.RecoveryServices.SiteRecovery) поставляется с удобными псевдонимами для большинства командлетов. Командлеты в модуле имеют вид *\<Операция>-**AzureRmRecoveryServicesAsr**\<объект>* и поддерживают эквивалентные псевдонимы вида *\<Операция>-**ASR**\<Объект>*. В этой статье для более удобного чтения используется псевдонимы командлетов.

Настройте контекст хранилища для сеанса PowerShell. Для этого скачайте файл параметров хранилища и импортируйте его в сеанс PowerShell. 

После этого последующие операции Azure Site Recovery в сеансе PowerShell будут выполняться в контексте выбранного хранилища. 

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResouceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Подготовка хранилища к началу репликации виртуальных машин Azure

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1. Создание объекта структуры Site Recovery, который будет представлять исходный регион (источник)

Объект структуры в хранилище представляет регион Azure. Объект исходной структуры создается для того, чтобы обозначать исходный регион Azure, в котором размещаются защищенные виртуальные машины. В примере для этой статьи защищаемая виртуальная машина находится в регионе "Восточная часть США".

> [!NOTE]
> Операции Azure Site Recovery выполняются асинхронно. При запуске операции отправляется задание Azure Site Recovery и возвращается объект отслеживания задания. Используйте объект отслеживания заданий, чтобы получить актуальное состояние задания (Get-ASRJob) и отслеживать состояние операции.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Если в одном хранилище будут защищаться виртуальные машины из нескольких регионов Azure, создайте отдельный объект структуры для каждого исходного региона Azure.

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2. Создание объекта структуры Site Recovery, который будет представлять регион восстановления

Объект целевой структуры представляет расположение восстановления в Azure. Репликация и восстановление (при отработке отказа) для виртуальных машин будут выполняться в целевом регионе, который представлен структурой восстановления. В нашем примере регионом восстановления (целевым) Azure является регион "Западная часть США 2".

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>3. Создание контейнера защиты Site Recovery в исходной структуре

Контейнер защиты — это контейнер, в котором сгруппированы реплицируемые элементы структуры.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4. Создание контейнера защиты Site Recovery в структуре восстановления

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

####<a name="5-create-a-replication-policy"></a>5. Создание политики репликации

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6. Создание сопоставления между исходным контейнером защиты и целевым контейнером защиты

Сопоставление контейнеров защиты связывает между собой исходный контейнер защиты, целевой контейнер защиты и политику репликации. Создайте отдельное сопоставление для каждой политики репликации, которую вы будете применять для репликации виртуальных машин между каждой парой контейнеров защиты.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7. Создание сопоставления контейнеров защиты для восстановления размещения (обратной репликации после отработки отказа)

После отработки отказа, когда вы будете готовы вернуть виртуальную машину в исходный регион Azure, выполняется восстановление размещения. При восстановлении размещения та виртуальная машина, для которой выполнена отработка отказа, реплицируется обратно из региона отработки отказа в исходный регион. Для обратной репликации исходный и целевой регионы меняются ролями. Исходный регион становится новым регионом восстановления, а бывший регион восстановления становится исходным регионом. Сопоставление контейнеров защиты для обратной репликации отражает эту смену ролей исходного и целевого регионов.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy 
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Создание учетных записей хранения для кэша и целевого хранилища

Учетная запись хранения кэша — это стандартная учетная запись хранения в том же регионе Azure, где расположена реплицируемая виртуальная машина. Учетная запись хранения кэша используется для временного хранения реплицируемых изменений перед их перемещением в регион восстановления Azure. Вы можете (но это не обязательно) указать разные учетные записи хранения кэша для разных дисков виртуальной машины.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Если виртуальная машина не использует управляемые диски, целевая учетная запись хранения размещается в регионе восстановления, в который реплицируются диски этой виртуальной машины. Целевая учетная запись хранения может иметь категорию "Стандартная" или "Премиум". На выбор типа учетной записи хранения влияют такие факторы, как скорость изменения данных (скорости записи) на реплицируемых дисках и допустимая скорость обработки в Azure Site Recovery для используемого типа хранилища.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Создание сетевых сопоставлений

Сетевое сопоставление связывает виртуальные сети исходного региона с виртуальными сетями в целевом регионе. Сетевое сопоставление указывает, в какой виртуальной сети Azure целевого региона будет создаваться виртуальная машина при отработке отказа. Виртуальную сеть Azure можно сопоставить только с одной виртуальной сетью Azure в регионе восстановления.

- Создайте виртуальную сеть Azure в регионе восстановления, которая будет использоваться для отработки отказа:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Получите данные о виртуальной сети из исходного расположения (к которой сейчас подключена виртуальная машина):
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to
    
    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resourec name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Создайте сетевое сопоставление между исходной виртуальной сетью и целевой виртуальной сетью:
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
    
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    
   ```
- Создайте сетевое сопоставление для восстановления размещения (обратное действие):
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
        
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Репликация виртуальной машины Azure

Выполните репликацию виртуальной машины Azure:

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Когда начальная репликация успешно завершится, все данные виртуальной машины будут реплицированы в регион восстановления. 

Процесс репликации начинается с того, что в регионе восстановления создаются копии реплицируемых дисков виртуальной машины. Этот этап называется начальной репликацией. 

После завершения начальной репликации начинается этап разностной синхронизации. На этом этапе виртуальная машина считается защищенной и для нее можно выполнять тестовую отработку отказа. Когда завершится начальная репликация, реплицируемый элемент, представляющий виртуальную машину, примет состояние репликации "Защищено".

Чтобы отследить текущее состояние и работоспособность репликации для виртуальной машины, получите сведения о репликации для соответствующего защищенного элемента.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Выполнение тестовой отработки отказа, проверка и очистка после тестовой отработки отказа

Когда состояние репликации для виртуальной машины достигнет состояния "Защищено", вы можете выполнить для этой виртуальной машины тестовую отработку отказа (используя защищенный элемент, представляющий эту виртуальную машину).

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Выполните тестовую отработка отказа.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Дождитесь завершения этой операции.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
Когда это задание успешно завершится, вы сможете подключиться к созданной виртуальной машине и проверить правильность тестовой отработки отказа.

Когда проверка созданной тестовой виртуальной машины завершится, удалите эту тестовую копию, запустив операцию очистки после тестовой отработки отказа. Эта операция удаляет тестовую копию виртуальной машины, созданную в процессе тестовой отработки отказа.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Отработка отказа в Azure

Выполните отработку отказа виртуальной машины до определенной точки восстановления.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

Проверив успешность операции, вы можете зафиксировать изменения, внесенные при отработке отказа.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

После отработки отказа, когда вы будете готовы вернуть виртуальную машину в исходный регион, запустите обратную репликацию для защищенного элемента с помощью командлета Update-AzureRmRecoveryServicesAsrProtectionDirection.

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со [справочником по PowerShell для Azure Site Recovery](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery), чтобы узнать, как выполнять другие задачи, в том числе создание планов восстановления и тестирование отработки отказа по планам восстановления с помощью PowerShell.
