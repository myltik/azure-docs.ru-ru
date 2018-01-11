---
title: "Репликации и отработки отказа VMware виртуальных машин в Azure с помощью Azure Site Recovery PowerShell | Документы Microsoft"
description: "Репликации и отработки отказа VMware виртуальных машин в Azure с помощью Azure Site Recovery PowerShell"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 14/12/2017
ms.author: bsiva
ms.openlocfilehash: 3cf2478eb810961604e1218731f5303abd0f611a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="replicate-and-failover-vmware-virtual-machines-to-azure-using-azure-site-recovery-powershell"></a>Репликации и отработки отказа VMware виртуальных машин в Azure с помощью Azure Site Recovery PowerShell

В этой статье вы видите репликации и отработки отказа виртуальных машин VMware в Azure с помощью Azure PowerShell. 

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> - Создайте хранилище служб восстановления,
> - Задайте контекст хранилища.
> - Проверьте сервер конфигурации и масштабировать серверы обработки регистрируются в хранилище.
> - Создайте политику репликации и сопоставьте ее для использования с сервером конфигурации.
> - Добавление сервера vCenter и обнаружение виртуальных машин VMware.
> - Создание учетных записей хранилища для репликации виртуальных машин.
> - Виртуальные машины VMware можно Реплицируйте в учетных записях хранилища Azure.
> - Настройте параметры отработки отказа для репликации виртуальных машин.
> - Выполнить тестовую отработку отказа, проверки и очистки теста отработки отказа.
> - Отработка отказа в Azure.

## <a name="prerequisites"></a>Технические условия

