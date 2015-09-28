<properties
	pageTitle="Развертывание резервной копии виртуальной машины Azure с помощью PowerShell и управление ею | Microsoft Azure"
	description="Узнайте о том, как развернуть службу архивации Azure и управлять ею с помощью PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2015" ms.author="trinadhk";"aashishr" />


# Развертывание резервной копии виртуальной машины Azure с помощью PowerShell и управление ею
В этой статье объясняется, как использовать Azure PowerShell для резервного копирования и восстановления виртуальных машин Azure IaaS.

## Основные понятия
См. статью [Общие сведения о резервном копировании Azure IaaS](backup-azure-vms-introduction.md) в документации службы архивации Azure. В этом документе объясняется, зачем следует выполнять резервное копирование виртуальной машины, а также содержится информация о необходимых условиях и ограничениях этой процедуры.

Чтобы эффективно использовать PowerShell, необходимо понимать иерархию объектов и знать, откуда следует начинать резервное копирование.

![Иерархия объектов](./media/backup-azure-vms-automation/object-hierarchy.png)

Две самые важные процедуры включают в себя обеспечение защиты виртуальной машины и восстановление данных из точки восстановления. Эта статья поможет вам приобрести опыт в работе с командлетами PowerShell, чтобы обеспечить выполнение этих двух сценариев.


## Настройка и регистрация
Чтобы начать работу, сделайте следующее:

