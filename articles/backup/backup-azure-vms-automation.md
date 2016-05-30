<properties
   pageTitle="Развертывание резервной копии виртуальной машины ARM с помощью PowerShell и управление ею | Microsoft Azure"
   description="Использование PowerShell для развертывания резервных копий и управления ими в Azure для виртуальных машин ARM"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="05/09/2016"
   ms.author="markgal; trinadhk"/>

# Развертывание резервной копии виртуальной машины ARM с помощью PowerShell и управление ею

> [AZURE.SELECTOR]
- [Resource Manager и PowerShell](backup-azure-vms-automation.md)
- [Классическая модель и PowerShell](backup-azure-vms-classic-automation.md)

В этой статье показано, как с помощью Azure PowerShell выполнять архивацию и восстановление виртуальной машины Azure из хранилища служб восстановления. Хранилище служб восстановления — это ресурс Azure Resource Manager (ARM), используемый для защиты данных и ресурсов-контейнеров в службе архивации Azure и службах Azure Site Recovery. Хранилище служб восстановления можно использовать при работе в развертывании с использованием ARM. Это хранилище позволяет защитить виртуальные машины, развернутые с помощью диспетчера служб Azure и Azure Resource Manager.

>[AZURE.NOTE] В Azure предусмотрены две модели развертывания, позволяющие создавать ресурсы и работать с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md). В этой статье рассматривается использование виртуальных машин, созданных с помощью модели Resource Manager.

В этой статье описано, как использовать PowerShell для защиты виртуальной машины и восстановления данных из точки восстановления.

## Основные понятия

Если вы не знакомы со службой архивации Azure, общие сведения о службе см. в статье [Что такое служба архивации Azure?](backup-introduction-to-azure-backup.md) Перед началом работы убедитесь, что вам известны предварительные требования для работы со службой архивации Azure и ограничения, применяемые к текущему решению для архивации виртуальных машин.

Чтобы эффективно использовать PowerShell, необходимо понимать иерархию объектов и знать, откуда следует начинать резервное копирование.