Перед началом работы:
- Вам должны быть понятны [архитектура и компоненты сценария](concepts-vmware-to-azure-architecture.md).
- [Ознакомьтесь](site-recovery-support-matrix-to-azure.md) с требованиями поддержки для всех компонентов.
- Версия 5.0.1 или более модуль AzureRm PowerShell. Чтобы установить или обновить Azure PowerShell, выполните это [руководство по установке и настройке Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Вход в подписку Microsoft Azure

Вход с вашей подпиской Azure, с помощью командлета AzureRmAccount входа

```azurepowershell
Login-AzureRmAccount
```
Выберите подписку Azure, нужно реплицировать виртуальные машины VMware. Используйте командлет Get-AzureRmSubscription получить список подписок Azure, у вас есть доступ к. Выберите подписку Azure для работы с помощью командлет Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

* Создайте группу ресурсов, в которой создается хранилище служб восстановления. В следующем примере имя группы ресурсов с именем VMwareDRtoAzurePS и создан в регионе Восточная Азия.

```azurepowershell
New-AzureRmResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
```
```
ResourceGroupName : VMwareDRtoAzurePS
Location          : eastasia
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
```
   
* Создайте хранилище служб восстановления. В следующем примере хранилище служб восстановления называется VMwareDRToAzurePs и будет создан в регионе Восточная Азия и в группе ресурсов, созданные на предыдущем шаге.

```azurepowershell
New-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
```
```
Name              : VMwareDRToAzurePs
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
Type              : Microsoft.RecoveryServices/vaults
Location          : eastasia
ResourceGroupName : VMwareDRToAzurePs
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 

* Загрузите ключ регистрации в хранилище для хранилища. Ключ регистрации в хранилище используется для регистрации сервера в локальной конфигурации, в этом хранилище. Регистрация является частью процесса установки программного обеспечения сервера конфигурации.

```azurepowershell
#Get the vault object by name and resource group and save it to the $vault PowerShell variable 
$vault = Get-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

#Download vault registration key to the path C:\Work
Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
```
```
FilePath
--------
C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
```

Используйте ключ регистрации в хранилище загруженные и следуйте инструкциям статьи, представленные ниже полной установки и регистрации сервера конфигурации.
* [Выбор целей защиты](site-recovery-set-up-vmware-to-azure.md#choose-your-protection-goals)
* [Настройка исходной среды](site-recovery-set-up-vmware-to-azure.md#set-up-the-source-environment) 

## <a name="set-the-vault-context"></a>Задать контекст хранилища

> [!TIP]
> Модуль PowerShell восстановления сайта Azure (модуль AzureRm.RecoveryServices.SiteRecovery) поставляется с легко использовать псевдонимы для большинства командлетов. Командлеты в модуле представляются в виде  *\<операции >-**AzureRmRecoveryServicesAsr**\<объекта >* и имеют эквивалента псевдонимы, которые представляются в виде  *\<операции >-**ASR**\<объекта >*. В этой статье используется командлет псевдонимы для простоты чтения.

Задайте контекст хранилища с помощью командлета Set-ASRVaultContext. После установки последующих операций Azure Site Recovery в сеансе PowerShell выполняются в контексте выбранного хранилища. В следующем примере сведения о хранилища из $vault переменная используется для указания контекста хранилище для сеанса PowerShell.
 ```azurepowershell
Set-ASRVaultContext -Vault $vault
```
```
ResourceName      ResourceGroupName ResourceNamespace          ResouceType
------------      ----------------- -----------------          -----------
VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
```

Последующих разделах данной статьи предполагается, задан контекст хранилища для операций Azure Site Recovery.

## <a name="validate-that-your-configuration-server-and-scale-out-process-servers-are-registered-to-the-vault"></a>Проверить сервер конфигурации и масштабировать серверы обработки регистрируются в хранилище

 Предположим.
- Сервер конфигурации с именем *ConfigurationServer* был зарегистрирован в этом хранилище
- Дополнительный сервер с именем процесса *горизонтального масштабирования запускаться* был зарегистрирован для *ConfigurationServer*
- Учетные записи с именем *vCenter_account*, *WindowsAccount*, и *LinuxAccount* были настроены на сервере конфигурации. Эти учетные записи используются для добавления сервера vCenter для обнаружения виртуальных машин и установка программного обеспечения службы мобильности на серверах Windows и Linux, которые должны быть реплицированы.

Зарегистрированные серверы конфигурации представлены объектом структуры в Azure Site Recovery. На этом шаге получить список структуры объектов в хранилище и идентификации сервера конфигурации.

```azurepowershell
# Verify that the Configuration server is successfully registered to the vault
$ASRFabrics = Get-ASRFabric
$ASRFabrics.count
```
```
1
```
```azurepowershell
#Print details of the Configuration Server
$ASRFabrics[0]
```
```
Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
FriendlyName          : ConfigurationServer
ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                        /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
FabricType            : VMware
SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
```

* Определите серверы обработки, который может использоваться для репликации машин.

```azurepowershell
$ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
for($i=0; $i -lt $ProcessServers.count; $i++) {
 "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
}
```
```
0     ScaleOut-ProcessServer
1     ConfigurationServer
```

В приведенном выше примере ***$ProcessServers [0]*** соответствует *горизонтального масштабирования запускаться* и ***$ProcessServers [1]*** соответствует роли сервера обработки на *ConfigurationServer*

* Определите учетные записи, которые были настроены на сервере конфигурации.

```azurepowershell
$AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
#Print the account details
$AccountHandles
```
```
AccountId AccountName
--------- -----------
1         vCenter_account
2         WindowsAccount
3         LinuxAccount
```

В приведенном выше примере ***$AccountHandles [0]*** соответствует учетной записи *vCenter_account*, ***$AccountHandles [1]*** учетную запись *WindowsAccount*, и ***$AccountHandles [2]*** учетную запись *LinuxAccount*

## <a name="create-a-replication-policy-and-map-it-for-use-with-the-configuration-server"></a>Создайте политику репликации и сопоставьте ее для использования с сервером конфигурации

На этом шаге создаются две политики репликации. Одна политика для репликации виртуальных машин VMware в Azure, а другая — для репликации отработку отказа виртуальных машин, работающих в Azure обратно в локальный сайт VMware.

> [!NOTE]
> Большинство операций Azure Site Recovery, выполняются асинхронно. При запуске операции отправки задание Azure Site Recovery, а задание отслеживания объекта возвращается. Это задание отслеживания объекта можно использовать для наблюдения за состоянием операции.

* Создайте политику репликации с именем *ReplicationPolicy* для репликации виртуальных машин VMware в Azure с указанными свойствами.

```azurepowershell
$Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

# Track Job status to check for completion
while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
}

