---
title: "Развертывание резервных копий виртуальных машин, развернутых с использованием Resource Manager, и управление ими с помощью PowerShell | Документация Майкрософт"
description: "Использование PowerShell для развертывания архивации виртуальных машин, развернутых посредством Resource Manager, и управления ею"
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/16/2017
ms.author: markgal; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 3a560e836de9cf70bf448b30b091ad92372bce06
ms.openlocfilehash: 909b16d2e8c72fa29f45e1bdfc778612e00ebbd8


---
# <a name="deploy-and-manage-backups-for-resource-manager-deployed-vms-using-powershell"></a>Развертывание архивации виртуальных машин, развернутых посредством Resource Manager, и управление ею с помощью PowerShell
> [!div class="op_single_selector"]
> * [Диспетчер ресурсов](backup-azure-vms-automation.md)
> * [Классический](backup-azure-vms-classic-automation.md)
> 
> 

В этой статье показано, как выполнять архивацию и восстановление виртуальной машины Azure из хранилища служб восстановления с помощью командлетов Azure PowerShell. Хранилище служб восстановления — это ресурс Azure Resource Manager, используемый для защиты данных и ресурсов-контейнеров в службе архивации Azure и службах Azure Site Recovery. Это хранилище позволяет защитить виртуальные машины, развернутые с помощью Azure Service Manager и Azure Resource Manager.

> [!NOTE]
> В Azure предусмотрены две модели развертывания, позволяющие создавать ресурсы и работать с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование виртуальных машин, созданных с помощью модели Resource Manager.
> 
> 

В этой статье описано, как использовать PowerShell для защиты виртуальной машины и восстановления данных из точки восстановления.

## <a name="concepts"></a>Основные понятия
Если вы не знакомы со службой архивации Azure, общие сведения о службе см. в статье [Что такое служба архивации Azure?](backup-introduction-to-azure-backup.md). Перед началом работы убедитесь, что вам известны предварительные требования для работы со службой архивации Azure и ограничения, применяемые к текущему решению для архивации виртуальных машин.

