---
title: "Автоматическая архивация для виртуальных машин SQL Server 2014 в Azure | Документация Майкрософт"
description: "Описывает функцию автоматической архивации для виртуальных машин SQL Server 2014 в Azure. Данная статья относится к виртуальным машинам, использующим модель развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: 91aab896dd5f06c950ee0ed8f36cc6a953d91611
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Автоматическая архивация для виртуальных машин SQL Server 2014 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

Служба автоматической архивации автоматически настраивает [управляемое резервное копирование на портал Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) для всех существующих и новых баз данных на виртуальной машине Azure c SQL Server 2014 Standard или Enterprise. Это позволяет настроить регулярную архивацию базы данных с использованием надежного хранилища больших двоичных объектов Azure. Автоматическая архивация зависит от [Расширения агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Предварительные требования
Для использования автоматической архивации необходимо выполнить следующие предварительные требования.

**Операционная система**

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Версия/выпуск SQL Server**

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Автоматическая архивация работает с SQL Server 2014. Если используется SQL Server 2016, то для архивации баз данных можно применять автоматическую архивацию версии 2. Дополнительную информацию см. в статье [Автоматическая архивация версии 2 для виртуальных машин SQL Server 2016 в Azure](virtual-machines-windows-sql-automated-backup-v2.md).

**Конфигурация базы данных**

- В конечных базах данных должна использоваться модель полного восстановления. Дополнительные сведения о влиянии модели полного восстановления на резервные копии см. в разделе [Резервное копирование в модели полного восстановления](https://technet.microsoft.com/library/ms190217.aspx).
- Конечные базы данных должны находиться в экземпляре SQL Server по умолчанию. Расширение IaaS SQL Server не поддерживает именованные экземпляры.

**Модель развертывания Azure**

- Диспетчер ресурсов

**Azure PowerShell**

- [Установите последнюю версию Azure PowerShell](/powershell/azure/overview) , если планируете настраивать автоматизированную архивацию с помощью PowerShell.

> [!NOTE]
> Автоматическая архивация зависит от расширения агента IaaS для SQL Server. В текущей коллекции образов виртуальных машин SQL это расширение присутствует по умолчанию. Дополнительные сведения см. в статье [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) (Расширение агента SQL Server IaaS).

## <a name="settings"></a>Параметры

В приведенной ниже таблице описаны параметры настройки автоматической архивации. Фактическая процедура настройки может варьироваться в зависимости от того, используете вы портал Azure или команды Azure Windows PowerShell.

| Настройка | Диапазон (по умолчанию) | Описание |
| --- | --- | --- |
| **Автоматическая архивация** | Включено/отключено (отключено) | Включает или отключает автоматическую архивацию для виртуальной машины Azure под управлением SQL Server 2014 Standard или Enterprise. |
| **Срок хранения** | 1–30 дней (30 дней) | Число дней хранения резервной копии. |
| **Учетная запись хранения** | Учетная запись хранения Azure. | Учетная запись хранения Azure для хранения файлов автоматической архивации в хранилище больших двоичных объектов. Там же создается контейнер для хранения всех файлов резервных копий. Имя файла резервной копии содержит дату, время и имя компьютера. |
| **Шифрование** | Включено/отключено (отключено) | Включает или отключает шифрование. Если шифрование включено, то сертификаты, используемые для восстановления резервной копии, сохраняются в указанной учетной записи хранения в том же контейнере `automaticbackup` с использованием того же соглашения об именовании файлов. При изменении пароля создается новый сертификат; при этом старый сертификат сохраняется для восстановления предыдущих резервных копий. |
| **Пароль** | Текст пароля | Пароль для ключей шифрования. Требуется, только если шифрование включено. Для восстановления зашифрованной резервной копии требуется правильный пароль и соответствующий сертификат, который использовался при создании резервной копии. |

## <a name="configuration-in-the-portal"></a>Настройка на портале

Для настройки автоматической архивации при подготовке виртуальных машин SQL Server 2014 или для существующих виртуальных машин SQL Server 2016 можно использовать портал Azure.

### <a name="new-vms"></a>Новые виртуальные машины

При создании новой виртуальной машины SQL Server 2014 с моделью развертывания с помощью Resource Manager настройте автоматическую архивацию, используя портал Azure.

В колонке **Параметры SQL Server** выберите **Автоматизированная архивация**. Колонка **Автоматизированная архивация SQL** показана на следующем снимке экрана портала Azure.

![Настройка автоматической архивации SQL на портале Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Сведения о контексте приведены в полном описании в статье [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Существующие виртуальные машины

Выберите существующую виртуальную машину SQL Server. Затем в колонке **Параметры** выберите раздел **Конфигурация SQL Server**.

![Автоматизированная архивация SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

В колонке **Конфигурация SQL Server** нажмите кнопку **Изменить** в разделе "Автоматизированная архивация".

![Настройка автоматизированной архивации SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

По завершении нажмите кнопку **ОК** в нижней части колонки **Конфигурация SQL Server**, чтобы сохранить изменения.

Если автоматизированная архивация включается впервые, Azure настроит агент IaaS SQL Server в фоновом режиме. При этом портал Azure может не сообщать о том, что выполняется настройка автоматической архивации. Установка и настройка агента занимают несколько минут. После этого новые параметры отобразятся на портале Azure.

> [!NOTE]
> Можно также настроить автоматизированную архивацию с помощью шаблона. Чтобы получить дополнительные сведения, ознакомьтесь с [шаблоном быстрого запуска Azure для автоматизированной архивации](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Настройка с помощью PowerShell

Для настройки автоматической архивации можно также использовать PowerShell. Предварительно необходимо выполнить следующее.

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

### <a id="verifysettings"></a> Проверка текущих параметров

Если вы включили автоматическую архивацию во время подготовки, то можете использовать PowerShell для проверки текущей конфигурации. Выполните команду **Get-AzureRmVMSqlServerExtension** и изучите свойство **AutoBackupSettings**.

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Должен отобразиться результат, аналогичный приведенному ниже.

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Если выходные данные показывают, что значение **Enable** равно **False**, то необходимо включить автоматическую архивацию. Хорошая новость состоит в том, что включить и настроить автоматическую архивацию можно точно так же. Этот процесс описан в следующем разделе.

> [!NOTE] 
> Если вы проверяете параметры сразу же после внесения изменений, возможно, вы увидите старые значения конфигурации. Подождите несколько минут и проверьте параметры, чтобы убедиться, что изменения были применены.

### <a name="configure-automated-backup"></a>Настройка автоматической архивации
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

Затем выполните команду **New-AzureRmVMSqlServerAutoBackupConfig**, чтобы включить и настроить параметры автоматической архивации для хранения архивных копий в учетной записи хранения Azure. В этом примере резервные копии хранятся в течение 10 дней. Вторая команда, **Set-AzureRmVMSqlServerExtension**, обновляет указанную виртуальную машину Azure в соответствии с заданными параметрами.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Установка и настройка агента SQL Server IaaS занимают несколько минут.

> [!NOTE]
> Существуют и другие параметры для **New-AzureRmVMSqlServerAutoBackupConfig**, которые применяются только для SQL Server 2016 и автоматической архивации версии 2. SQL Server 2014 не поддерживает следующие параметры: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** и **LogBackupFrequencyInMinutes**. При попытке настроить эти параметры на виртуальной машине SQL Server 2014 ошибки не возникают, но параметры не применяется. Если вы хотите использовать эти параметры на виртуальной машине SQL Server 2016, см. раздел [Автоматическая архивация версии 2 для виртуальных машин Azure SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md).

Чтобы включить шифрование, измените предыдущий сценарий таким образом, чтобы он передавал параметр **EnableEncryption** вместе с паролем (защищенной строкой) для параметра **CertificatePassword**. Следующий скрипт активирует параметры автоматической архивации их предыдущего примера и добавляет шифрование.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Дальнейшие действия

Автоматическая архивация настраивает управляемое резервное копирование на виртуальных машинах Azure. В связи с этим важно изучить [документацию по управляемой архивации](https://msdn.microsoft.com/library/dn449496.aspx) и понять, как она работает.

Дополнительные сведения об архивации и восстановлении SQL Server на виртуальных машинах Azure см. в статье [Архивация и восстановление SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-backup-recovery.md).

Сведения о других доступных задачах автоматизации см. в разделе [Расширение агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в [обзоре использования SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).