#Display job status
$Job_PolicyCreate
```
```
Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                   9-479f-430d-b76b-6bc7eb2d0b3e
Type             :
JobType          : AddProtectionProfile
DisplayName      : Create replication policy
ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:49:24 AM
EndTime          : 11/24/2017 2:49:23 AM
TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
TargetObjectType : ProtectionProfile
TargetObjectName : ReplicationPolicy
AllowedActions   :
Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
Errors           : {}
```

* Создайте политику репликации для восстановления размещения из Azure и локальным сайтом с VMware.

```azurepowershell
$Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
```

Использовать сведения о задании в *$Job_FailbackPolicyCreate* для отслеживания до завершения операции.

* Создайте сопоставление контейнера защиты для сопоставления политики репликации с сервером конфигурации.

```azurepowershell
#Get the protection container corresponding to the Configuration Server
$ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

#Get the replication policies to map by name.
$ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
$FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

# Associate the replication policies to the protection container corresponding to the Configuration Server. 

$Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
}
$Job_AssociatePolicy.State

$Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
}
$Job_AssociateFailbackPolicy.State

```

## <a name="add-a-vcenter-server-and-discover-vmware-virtual-machines"></a>Добавление сервера vCenter и обнаружение виртуальных машин VMware

Добавление сервера vCenter Server IP-адреса или имени узла. **-Порт** параметр задает порт на сервере vCenter соединиться, **-имя** параметр указывает понятное имя, используемое для сервера vCenter и **-учетная запись** параметр задает дескриптор учетной записи на сервере конфигурации, следует использовать для поиска виртуальных машин, управляемых сервером vCenter.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) { 
        sleep 30; 
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts"></a>Создание учетных записей хранения

На этом шаге создаются учетные записи хранения для репликации. Эти учетные записи хранения используются позже выполнять репликацию виртуальных машин. Убедитесь, что учетные записи хранения создаются в том же регионе Azure, в котором находится хранилище. Этот шаг можно пропустить, если вы планируете использовать существующую учетную запись хранения для репликации.

> [!NOTE]
> При репликации виртуальных машин в локальной учетной записи хранения premium, необходимо указать дополнительная Стандартная учетная запись хранения (учетная запись хранения журнала). Учетная запись хранения журнала содержит журналы репликации как промежуточного хранилища, пока не журналы можно применять к целевому хранилищу premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-virtual-machines"></a>Репликация виртуальных машин VMware

Занимает около 15-20 минут для виртуальных машин должны быть обнаружены на сервере vCenter. После завершения обнаружения объекта защищаемый элемент создается в Azure Site Recovery для каждой обнаруженной виртуальной машины. На этом шаге три обнаруженных виртуальных машин, реплицируются в учетные записи хранилища Azure, созданные на предыдущем шаге.

Требуется следующие сведения для защиты обнаруженных виртуальной машины:
* Защищаемый элемент, будут реплицироваться.
* Учетная запись хранения для репликации виртуальной машины. Кроме того хранилище журнала необходим для защиты виртуальных машин в учетную запись хранения premium.
* Сервер обработки, используемый для репликации. Список серверов, доступных процессу были получены и сохранены в ***$ProcessServers [0]****(запускаться для горизонтального масштабирования)* и ***$ProcessServers [1]*** *(ConfigurationServer)* переменных.  
* Учетная запись, используемая для push установки программного обеспечения службы мобильности на компьютерах. Список доступных учетных записей были получены и сохранены в ***$AccountHandles*** переменной.
* Сопоставление контейнера защиты для политики репликации для репликации.
* Группа ресурсов, в котором должны создаваться виртуальных машин на другой ресурс.
* При необходимости виртуальной сети Azure и подсеть, к которой была выполнена Отработка на виртуальной машине должен быть подключен.

Теперь репликация следующих виртуальных машин, используя настройки, заданные в этой таблице


|Виртуальная машина.  |Сервер обработки        |Учетная запись хранения              |Учетная запись хранения журнала  |Политика           |Учетная запись для установки службы Mobility|Целевая группа ресурсов  | Целевой виртуальной сети  |Целевой подсети  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |Запускаться горизонтального масштабирования|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |Виртуальная сеть ASR                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| Н/Д                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Виртуальная сеть ASR                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| Н/Д                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Виртуальная сеть ASR                 |Subnet-1       |   

 
```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzureRmResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzureRmVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableRepication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableRepication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableRepication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

