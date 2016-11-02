<properties
    pageTitle="Автоматическая архивация SQL Server на виртуальных машинах Azure (классическая модель) | Microsoft Azure"
    description="Описание функции автоматической архивации для SQL Server на виртуальных машинах Azure с использованием модели развертывания Resource Manager. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth" />


# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-(classic)"></a>Автоматическая архивация SQL Server на виртуальных машинах Azure (классическая модель)

> [AZURE.SELECTOR]
- [Диспетчер ресурсов](virtual-machines-windows-sql-automated-backup.md)
- [Классический](virtual-machines-windows-classic-sql-automated-backup.md)

Служба автоматической архивации автоматически настраивает [управляемое резервное копирование на портал Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) для всех существующих и новых баз данных на виртуальной машине Azure c SQL Server 2014 Standard или Enterprise. Это позволяет настроить регулярную архивацию базы данных с использованием надежного хранилища больших двоичных объектов Azure. Автоматическая архивация зависит от [Расширения агента IaaS для SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Версию этой статьи для Resource Manager см. в разделе [Автоматическая архивация SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Предварительные требования

Для использования автоматической архивации необходимо выполнить следующие предварительные требования.

**Операционная система**

- Windows Server 2012
- Windows Server 2012 R2

**Версия/выпуск SQL Server**

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

>[AZURE.NOTE] Автоматическая архивация пока не поддерживает SQL Server 2016.

**Конфигурация базы данных**

- В конечных базах данных должна использоваться модель полного восстановления.

**Azure PowerShell**

- [Установите последнюю версию команд Azure PowerShell](../powershell-install-configure.md).

**Расширение IaaS для SQL Server**

- [Установите расширение IaaS для SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Параметры

В приведенной ниже таблице описаны параметры настройки автоматической архивации. Для виртуальных машин, развернутых с использованием классической модели, эти параметры необходимо настроить с помощью PowerShell.

|Настройка|Диапазон (по умолчанию)|Описание|
|---|---|---|
|**Автоматическая архивация**|Включено/отключено (отключено)|Включает или отключает автоматическую архивацию для виртуальной машины Azure под управлением SQL Server 2014 Standard или Enterprise.|
|**Срок хранения**|1–30 дней (30 дней)|Число дней хранения резервной копии.|
|**Учетная запись хранения**|Учетная запись хранения Azure (учетная запись хранения, созданная для указанной виртуальной машины)|Учетная запись хранения Azure для хранения файлов автоматической архивации в хранилище больших двоичных объектов. Там же создается контейнер для хранения всех файлов резервных копий. Имя файла резервной копии содержит дату, время и имя компьютера.|
|**Шифрование**|Включено/отключено (отключено)|Включает или отключает шифрование. Если шифрование включено, сертификаты, используемые для восстановления резервной копии, сохраняются в указанной учетной записи хранения в том же контейнере automaticbackup с использованием того же принципа именования файлов. При изменении пароля создается новый сертификат; при этом старый сертификат сохраняется для восстановления предыдущих резервных копий.|
|**Пароль**|Текст пароля (нет)|Пароль для ключей шифрования. Требуется, только если шифрование включено. Для восстановления зашифрованной резервной копии требуется правильный пароль и соответствующий сертификат, который использовался при создании резервной копии.|

## <a name="configuration-with-powershell"></a>Настройка с помощью PowerShell

В следующем примере с использованием PowerShell автоматическая архивация настраивается для существующей виртуальной машины SQL Server 2014. Команда **AzureVMSqlServerAutoBackupConfig** настраивает параметры автоматической архивации для хранения резервных копий в учетной записи хранения Azure, указанной в переменной $storageaccount. Эти резервные копии будут храниться в течение 10 дней. Команда **AzureVMSqlServerExtension** обновляет указанную виртуальную машину Azure в соответствии с заданными параметрами.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Установка и настройка агента SQL Server IaaS занимают несколько минут.

Чтобы включить шифрование, измените предыдущий скрипт таким образом, чтобы он передавал параметр EnableEncryption вместе с паролем (защищенной строкой) для параметра CertificatePassword. Следующий скрипт активирует параметры автоматической архивации их предыдущего примера и добавляет шифрование.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Чтобы отключить автоматическую архивацию, выполните тот же сценарий без параметра **-Enable** в командлете **New-AzureVMSqlServerAutoBackupConfig**. Как и установка, отключение автоматической архивации занимает несколько минут.

>[AZURE.NOTE] При отключении и удалении агента SQL Server IaaS не удаляются настроенные ранее параметры управляемого резервного копирования. Перед отключением и удалением агента SQL Server IaaS необходимо отключить автоматическую архивацию.

## <a name="next-steps"></a>Дальнейшие действия

Автоматическая архивация настраивает управляемое резервное копирование на виртуальных машинах Azure. В связи с этим важно изучить [документацию по управляемой архивации](https://msdn.microsoft.com/library/dn449496.aspx) и понять, как она работает.

Дополнительные сведения об архивации и восстановлении SQL Server на виртуальных машинах Azure см. в статье [Архивация и восстановление SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-backup-recovery.md).

Сведения о других доступных задачах автоматизации см. в разделе [Расширение агента IaaS для SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в [обзоре использования SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).



<!--HONumber=Oct16_HO2-->