Чтобы эффективно использовать PowerShell, необходимо понимать иерархию объектов и знать, откуда следует начинать резервное копирование.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Справку по командлету PowerShell AzureRmRecoveryServicesBackup см. в статье [Azure Backup - Recovery Services Cmdlets](https://msdn.microsoft.com/library/mt723320.aspx) (Командлеты служб архивации и восстановления Azure) в библиотеке Azure.
Справку по командлету PowerShell AzureRmRecoveryServicesVault см. в статье [Azure Recovery Service Cmdlets](https://msdn.microsoft.com/library/mt643905.aspx) (Командлеты службы восстановления Azure).

## <a name="setup-and-registration"></a>Настройка и регистрация
Чтобы начать работу, сделайте следующее:

1. [Скачайте последнюю версию PowerShell](https://github.com/Azure/azure-powershell/releases) (минимальная требуемая версия — 1.4.0).
2. Чтобы получить список доступных командлетов PowerShell для службы архивации Azure, введите следующую команду:

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```


С помощью PowerShell можно автоматизировать следующие задачи:

* Создание хранилища служб восстановления
* архивация или защита виртуальных машин Azure;
* активация задания архивации;
* наблюдение за выполнением задания архивации;
* Восстановление виртуальной машины Azure

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления
Чтобы создать хранилище служб восстановления, выполните описанные ниже действия. Хранилище служб восстановления отличается от хранилища службы архивации.

1. Если вы используете службу архивации Azure впервые, выполните командлет **[Register-AzureRMResourceProvider](https://msdn.microsoft.com/library/mt679020.aspx)**, чтобы зарегистрировать поставщик служб восстановления Azure в своей подписке.
   
    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Хранилище служб восстановления представляет собой ресурс Resource Manager, поэтому вам потребуется разместить его в группе ресурсов. Вы можете использовать имеющуюся группу ресурсов или создать новую, выполнив командлет **[New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt678985.aspx)**. При создании группы ресурсов укажите ее имя и расположение.  
   
    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Выполните командлет **[New-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643910.aspx)**, чтобы создать хранилище. Разместите хранилище там же, где находится группа ресурсов.
   
    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Укажите необходимый тип избыточности хранилища: [локально избыточное (LRS)](../storage/storage-redundancy.md#locally-redundant-storage) или [геоизбыточное (GRS)](../storage/storage-redundancy.md#geo-redundant-storage). В следующем примере показано, что для параметра BackupStorageRedundancy для testVault задано значение GeoRedundant.
   
    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```
   
   > [!TIP]
   > Для многих командлетов службы архивации Azure требуется объект хранилища служб восстановления в качестве входных данных. По этой причине объект хранилища служб восстановления резервных копий удобно хранить в переменной.
   > 
   > 

## <a name="view-the-vaults-in-a-subscription"></a>Просмотр хранилищ в подписке
Чтобы получить список всех хранилищ в текущей подписке, используйте командлет **[Get-AzureRmRecoveryServicesVault](https://msdn.microsoft.com/library/mt643907.aspx)**. Он позволяет убедиться в том, что хранилище создано, и увидеть, какие хранилища доступны в подписке.

Выполнив команду Get-AzureRmRecoveryServicesVault, вы получите список всех хранилищ в подписке.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="backup-azure-vms"></a>Виртуальные машины службы архивации Azure
Созданное хранилище служб восстановления можно использовать для защиты виртуальной машины. Однако прежде чем применить защиту, необходимо задать контекст хранилища и проверить политику защиты. Контекст хранилища определяет тип данных, защиту которых обеспечивает хранилище. Политика защиты — это график выполнения заданий резервного копирования и срок хранения каждого моментального снимка резервной копии.

Перед включением защиты на виртуальной машине необходимо задать контекст хранилища. Он применяется ко всем последующим командлетам.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Создание политики защиты
При создании хранилища политика предоставляется по умолчанию. Эта политика активирует задание резервного копирования каждый день в указанное время. Согласно политике по умолчанию, моментальный снимок резервной копии хранится 30 дней. С помощью политики по умолчанию можно быстро защитить виртуальную машину. Кроме того, политику можно изменить позже, задав другие сведения.

Для получения списка политик, доступных в хранилище, используется командлет **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://msdn.microsoft.com/library/mt723300.aspx)**.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Часовой пояс поля BackupTime в PowerShell — UTC. Однако при отображении времени архивации на портале Azure время меняется в соответствии с локальным часовым поясом.
> 
> 

Политика защиты архивации связана по крайней мере с одной политикой хранения.  Политика хранения определяет продолжительность хранения точки восстановления в службе архивации Azure. Для просмотра политики хранения по умолчанию используется командлет **Get-AzureRmRecoveryServicesBackupRetentionPolicyObject** .  Для получения политики расписания по умолчанию используется командлет **Get-AzureRmRecoveryServicesBackupSchedulePolicyObject** . Объекты политик расписания и хранения используются в качестве входных данных в командлете **New-AzureRmRecoveryServicesBackupProtectionPolicy** .

Политика защиты архивации определяет время и частоту архивации элемента. Командлет New-AzureRmRecoveryServicesBackupProtectionPolicy создает объект PowerShell, который содержит сведения о политике архивации. Политика архивации используется в качестве входных данных для командлета Enable-AzureRmRecoveryServicesBackupProtection.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Включить защиту
Защита включается для двух объектов: элемента и политики. Для включения защиты в хранилище требуются оба эти объекта. После того как политика сопоставится с хранилищем, рабочий процесс архивации будет активироваться по времени, определенному в политике расписания.

Включение защиты на виртуальных машинах ARM без шифрования

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Чтобы включить защиту на зашифрованных виртуальных машинах [зашифрованных с помощью BEK и KEK], необходимо предоставить службе архивации Azure разрешения на доступ для чтения ключей и секретов из хранилища ключей.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'KeyVaultName' -ResourceGroupName 'RGNameOfKeyVault' -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Если вы работаете в облаке Azure для государственных организаций, используйте значение ff281ffe-705c-4f53-9f37-a40e6f2c68f3 для параметра **-ServicePrincipalName** в командлете Set-AzureRmKeyVaultAccessPolicy.
> 
> 

Для виртуальных машин на основе диспетчера служб Azure

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Изменение политики защиты
Чтобы изменить политику, измените объект BackupSchedulePolicyObject или BackupRetentionPolicy, а также политику с помощью командлета Set-AzureRmRecoveryServicesBackupProtectionPolicy.

В следующем примере количество дней хранения изменяется на 365.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## <a name="run-an-initial-backup"></a>Выполнение начальной архивации
Расписание архивации активирует полную архивацию при начальной архивации элемента. При последующем выполнении архивации резервная копия будет добавочной. Если вы хотите, чтобы начальная архивация была выполнена в определенное время или даже немедленно, используйте командлет **[Backup-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723312.aspx)**.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Часовой пояс для полей StartTime и EndTime в PowerShell — UTC. Однако при отображении времени на портале Azure время меняется в соответствии с локальным часовым поясом.
> 
> 

## <a name="monitoring-a-backup-job"></a>Наблюдение за выполнением задания резервного копирования
Большинство длительных операций службы архивации Azure моделируются в виде задания. Это упрощает отслеживание хода выполнения, при этом вам не нужно все время держать открытым портал Azure.

Чтобы получить последнее состояние выполняющегося задания, используйте командлет Get-AzureRMRecoveryservicesBackupJob.

```
PS C:\ > $joblist = Get-AzureRmRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Вместо опроса этих заданий о ходе их выполнения, который требует выполнения дополнительного кода, используйте командлет **[Wait-AzureRmRecoveryServicesBackupJob](https://msdn.microsoft.com/library/mt723321.aspx)**. Он приостанавливает выполнение сценария до завершения задания или до достижения конкретного значения времени ожидания.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Восстановление виртуальной машины Azure
Операции восстановления виртуальной машины через портал Azure и с помощью Azure PowerShell существенно отличаются. При использовании PowerShell операция восстановления завершается созданием дисков и сведений о конфигурации из точки восстановления. Виртуальная машина при этом не создается, но предоставляются инструкции по созданию виртуальной машины с дисков. Однако для полного восстановления виртуальной машины необходимо выполнить следующие процедуры:

* Выбор виртуальной машины.
* Выбор точки восстановления
* Восстановление дисков
* Создание виртуальной машины с хранимых дисков

На представленной ниже схеме показана иерархия объектов от RecoveryServicesVault до BackupRecoveryPoint.

![Иерархия объектов служб восстановления с BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Чтобы восстановить данные резервной копии, определите архивный элемент и точку восстановления, которая содержит данные на определенный момент времени. Затем выполните командлет **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)**, чтобы восстановить данные из хранилища в учетную запись клиента.

### <a name="select-the-vm"></a>Выбор виртуальной машины.
Чтобы получить объект PowerShell, определяющий правильный архивный элемент, начните с контейнера в хранилище и пройдите постепенно вниз по иерархии объектов. Чтобы выбрать контейнер, который представляет виртуальную машину, используйте командлет **[Get-AzureRmRecoveryServicesBackupContainer](https://msdn.microsoft.com/library/mt723319.aspx)** и передайте найденный контейнер в командлет **[Get-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723305.aspx)**.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Выбор точки восстановления
Выполните командлет **[Get-AzureRMRecoveryServicesBackupRecoveryPoint](https://msdn.microsoft.com/library/mt723308.aspx)**, чтобы получить полный список точек восстановления. Выберите нужную точку восстановления. Если вы не знаете, какую точку восстановления выбрать, используйте последнюю точку RecoveryPointType = AppConsistent в списке.

В следующем сценарии переменная **$rp**представляет собой массив точек восстановления для выбранного архивного элемента. Массив сортируется по времени в обратном порядке, так что последняя точка восстановления получает индекс 0. Используйте стандартное индексирование массива PowerShell для выбора точки восстановления. Например, $rp[0] выбирает последнюю точку восстановления.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM
                              /recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>Восстановление дисков
Выполните командлет **[Restore-AzureRmRecoveryServicesBackupItem](https://msdn.microsoft.com/library/mt723316.aspx)**, чтобы восстановить данные и конфигурацию архивного элемента до точки восстановления. Используйте выбранную точку восстановления как значение для параметра **-RecoveryPoint** . В предыдущем примере кода была выбрана точка восстановления **$rp [0]** . В следующем примере кода для восстановления на диск выбрана точка восстановления **$rp [0]** .

Восстановление дисков и сведений о конфигурации

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Когда задание восстановления будет выполнено, выполните командлет **[Get-AzureRmRecoveryServicesBackupJobDetails](https://msdn.microsoft.com/library/mt723310.aspx)**, чтобы получить сведения об операции восстановления. Свойство JobDetails содержит сведения, необходимые для повторного создания виртуальной машины.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

Восстановив диски, перейдите к следующему разделу, где вы найдете информацию о создании виртуальной машины.

## <a name="create-a-vm-from-restored-disks"></a>Создание виртуальной машины с восстановленного диска
После восстановления дисков создайте и настройте виртуальную машину с диска, выполнив описанные ниже действия.

> [!NOTE]
> При создании зашифрованных виртуальных машин с помощью восстановленных дисков у роли должно быть разрешение на выполнение команды **Microsoft.KeyVault/vaults/deploy/action**. Если у роли нет этого разрешения, создайте пользовательскую роль с этим действием. Дополнительные сведения см. в статье [Пользовательские роли в Azure RBAC](../active-directory/role-based-access-control-custom-roles.md).
> 
> 

1. Запросите свойства восстановленного диска для получения сведений о задании.
   
    ```
    PS C:\> $properties = $details.properties
    PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
    PS C:\> $containerName = $properties["Config Blob Container Name"]
    PS C:\> $blobName = $properties["Config Blob Name"]
    ```
2. Задайте контекст хранилища Azure и восстановите файл конфигурации JSON.
   
    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName testvault
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```
3. Используйте файл конфигурации JSON для создания конфигурации виртуальной машины.
   
    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.HardwareProfile.VirtualMachineSize -VMName "testrestore"
    ```
4. Подключите диск операционной системы и диски данных.
   
      Для виртуальных машин без шифрования
   
      ```
      PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -CreateOption “Attach”
      PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType foreach($dd in $obj.StorageProfile.DataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach
       }
       ```
      For encrypted VMs, you need to specify [Key vault information](https://msdn.microsoft.com/library/dn868052.aspx) before you can attach disks.
   
      ```
      PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.StorageProfile.OSDisk.VirtualHardDisk.Uri -DiskEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007" -DiskEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -KeyEncryptionKeyUrl "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007" -KeyEncryptionKeyVaultId "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault" -CreateOption "Attach" -Windows    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType foreach($dd in $obj.StorageProfile.DataDisks)     {     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.VirtualHardDisk.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption Attach     }     ```
5. Задайте параметры сети.
   
    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Создайте виртуальную машину.
   
    ```
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.StorageProfile.OSDisk.OperatingSystemType
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="next-steps"></a>Дальнейшие действия
Если вы предпочитаете использовать PowerShell для взаимодействия с ресурсами Azure, ознакомьтесь со статьей о защите Windows Server с помощью PowerShell: [Развертывание службы архивации для Windows Server и управление ею](backup-client-automation.md). Доступна и другая статья, посвященная тому, как использовать PowerShell для управления службой архивации DPM: [Развертывание службы архивации для DPM и управление ею](backup-dpm-automation.md). Обе эти статьи имеют две версии — для развертывания с помощью Resource Manager и для классической модели развертывания.  




<!--HONumber=Jan17_HO3-->