1. [Скачайте последнюю версию PowerShell](https://github.com/Azure/azure-powershell/releases) (минимальная требуемая версия: 0.9.8)

2. Включите командлеты службы архивации Azure. Для этого перейдите в режим *AzureResourceManager* с помощью командлета **Switch-AzureMode**:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

С помощью PowerShell можно автоматизировать указанные ниже задачи по настройке и регистрации.

- создать хранилище архивации;
- Регистрация виртуальной машины в службе архивации Azure

### создать хранилище архивации;

> [AZURE.WARNING]Для клиентов, использующих службу архивации Azure впервые, необходимо зарегистрировать поставщика службы архивации для использования с вашей подпиской. Для этого выполните следующую команду: Register-AzureProvider -ProviderNamespace "Microsoft.Backup"

Вы можете создать новое хранилище службы архивации с помощью командлета **New-AzureRMBackupVault**. Хранилище архивов представляет собой ресурс ARM, поэтому вам потребуется разместить его в группе ресурсов. В консоли Azure PowerShell с повышенными привилегиями выполните следующие команды:

```
PS C:\> New-AzureResourceGroup –Name “test-rg” –Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Чтобы получить список всех хранилищ службы архивации в данной подписке, используйте командлет **Get-AzureRMBackupVault**.

> [AZURE.NOTE]Объект хранилища службы архивации удобно хранить в переменной. Объект хранилища используется в качестве входных данных для многих командлетов службы архивации Azure.


### Регистрация виртуальных машин
Чтобы настроить резервное копирование с помощью службы архивации Azure, сначала зарегистрируйте свой компьютер или виртуальную машину в хранилище службы архивации Azure. Командлет **Register-AzureRMBackupContainer** берет входные данные виртуальной машины Azure IaaS и регистрирует ее в указанном хранилище. Операция регистрации связывает виртуальную машину Azure с хранилищем службы архивации и отслеживает виртуальную машину на протяжении всего цикла резервного копирования.

Во время регистрации виртуальной машины в службе архивации Azure создается объект контейнера верхнего уровня. Контейнер обычно содержит несколько элементов, которые можно архивировать, но контейнер для виртуальной машины содержит только один элемент для резервного копирования.

```
PS C:\> $registerjob = Register-AzureRMBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## Виртуальные машины службы архивации Azure

### Создание политики защиты
Чтобы начать резервное копирование виртуальных машин, создавать новую политику защиты не обязательно. Хранилище поставляется с «политикой по умолчанию», которую можно использовать для быстрого включения защиты. Позднее в эту политику можно внести сведения о правах. Список доступных в хранилище политик можно получить с помощью командлета **Get-AzureRMBackupProtectionPolicy**:

```
PS C:\> Get-AzureRMBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

Политика резервного копирования связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления в службе архивации Azure. Командлет **New-AzureRMBackupRetentionPolicy** создает объекты PowerShell, которые содержат сведения о политике хранения. Эти объекты политики хранения используются в качестве входных данных для командлета *New-AzureRMBackupProtectionPolicy* или используются непосредственно с командлетом *Enable-AzureRMBackupProtection*.

Политика резервного копирования определяет время и частоту резервного копирования элемента. Командлет **New-AzureRMBackupProtectionPolicy** создает объект PowerShell, который содержит сведения о политике резервного копирования. Политика резервного копирования используется в качестве входных данных для командлета *Enable-AzureRMBackupProtection*.

```
PS C:\> $Daily = New-AzureRMBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRMBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy ($Daily) -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### Включить защиту
Защита включается для двух объектов: элемента и политики, оба объекта должны принадлежать одному хранилищу. После того как политика сопоставится с элементом, рабочий процесс резервного копирования будет срабатывать по определенному расписанию.

```
PS C:\> Get-AzureRMBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRMBackupItem | Enable-AzureRMBackupProtection -Policy $newpolicy
```

### Начальное резервное копирование
Расписание резервного копирования предполагает создание полной начальной копии элемента и добавочное копирование последующих резервных копий. Если вы хотите, чтобы начальное резервное копирование произошло в определенное время или даже немедленно, используйте командлет **Backup-AzureRMBackupItem**:

```
PS C:\> $container = Get-AzureRMBackupContainer -Vault $backupvault -type AzureVM -name "testvm"
PS C:\> $backupjob = Get-AzureRMBackupItem -Container $container | Backup-AzureRMBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

### Наблюдение за выполнением задания резервного копирования
Большинство длительных операций службы архивации Azure моделируются в виде задания. Это упрощает отслеживание хода выполнения, при этом вам не нужно все время держать открытым портал Azure.

Чтобы получить последнее состояние выполняющегося задания, используйте командлет **Get-AzureRMBackupJob**.

```
PS C:\> $joblist = Get-AzureRMBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

Вместо опроса этих заданий о ходе их выполнения, который требует выполнения дополнительного кода, проще использовать командлет **Wait-AzureRMBackupJob**. Если командлет используется в сценарии, он приостанавливает выполнение сценария до завершения задания или до достижения конкретного значения времени ожидания.

```
PS C:\> Wait-AzureRMBackupJob -Job $joblist[0] -Timeout 43200
```


## Восстановление виртуальной машины Azure

Чтобы восстановить данные резервной копии, необходимо определить архивный элемент и точку восстановления, которая содержит данные на определенный момент времени. Эти сведения предоставляются командлету AzureRMBackupItem, чтобы инициировать восстановление данных из хранилища в учетную запись клиента.

### Выбор виртуальной машины.

Чтобы получить объект PowerShell, определяющий правильный архивный элемент, необходимо начать с контейнера в хранилище и пройти постепенно вниз по иерархии объектов. Чтобы выбрать контейнер, который представляет виртуальную машину, используйте командлет **Get-AzureRMBackupContainer** и передайте найденный контейнер в командлет **Get-AzureRMBackupItem**.

```
PS C:\> $backupitem = Get-AzureBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRMBackupItem
```

### Выбор точки восстановления

Теперь можно получить список всех точек восстановления для архивного элемента с помощью командлета **Get-AzureRMBackupRecoveryPointt** и выбрать точку восстановления. Обычно пользователи выбирают самую последнюю точку *AppConsistent* в списке.

```
PS C:\> $rp =  Get-AzureRMBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

### Восстановление дисков

Операции восстановления, выполняемые на портале Azure и с помощью Azure PowerShell, существенно отличаются. При использовании PowerShell операция восстановления прекращается на восстановлении дисков и сведений о конфигурации из точки восстановления. Она не создает виртуальную машину.

> [AZURE.WARNING]Командлет Restore-AzureRMBackupItem не создает виртуальную машину. Он только восстанавливает диски в указанную учетную запись хранения. На портале Azure операция восстановления выполняется иначе.

```
PS C:\> $restorejob = Restore-AzureRMBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

Когда задание восстановления будет выполнено, вы можете получить сведения об операции восстановления с помощью командлета **Get-AzureRMBackupJobDetails**. Свойство *ErrorDetails* будет содержать сведения, необходимые для повторного создания виртуальной машины.

```
PS C:\> $restorejob = Get-AzureRMBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRMBackupJobDetails -Job $restorejob
```

### Сборка виртуальной машины

Вы можете собрать виртуальную машину из восстановленных дисков с помощью старых командлетов PowerShell диспетчера служб Azure, новых шаблонов диспетчера ресурсов Azure или даже с помощью портала Azure. В кратком примере мы покажем, как это сделать с помощью командлетов диспетчера служб Azure.

```
 $properties  = $details.Properties

 $storageAccountName = $properties["TargetStorageAccountName"]
 $containerName = $properties["TargetContainerName"]
 $blobName = $properties["TargetBlobName"]

 Switch-AzureMode AzureServiceManagement

 $keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
 $storageAccountKey = $keys.Primary
 $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


 $destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
 Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


 $obj = [xml](Get-Content $destination_path)
 $pvr = $obj.PersistentVMRole
 $os = $pvr.OSVirtualHardDisk
 $dds = $pvr.DataVirtualHardDisks
 $osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
 $vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

 if (!($dds -eq $null))
 {
	 foreach($d in $dds.DataVirtualHardDisk)
 	 {
		 $lun = 0;
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

Дополнительные сведения о сборке виртуальной машины из восстановленных дисков см. в документации о следующих командлетах:

- [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
- [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
- [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

<!---HONumber=Sept15_HO3-->