![Иерархия объектов служб восстановления](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## Настройка и регистрация

Чтобы начать работу, сделайте следующее:

1. [Скачайте последнюю версию PowerShell](https://github.com/Azure/azure-powershell/releases) (минимальная требуемая версия: 1.0.0).

2. Чтобы получить список доступных командлетов PowerShell для службы архивации Azure, введите следующую команду:

```
PS C:\WINDOWS\system32> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.0.7      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.0.7      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.0.7      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
```


С помощью PowerShell можно автоматизировать следующие задачи:

- Создание хранилища служб восстановления
- архивация или защита виртуальных машин Azure;
- активация задания архивации;
- наблюдение за выполнением задания архивации;
- Восстановление виртуальной машины Azure

## Создание хранилища служб восстановления

> [AZURE.TIP] Для клиентов, использующих службу архивации Azure впервые, необходимо зарегистрировать поставщика служб восстановления Azure для использования с вашей подпиской. Для этого выполните следующую команду: Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices".

Новое хранилище служб восстановления можно создать с помощью командлета **New-AzureRmRecoveryServicesVault**. Хранилище служб восстановления представляет собой ресурс ARM, поэтому вам потребуется разместить его в группе ресурсов. В консоли Azure PowerShell с повышенными привилегиями выполните следующие команды:

```
PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
```

Чтобы просмотреть список всех хранилищ служб восстановления в подписке, используйте командлет **Get-AzureRmRecoveryServicesVault**.

### Установка избыточности хранилища
При создании хранилища служб восстановления укажите тип избыточности хранилища, который следует использовать: локально избыточное хранилище или геоизбыточное хранилище. В следующем примере показано, что для параметра BackupStorageRedundancy для testVault задано значение GeoRedundant.

```
PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```


> [AZURE.TIP] Для многих командлетов службы архивации Azure требуется объект хранилища служб восстановления в качестве входных данных. По этой причине объект хранилища служб восстановления резервных копий удобно хранить в переменной.


## Виртуальные машины службы архивации Azure

Перед включением защиты на виртуальной машине необходимо задать контекст хранилища. Он применяется ко всем последующим командлетам.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### Создание политики защиты

При создании хранилища политика предоставляется по умолчанию. Эта политика активирует задание архивации каждый день в 21:30. Моментальный снимок резервной копии хранится в течение 30 дней. С помощью политики по умолчанию можно быстро защитить виртуальную машину. Кроме того, политику можно изменить позже, задав другие сведения.

Просмотреть список доступных политик в хранилище можно с помощью командлета Get-AzureRmRecoveryServicesBackupProtectionPolicy.

```
PS C:\WINDOWS\system32> get-AzureRMRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] Часовой пояс поля BackupTime в PowerShell — UTC. Однако при отображении времени архивации на портале Azure время меняется в соответствии с локальным часовым поясом.

Политика защиты архивации связана по крайней мере с одной политикой хранения. Политика хранения определяет продолжительность хранения точки восстановления в службе архивации Azure. Для просмотра политики хранения по умолчанию используется командлет Get-AzureRmRecoveryServicesBackupRetentionPolicyObject. Аналогичным образом можно получить политику расписания по умолчанию, используя командлет Get-AzureRmRecoveryServicesBackupSchedulePolicyObject. Объекты политик расписания и хранения используются в качестве входных данных в командлете New-AzureRmRecoveryServicesBackupProtectionPolicy.

Политика защиты архивации определяет время и частоту архивации элемента. Командлет New-AzureRmRecoveryServicesBackupProtectionPolicy создает объект PowerShell, который содержит сведения о политике архивации. Политика архивации используется в качестве входных данных для командлета Enable-AzureRmRecoveryServicesBackupProtection.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### Включить защиту

Защита включается для двух объектов: элемента и политики. Для включения защиты в хранилище требуются оба эти объекта. После того как политика сопоставится с элементом, рабочий процесс архивации будет активироваться по времени, определенному в политике расписания.

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Для виртуальных машин на основе диспетчера служб Azure

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### Изменение политики защиты

Чтобы изменить политику, измените объект BackupSchedulePolicyObject или BackupRetentionPolicy, а также политику с помощью командлета Set-AzureRmRecoveryServicesBackupProtectionPolicy.

В следующем примере количество дней хранения изменяется на 365.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRMRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## Выполнение начальной архивации

Расписание архивации активирует полную архивацию при начальной архивации элемента. При последующем выполнении архивации резервная копия будет добавочной. Если вы хотите, чтобы начальная архивация была выполнена в определенное время или даже немедленно, используйте командлет Backup-AzureRmRecoveryServicesBackupItem.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM";
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM";
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item;
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM            cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> AZURE.NOTE. Часовой пояс для полей StartTime и EndTime, отображаемый в PowerShell, — UTC. Однако при отображении времени на портале Azure время меняется в соответствии с локальным часовым поясом.

## Наблюдение за выполнением задания резервного копирования

Большинство длительных операций службы архивации Azure моделируются в виде задания. Это упрощает отслеживание хода выполнения, при этом вам не нужно все время держать открытым портал Azure.

Чтобы получить последнее состояние выполняющегося задания, используйте командлет Get-AzureRMRecoveryservicesBackupJob.

```
PS C:\ > $joblist = Get-AzureRMRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Вместо опроса этих заданий о ходе их выполнения, который требует выполнения дополнительного кода, проще использовать командлет Wait-AzureRmRecoveryServicesBackupJob. Если командлет используется в сценарии, он приостанавливает выполнение сценария до завершения задания или до достижения конкретного значения времени ожидания.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## Восстановление виртуальной машины Azure

Чтобы восстановить данные резервной копии, необходимо определить архивный элемент и точку восстановления, которая содержит данные на определенный момент времени. Эти сведения предоставляются командлету AzureRMRecoveryServicesBackupItem, чтобы запустить восстановление данных из хранилища в учетную запись клиента.

### Выбор виртуальной машины.

Чтобы получить объект PowerShell, определяющий правильный архивный элемент, начните с контейнера в хранилище и пройдите постепенно вниз по иерархии объектов. Чтобы выбрать контейнер, который представляет виртуальную машину, используйте командлет Get-AzureRmRecoveryServicesBackupContainer и передайте найденный контейнер в командлет Get-AzureRmRecoveryServicesBackupItem.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem=Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### Выбор точки восстановления

Теперь можно получить список всех точек восстановления для архивного элемента с помощью командлета Get-AzureRMRecoveryServicesBackupRecoveryPoint и выбрать точку восстановления. Обычно пользователи выбирают самую последнюю точку AppConsistent в списке.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRMRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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

Переменная $rp — это массив точек восстановления для выбранного архивного элемента, отсортированный по времени в обратном порядке — последняя точка восстановления имеет индекс 0. Используйте стандартное индексирование массива PowerShell для выбора точки восстановления. Например, $rp[0] выбирает последнюю точку восстановления.

### Восстановление дисков

Операции восстановления, выполняемые на портале Azure и с помощью Azure PowerShell, существенно отличаются. При использовании PowerShell операция восстановления прекращается на восстановлении дисков и сведений о конфигурации из точки восстановления. Она не создает виртуальную машину.

> [AZURE.WARNING] Командлет Restore-AzureRMRecoveryServicesBackupItem не создает виртуальную машину, а только восстанавливает диски в указанную учетную запись хранения. Это отличается от операций, происходящих на портале Azure.

```
PS C:\> $restorejob = Restore-AzureRMRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount
 -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Restore               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Когда задание восстановления будет выполнено, вы можете получить сведения об этой операции с помощью командлета Get-AzureRmRecoveryServicesBackupJobDetails. Свойство JobDetails будет содержать сведения, необходимые для повторного создания виртуальной машины.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

## Регистрация Windows Server или DPM в хранилище служб восстановления

После создания хранилища служб восстановления скачайте последнюю версию агента и учетные данные хранилища и сохраните их в удобном расположении, например C:\\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

На сервере Windows Server или DPM запустите командлет [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx), чтобы зарегистрировать компьютер в хранилище.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

<!---HONumber=AcomDC_0518_2016-->