```

После успешного завершения задание включения репликации для виртуальных машин запускается начальная репликация. Начальная репликация может занять некоторое время в зависимости от объема данных репликации и пропускную способность, доступную для репликации. После завершения начальной репликации, виртуальная машина будет перенесена в защищенное состояние. При достижении виртуальной машины защищенных состояние, в котором можно выполнить тестовую отработку отказа для виртуальной машины, добавьте ее в планы восстановления и т. д.

Можно проверить состояние репликации и работоспособность репликации виртуальной машины с помощью командлета Get-ASRReplicationProtectedItem.

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings-for-replicating-virtual-machines"></a>Настройте параметры отработки отказа для репликации виртуальных машин

Параметры отработки отказа для защищенные виртуальные машины можно обновить с помощью командлета Set-ASRReplicationProtectedItem. Ниже приведены некоторые из параметров, которые можно обновить с помощью этого командлета.
* Имя виртуальной машины будут созданы для отработки отказа
* Размер виртуальной Машины из виртуальной машины будут созданы для отработки отказа
* Виртуальная сеть Azure и подсети, должен быть подключен к сетевых адаптеров виртуальной машины на другой ресурс
* Переход на другой ресурс для управляемых дисков
* Применить преимущество использования гибридной Azure
* Назначьте статический IP-адрес из целевой виртуальной сети, чтобы назначить виртуальную машину на другой ресурс.

В этом примере мы изменить размер виртуальной Машины виртуальной машины будут созданы для отработки отказа для виртуальной машины *Win2K12VM1* и указать, что управляемые виртуальной машины с помощью дисков при отработке отказа.

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Выполнить тестовую отработку отказа, проверки и очистки теста отработки отказа

```azurepowershell
#Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

#Get details of the test failover virtual network to be used
TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

#Start the test failover operation
$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
```
По завершении тестового задания отработки отказа можно заметить, что виртуальная машина добавляется суффикс *«-тестирование»* (в данном случае Win2K12VM1 Test) на его имя создается в Azure. 

Теперь можно подключиться к тестовому отработку отказа виртуальной машины и проверки теста отработки отказа.

Очистка тестовой отработки отказа с помощью командлета Start-ASRTestFailoverCleanupJob. Эта операция удалит виртуальную машину, созданную в рамках операцию тестовой отработки отказа.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
```

## <a name="failover-to-azure"></a>Отработка отказа в Azure

На этом шаге мы отработки отказа виртуальной машины Win2K12VM1 на определенный момент времени.

```azurepowershell
# Get the list of available recovery points for Win2K12VM1
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
```
```
CrashConsistent 11/24/2017 5:28:25 PM
```
```azurepowershell

#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 60;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
$Job_Failover.State
```
```
Succeeded
```

После успешного отработку отказа, можно применить отработку отказа операции и установки обратная репликация из Azure обратно в локальный сайт VMware.

## <a name="next-steps"></a>Дальнейшие действия
Представление [Справочник PowerShell восстановления сайта Azure ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) чтобы узнать, как можно выполнить другие задачи, такие как создание планов восстановления и тестированию отработки отказа по плану восстановления с помощью PowerShell.
