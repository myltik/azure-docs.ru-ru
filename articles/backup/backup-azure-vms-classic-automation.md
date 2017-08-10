---
title: "Развертывание архивации виртуальных машин Azure и управление ею с помощью PowerShell | Документация Майкрософт"
description: "Узнайте о том, как развернуть службу архивации Azure и управлять ею с помощью PowerShell."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 2e24b1d9-4375-4049-a28d-e3bc01152f32
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/2/2017
ms.author: markgal;trinadhk;jimpark
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 5f0f06adb8177ce2d17aa0b40666470279c04e22
ms.contentlocale: ru-ru
ms.lasthandoff: 08/03/2017

---
# <a name="use-azurermbackup-cmdlets-to-back-up-virtual-machines"></a>Архивация виртуальных машин с помощью командлетов AzureRM.Backup
> [!div class="op_single_selector"]
> * [Диспетчер ресурсов](backup-azure-vms-automation.md)
> * [Классический](backup-azure-vms-classic-automation.md)
>
>

В этой статье объясняется, как использовать Azure PowerShell для архивации и восстановления виртуальных машин Azure. В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: модель Resource Manager и классическая модель. В этой статье описывается архивация данных в резервное хранилище с использованием классической модели развертывания. Если вы не создали резервное хранилище в своей подписке, см. версию этого руководства для диспетчера ресурсов, [Архивация виртуальных машин с помощью командлетов AzureRM.RecoveryServices.Backup](backup-azure-vms-automation.md). Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

> [!IMPORTANT]
> Теперь вы можете обновить свои резервные хранилища до хранилищ служб восстановления. См. дополнительные сведения об [обновлении резервного хранилища до хранилища служб восстановления](backup-azure-upgrade-backup-to-recovery-services.md). Корпорация Майкрософт рекомендует обновить резервные хранилища до хранилищ служб восстановления.<br/> После 15 октября 2017 года вы не сможете использовать PowerShell для создания резервных хранилищ. **К 1 ноября 2017 года**:
>- Все остальные резервные хранилища будут автоматически обновлены до хранилищ служб восстановления.
>- Вы не сможете получить доступ к данным резервных копий на классическом портале. Вместо этого для доступа к данным резервных копий в хранилищах служб восстановления используйте портал Azure.
>

## <a name="concepts"></a>Основные понятия
В этой статье содержатся сведения о командлетах PowerShell, используемых для архивации виртуальных машин. Чтобы изучить вводные сведения о защите виртуальных машин Azure, ознакомьтесь с разделом [Планирование инфраструктуры резервного копирования виртуальных машин в Azure](backup-azure-vms-introduction.md).

> [!NOTE]
> Перед началом работы изучите [предварительные требования](backup-azure-vms-prepare.md) для работы со службой архивации Azure и [ограничения](backup-azure-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm) текущего решения для архивации виртуальных машин.
>
>

Чтобы эффективно использовать PowerShell, необходимо понимать иерархию объектов и знать, с чего начать.

![Иерархия объектов](./media/backup-azure-vms-classic-automation/object-hierarchy.png)

Две самые важные процедуры — это обеспечение защиты виртуальной машины и восстановление данных из точки восстановления. Эта статья поможет вам приобрести опыт в работе с командлетами PowerShell для выполнения этих двух сценариев.

## <a name="setup-and-registration"></a>Настройка и регистрация
Чтобы начать работу, сделайте следующее:

