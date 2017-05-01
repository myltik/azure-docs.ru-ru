---
title: "Автоматическая архивация версии 2 для виртуальных машин SQL Server 2016 в Azure | Документация Майкрософт"
description: "Описывает функцию автоматической архивации для виртуальных машин SQL Server 2016 в Azure. Данная статья относится к виртуальным машинам, использующим модель развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/05/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: dbf4b04ad92d9339b15d7f247b947dd58b17daa5
ms.lasthandoff: 04/06/2017


---
# <a name="automated-backup-v2-for-sql-server-2016-azure-virtual-machines-resource-manager"></a>Автоматическая архивация версии 2 для виртуальных машин SQL Server 2016 в Azure (Resource Manager)

Служба автоматической архивации версии 2 автоматически настраивает [управляемую архивацию на портал Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) для всех существующих и новых баз данных на виртуальной машине Azure под управлением SQL Server 2016 Standard, SQL Server 2016 Enterprise или SQL Server 2016 Developer. Это позволяет настроить регулярную архивацию базы данных с использованием надежного хранилища больших двоичных объектов Azure. Автоматическая архивация версии 2 зависит от [расширения агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Предварительные требования
Для использования автоматической архивации версии 2 необходимо выполнить следующие предварительные требования.

**Операционная система**

- Windows Server 2012 R2
- Windows Server 2016

**Версия/выпуск SQL Server**

- SQL Server 2016 Standard
- SQL Server 2016 Enterprise
- SQL Server 2016 Developer.

> [!IMPORTANT]
> Автоматическая архивация версии 2 работает с SQL Server 2016. Если используется SQL Server 2014, то для архивации баз данных можно применять автоматическую архивацию версии 1. Дополнительную информацию см. в статье [Автоматическая архивация для виртуальных машин SQL Server 2014 в Azure](virtual-machines-windows-sql-automated-backup.md).

**Конфигурация базы данных**

- В конечных базах данных должна использоваться модель полного восстановления.
- Для системных баз данных нет необходимости использовать модель полного восстановления. Тем не менее, если требуется создавать резервные копии журналов для базы данных Model или MSDB, то необходимо использовать модель полного восстановления.

Дополнительные сведения о влиянии модели полного восстановления на резервные копии см. в разделе [Резервное копирование в модели полного восстановления](https://technet.microsoft.com/library/ms190217.aspx).

**Модель развертывания Azure**

- Диспетчер ресурсов

> [!NOTE]
> Автоматическая архивация зависит от **расширения агента IaaS для SQL Server**. В текущей коллекции образов виртуальных машин SQL это расширение присутствует по умолчанию. Дополнительные сведения см. в статье [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) (Расширение агента SQL Server IaaS).

## <a name="settings"></a>Параметры
В приведенной ниже таблице описаны параметры настройки автоматической архивации версии 2. Фактическая процедура настройки может варьироваться в зависимости от того, используете вы портал Azure или команды Azure Windows PowerShell.

### <a name="basic-settings"></a>Основные параметры

| Настройка | Диапазон (по умолчанию) | Описание |
| --- | --- | --- |
| **Автоматическая архивация** | Включено/отключено (отключено) | Включает или отключает автоматическую архивацию для виртуальной машины Azure под управлением SQL Server 2016 Standard или SQL Server 2016 Enterprise. |
| **Срок хранения** | 1–30 дней (30 дней) | Число дней хранения резервных копий. |
| **Учетная запись хранения** | Учетная запись хранения Azure. | Учетная запись хранения Azure для хранения файлов автоматической архивации в хранилище больших двоичных объектов. Там же создается контейнер для хранения всех файлов резервных копий. Соглашение об именовании файлов резервных копий включает в себя дату, время и GUID базы данных. |
| **Шифрование** |Включено/отключено (отключено) | Включает или отключает шифрование. Если шифрование включено, то сертификаты, используемые для восстановления резервной копии, сохраняются в указанной учетной записи хранения в том же контейнере **automaticbackup** с использованием того же соглашения об именовании файлов. При изменении пароля создается новый сертификат; при этом старый сертификат сохраняется для восстановления предыдущих резервных копий. |
| **Пароль** |Текст пароля | Пароль для ключей шифрования. Требуется, только если шифрование включено. Для восстановления зашифрованной резервной копии требуется правильный пароль и соответствующий сертификат, который использовался при создании резервной копии. |

### <a name="advanced-settings"></a>Дополнительные параметры

| Настройка | Диапазон (по умолчанию) | Описание |
| --- | --- | --- |
| **System Database Backups** (Архивация системных баз данных) | Включено/отключено (отключено) | При включении этой функции также выполняется архивация системных баз данных: Master, MSDB и Model. Если требуется создавать резервные копии журналов для баз данных MSDB и Model, убедитесь, что они функционируют в режиме полного восстановления. Резервные копии журналов для базы данных Master не создаются. Кроме того, не создаются и резервные копии базы данных TempDB. |
| **Расписание архивации** | Ручное или автоматическое (автоматическое) | По умолчанию расписание архивации будет определяться автоматически в зависимости от размера журнала. Ручное расписание архивации позволяет пользователю указать временное окно для создания резервных копий. В этом случае резервные копии будут создаваться только с указанной частотой во время указанного временного окна в заданный день. |
| **Full backup frequency** (Частота полной архивации) | Ежедневно или еженедельно | Частота создания полных резервных копий. В обоих случаях полная архивация начинается во время следующего запланированного временного окна. Если выбрана еженедельная архивация, то она может охватывать несколько дней, пока не будут успешно созданы резервные копии всех баз данных. |
| **Full backup start time** (Время начала полной архивации) | 00:00–23:00 (01:00) | Время начала полной архивации в заданный день. |
| **Full backup time window** (Временное окно полной архивации) | 1–23 ч (1 ч) | Длительность временного окна для полной архивации в заданный день. |
| **Log backup frequency** (Частота создания резервных копий журналов) | 5–60 мин (60 мин) | Частота создания резервных копий журналов. |

## <a name="understanding-full-backup-frequency"></a>Основные сведения о частоте полной архивации
Важно понимать разницу между ежедневными и еженедельными полными резервными копиями. Для этого мы рассмотрим два примера сценария.

### <a name="scenario-1-weekly-backups"></a>Сценарий 1. Еженедельные резервные копии
У вас есть виртуальная машина SQL Server, которая содержит несколько очень больших баз данных.

В понедельник вы включили автоматическую архивацию версии 2 со следующими параметрами:

- "Расписание архивации": **Ручное**;
- "Full backup frequency" (Частота полной архивации): **Еженедельная**;
- "Full backup start time" (Время начала полной архивации): **01:00**;
- "Full backup time window" (Временное окно полной архивации): **1 ч**.

Это означает, что следующее доступное окно архивации длительностью в 1 час наступит во вторник в 01:00. В это время служба автоматической архивации начнет по очереди архивировать ваши базы данных. В этом сценарии базы данных достаточно велики, чтобы служба архивации успела создать резервные копии нескольких первых баз данных. Однако по прошествии 1 часа все базы данных будут заархивированы.

В этом случае служба автоматической архивации начнет создавать резервные копии остальных баз данных на следующий день, в среду, в 01:00, и будет это делать на протяжении 1 часа. Если и на этот раз будут заархивированы не все базы данных, то служба архивации продолжит архивацию на следующий день в то же время. Это будет продолжаться, пока все базы данных не будут заархивированы.

Как только наступит следующий вторник, служба автоматической архивации снова начнет создавать резервные копии всех баз данных.

Этот сценарий показывает, что автоматическая архивация будет выполняться только в пределах указанного временного окна, а каждая база данных будет архивироваться раз в неделю. Также показано, что создание резервных копий может длиться несколько дней, если невозможно завершить архивацию всех баз данных за один день.

### <a name="scenario-2-daily-backups"></a>Сценарий 2. Ежедневные резервные копии
У вас есть виртуальная машина SQL Server, которая содержит несколько очень больших баз данных.

В понедельник вы включили автоматическую архивацию версии 2 со следующими параметрами:

- "Расписание архивации": "Ручное";
- "Full backup frequency" (Частота полной архивации): "Ежедневная";
- "Full backup start time" (Время начала полной архивации): "22:00";
- "Full backup time window" (Временное окно полной архивации): "6 ч".

Это означает, что следующее доступное окно архивации длительностью в 6 часов наступит в понедельник в 22:00. В это время служба автоматической архивации начнет по очереди архивировать ваши базы данных.

Затем во вторник в 22:00 снова начнется 6-часовая полная архивация всех баз данных.

> [!IMPORTANT]
> При планировании ежедневной архивации рекомендуется выделить достаточное временное окно, чтобы гарантировать архивацию всех баз данных. Это особенно важно в случае, когда архивировать предстоит большой объем данных.

## <a name="configuration-in-the-portal"></a>Настройка на портале
Для настройки автоматической архивации версии 2 при подготовке виртуальных машин SQL Server 2016 или для существующих виртуальных машин SQL Server 2016 можно использовать портал Azure. 

### <a name="new-vms"></a>Новые виртуальные машины
При создании новой виртуальной машины SQL Server 2016 с моделью развертывания с помощью Resource Manager настройте автоматическую архивацию версии 2, используя портал Azure. 

В колонке **Параметры SQL Server** выберите **Автоматизированная архивация**. Колонка **Автоматизированная архивация SQL** показана на следующем снимке экрана портала Azure.

![Настройка автоматической архивации SQL на портале Azure](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> По умолчанию автоматическая архивация версии 2 отключена.

Сведения о контексте приведены в полном описании в статье [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Существующие виртуальные машины
Выберите существующую виртуальную машину SQL Server. Затем в колонке **Параметры** выберите раздел **Конфигурация SQL Server**.

![Автоматизированная архивация SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

В колонке **Конфигурация SQL Server** нажмите кнопку **Изменить** в разделе "Автоматизированная архивация".

![Настройка автоматизированной архивации SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

По завершении нажмите кнопку **ОК** в нижней части колонки **Конфигурация SQL Server**, чтобы сохранить изменения.

Если автоматизированная архивация включается впервые, Azure настроит агент IaaS SQL Server в фоновом режиме. При этом портал Azure может не сообщать о том, что выполняется настройка автоматической архивации. Установка и настройка агента занимают несколько минут. После этого новые параметры отобразятся на портале Azure.

## <a name="configuration-with-powershell"></a>Настройка с помощью PowerShell
Для настройки автоматической архивации версии 2 можно также использовать PowerShell. Предварительно необходимо выполнить следующее.

- [Скачайте и установите последнюю версию Azure PowerShell](http://aka.ms/webpi-azps).
- Откройте компонент Windows PowerShell и свяжите его со своей учетной записью. Это можно сделать, выполнив действия, описанные в посвященном подготовке разделе [Настройка подписки](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription).

### <a name="install-the-sql-iaas-extension"></a>Установка расширения IaaS для SQL Server
Если виртуальная машина SQL Server подготовлена на портале Azure, то на ней уже должно быть установлено расширение IaaS для SQL Server. Выяснить, установлено ли оно на виртуальной машины, можно, выполнив команду **Get-AzureRmVM** и изучив свойство **Extensions**.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Если расширение агента IaaS для SQL Server установлено, вы увидите его в списке как SqlIaaSAgent или SQLIaaSExtension. Значением **ProvisioningState** для расширения также должно быть "Succeeded". 

Если расширение не установлено или его не удалось подготовить, то для его установки можно выполнить приведенную ниже команду. Кроме имени и группы ресурсов виртуальной машины, необходимо указать регион (**$region**), в котором она расположена.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a name="a-idverifysettings-verify-current-settings"></a><a id="verifysettings"> Проверка текущих параметров
Если вы включили автоматическую архивацию во время подготовки, то можете использовать PowerShell для проверки текущей конфигурации. Выполните команду **Get-AzureRmVMSqlServerExtension** и изучите свойство **AutoBackupSettings**.

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Должен отобразиться результат, аналогичный приведенному ниже.

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Если выходные данные показывают, что значение **Enable** равно **False**, то необходимо включить автоматическую архивацию. Хорошая новость состоит в том, что включить и настроить автоматическую архивацию можно точно так же. Этот процесс описан в следующем разделе.

> [!NOTE] 
> Если вы проверяете параметры сразу же после внесения изменений, возможно, вы увидите старые значения конфигурации. Подождите несколько минут и проверьте параметры, чтобы убедиться, что изменения были применены.

### <a name="configure-automated-backup-v2"></a>Настройка автоматической архивации версии 2
Чтобы включить автоматическую архивацию, а также в любое время изменить ее конфигурацию и поведение, можно использовать PowerShell. 

Сначала выберите или создайте учетную запись хранения для файлов резервных копий. Приведенный ниже сценарий выбирает учетную запись хранения или создает ее, если она не существует.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Служба автоматической архивации не поддерживает хранение резервных копий в хранилище уровня "Премиум", но может создавать резервные копии дисков виртуальных машин, которые используют хранилище уровня "Премиум".

Затем выполните команду **New-AzureRmVMSqlServerAutoBackupConfig**, чтобы включить и настроить параметры автоматической архивации версии 2 для хранения резервных копий в учетной записи хранения Azure. В этом примере резервные копии хранятся в течение 10 дней. Архивация системных баз данных включена. Полная архивация выполняется раз в неделю, и для нее выделяется 2-часовое временное окно, начинающееся в 20:00. Резервные копии журналов создаются каждые 30 минут. Вторая команда, **Set-AzureRmVMSqlServerExtension**, обновляет указанную виртуальную машину Azure в соответствии с заданными параметрами.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Установка и настройка агента SQL Server IaaS занимают несколько минут. 

Чтобы включить шифрование, измените предыдущий сценарий таким образом, чтобы он передавал параметр **EnableEncryption** вместе с паролем (защищенной строкой) для параметра **CertificatePassword**. Следующий скрипт активирует параметры автоматической архивации их предыдущего примера и добавляет шифрование.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Чтобы убедиться, что параметры были применены, [проверьте конфигурацию автоматической архивации](#verifysettings).

### <a name="disable-automated-backup"></a>Отключение автоматической архивации
Чтобы отключить автоматическую архивацию, выполните тот же сценарий без параметра **-Enable** в команде **New-AzureRmVMSqlServerAutoBackupConfig**. Отсутствие параметра **-Enable** означает, что функцию нужно отключить. Как и установка, отключение автоматической архивации занимает несколько минут.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Пример сценария
Ниже приведен сценарий, предоставляющий набор переменных, которые можно задать для включения и настройки автоматической архивации для виртуальной машины. Может потребоваться настроить этот сценарий в зависимости от ваших требований. Например, его нужно будет изменить, если вы захотите включить шифрование или отключить архивацию системных баз данных.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Дальнейшие действия
Служба автоматической архивации версии 2 позволяет настроить управляемую архивацию на виртуальных машинах Azure. В связи с этим важно изучить [документацию по управляемой архивации](https://msdn.microsoft.com/library/dn449496.aspx) и понять, как она работает.

Дополнительные сведения об архивации и восстановлении SQL Server на виртуальных машинах Azure см. в статье [Архивация и восстановление SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-backup-recovery.md).

Сведения о других доступных задачах автоматизации см. в разделе [Расширение агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в [обзоре использования SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).