1. [Скачайте последнюю версию PowerShell](https://github.com/Azure/azure-powershell/releases) (минимальная требуемая версия: 1.0.0).
2. Чтобы получить список доступных командлетов PowerShell для службы архивации Azure, введите следующую команду:

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

С помощью PowerShell можно автоматизировать указанные ниже задачи по настройке и регистрации.

* создать хранилище архивации;
* Регистрация виртуальной машины в службе архивации Azure

### <a name="create-a-backup-vault"></a>создать хранилище архивации;
> [!WARNING]
> Для клиентов, использующих службу архивации Azure впервые, необходимо зарегистрировать поставщика службы архивации для использования с вашей подпиской. Для этого выполните следующую команду: Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
>
>

Вы можете создать новое хранилище службы архивации с помощью командлета **New-AzureRMBackupVault** . Хранилище архивов представляет собой ресурс ARM, поэтому вам потребуется разместить его в группе ресурсов. В консоли Azure PowerShell с повышенными привилегиями выполните следующие команды:

```
PS C:\> New-AzureRmResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRmBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Чтобы получить список всех хранилищ службы архивации в заданной подписке, используйте командлет **Get-AzureRmBackupVault** .

> [!NOTE]
> Объект хранилища службы архивации удобно хранить в переменной. Объект хранилища используется в качестве входных данных для многих командлетов службы архивации Azure.
>
>

### <a name="registering-the-vms"></a>Регистрация виртуальных машин
Чтобы настроить резервное копирование с помощью службы архивации Azure, сначала зарегистрируйте свой компьютер или виртуальную машину в хранилище службы архивации Azure. Командлет **Register-AzureRmBackupContainer** принимает входные данные виртуальной машины Azure IaaS и регистрирует ее в указанном хранилище. Операция регистрации связывает виртуальную машину Azure с хранилищем службы архивации и отслеживает виртуальную машину на протяжении всего цикла резервного копирования.

Во время регистрации виртуальной машины в службе архивации Azure создается объект контейнера верхнего уровня. Контейнер обычно содержит несколько элементов, которые можно архивировать, но контейнер для виртуальной машины содержит только один элемент для резервного копирования.

```
PS C:\> $registerjob = Register-AzureRmBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## <a name="backup-azure-vms"></a>Виртуальные машины службы архивации Azure
### <a name="create-a-protection-policy"></a>Создание политики защиты
Чтобы начать резервное копирование виртуальных машин, создавать новую политику защиты не обязательно. Хранилище поставляется с "политикой по умолчанию", которую можно использовать для быстрого включения защиты. Позднее в эту политику можно внести сведения о правах. Список доступных в хранилище политик можно получить, используя командлет **Get-AzureRmBackupProtectionPolicy**:

```
PS C:\> Get-AzureRmBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [!NOTE]
> Часовой пояс поля BackupTime в PowerShell — UTC. Однако при отображении времени резервного копирования на портале Azure часовой пояс выравнивается с локальной системой вместо со смещением от UTC.
>
>

Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления в службе архивации Azure. Командлет **New-AzureRmBackupRetentionPolicy** создает объекты PowerShell, в которых содержатся сведения о политике хранения. Эти объекты политики хранения используются в качестве входных данных для командлета *New-AzureRmBackupProtectionPolicy* или используются непосредственно с командлетом *Enable-AzureRmBackupProtection*.

Политика резервного копирования определяет время и частоту резервного копирования элемента. Командлет **New-AzureRmBackupProtectionPolicy** создает объект PowerShell, в котором содержатся сведения о политике архивации. Политика архивации используется в качестве входных данных для командлета *Enable-AzureRmBackupProtection* .

```
PS C:\> $Daily = New-AzureRmBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRmBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy $Daily -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### <a name="enable-protection"></a>Включить защиту
Защита включается для двух объектов: элемента и политики, оба объекта должны принадлежать одному хранилищу. После того как политика сопоставится с элементом, рабочий процесс резервного копирования будет срабатывать по определенному расписанию.

```
PS C:\> Get-AzureRmBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRmBackupItem | Enable-AzureRmBackupProtection -Policy $newpolicy
```

### <a name="initial-backup"></a>Начальное резервное копирование
Расписание резервного копирования предполагает создание полной начальной копии элемента и добавочное копирование последующих резервных копий. Если вы хотите, чтобы начальная архивация была выполнена в определенное время или даже немедленно, то используйте командлет **Backup-AzureRmBackupItem** .

```
PS C:\> $container = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -Name "testvm"
PS C:\> $backupjob = Get-AzureRmBackupItem -Container $container | Backup-AzureRmBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [!NOTE]
> Часовой пояс для полей StartTime и EndTime, отображаемый в PowerShell, — UTC. Однако при отображении подобной информации на портале Azure часовой пояс выравнивается с часами локальной системы.
>
>

### <a name="monitoring-a-backup-job"></a>Наблюдение за выполнением задания резервного копирования
Большинство длительных операций службы архивации Azure моделируются в виде задания. Это упрощает отслеживание хода выполнения, при этом вам не нужно все время держать открытым портал Azure.

Чтобы получить последнее состояние выполняющегося задания, используйте командлет **Get-AzureRmBackupJob** .

```
PS C:\> $joblist = Get-AzureRmBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

Вместо опроса этих заданий о ходе их выполнения, который требует дополнительного кода, проще использовать командлет **Wait-AzureRmBackupJob** . Если командлет используется в сценарии, он приостанавливает выполнение сценария до завершения задания или до достижения конкретного значения времени ожидания.

```
PS C:\> Wait-AzureRmBackupJob -Job $joblist[0] -Timeout 43200
```


## <a name="restore-an-azure-vm"></a>Восстановление виртуальной машины Azure
Чтобы восстановить данные резервной копии, необходимо определить архивный элемент и точку восстановления, которая содержит данные на определенный момент времени. Эти сведения предоставляются командлету AzureRmBackupItem, чтобы инициировать восстановление данных из хранилища в учетную запись клиента.

### <a name="select-the-vm"></a>Выбор виртуальной машины.
Чтобы получить объект PowerShell, определяющий правильный архивный элемент, необходимо начать с контейнера в хранилище и пройти постепенно вниз по иерархии объектов. Чтобы выбрать контейнер, который представляет виртуальную машину, используйте командлет **Get-AzureRmBackupContainer** и передайте найденный контейнер в командлет **Get-AzureRmBackupItem**.

```
PS C:\> $backupitem = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRmBackupItem
```

### <a name="choose-a-recovery-point"></a>Выбор точки восстановления
Теперь можно получить список всех точек восстановления для архивного элемента с помощью командлета **Get-AzureRmBackupRecoveryPoint** и выбрать точку восстановления. Обычно пользователи выбирают самую последнюю точку *AppConsistent* в списке.

```
PS C:\> $rp =  Get-AzureRmBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

Переменная ```$rp``` — массив точек восстановления для выбранного архивного элемента, отсортированный по времени в обратном порядке, — последняя точка восстановления имеет индекс 0. Используйте стандартное индексирование массива PowerShell для выбора точки восстановления. Например: ```$rp[0]``` выбирает последнюю точку восстановления.

### <a name="restoring-disks"></a>Восстановление дисков
Операции восстановления, выполняемые на портале Azure и с помощью Azure PowerShell, существенно отличаются. При использовании PowerShell операция восстановления прекращается на восстановлении дисков и сведений о конфигурации из точки восстановления. Она не создает виртуальную машину.

> [!WARNING]
> Командлет Restore-AzureRmBackupItem не создает виртуальную машину. Он только восстанавливает диски в указанную учетную запись хранения. На портале Azure операция восстановления выполняется иначе.
>
>

```
PS C:\> $restorejob = Restore-AzureRmBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

Когда задание восстановления будет выполнено, вы сможете получить сведения об операции восстановления с помощью командлета **Get-AzureRmBackupJobDetails** . Свойство *ErrorDetails* будет содержать сведения, необходимые для повторного создания виртуальной машины.

```
PS C:\> $restorejob = Get-AzureRmBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmBackupJobDetails -Job $restorejob
```

### <a name="build-the-vm"></a>Сборка виртуальной машины
Вы можете собрать виртуальную машину из восстановленных дисков с помощью старых командлетов PowerShell управления службами Azure, новых шаблонов диспетчера ресурсов Azure или даже с помощью портала Azure. В кратком примере мы покажем, как это сделать с помощью командлетов управления службами Azure.

```
$properties  = $details.Properties

$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$blobName = $properties["Config Blob Name"]

$keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
$storageAccountKey = $keys.Primary
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


$destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
$pvr = $obj.PersistentVMRole
$os = $pvr.OSVirtualHardDisk
$dds = $pvr.DataVirtualHardDisks
$osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
$vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

if (!($dds -eq $null))
{
    foreach($d in $dds.DataVirtualHardDisk)
    {
        $lun = 0
        if(!($d.Lun -eq $null))
        {
            $lun = $d.Lun
        }
        $name = "panbhadataDisk" + $lun
        Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
        $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
    }
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

Дополнительные сведения о сборке виртуальной машины из восстановленных дисков см. в документации для следующих командлетов:

* [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
* [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
* [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## <a name="code-samples"></a>Примеры кода
### <a name="1-get-the-completion-status-of-job-sub-tasks"></a>1. Получение состояния выполнения подзадач
Чтобы отслеживать состояние выполнения отдельных подзадач, можно использовать командлет **Get-AzureRmBackupJobDetails** .

```
PS C:\> $details = Get-AzureRmBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### <a name="2-create-a-dailyweekly-report-of-backup-jobs"></a>2. Создание ежедневного или еженедельного отчета для задач резервного копирования
Администраторы обычно хотят знать, какие задачи резервного копирования запускались за последние 24 часа и каково состояние этих заданий резервного копирования. Кроме того, объем передаваемых данных позволяет администраторам оценить ежемесячное использование данных. Следующий сценарий извлекает необработанные данные из службы архивации Azure и отображает сведения в консоли PowerShell.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRmBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRmBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -Type NoteProperty -Name Date -Value $startdate
        $newstatsobj | Add-Member -Type NoteProperty -Name VMName -Value $job.WorkloadName
        $newstatsobj | Add-Member -Type NoteProperty -Name Duration -Value $job.Duration
        $newstatsobj | Add-Member -Type NoteProperty -Name Status -Value $job.Status

        $details = Get-AzureRmBackupJobDetails -Job $job
        $newstatsobj | Add-Member -Type NoteProperty -Name BackupSize -Value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Если вы хотите добавить в выходные данные отчета возможности построения диаграмм, ознакомьтесь с записью [Charting with PowerShell](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

## <a name="next-steps"></a>Дальнейшие действия
Если вы предпочитаете использовать PowerShell для взаимодействия с ресурсами Azure, ознакомьтесь со статьей о защите Windows Server с помощью PowerShell: [Развертывание службы архивации для Windows Server и управление ею](backup-client-automation-classic.md). Доступна и другая статья, посвященная тому, как использовать PowerShell для управления службой архивации DPM: [Развертывание службы архивации для DPM и управление ею](backup-dpm-automation-classic.md). Обе эти статьи имеют две версии — для развертывания с помощью Resource Manager и для классической модели развертывания.

