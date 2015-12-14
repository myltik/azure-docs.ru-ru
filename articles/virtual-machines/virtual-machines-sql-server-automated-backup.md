<properties
	pageTitle="Автоматическая архивация для виртуальных машин SQL Server | Microsoft Azure"
	description="Описание функции автоматической архивации для SQL Server на виртуальных машинах Azure с использованием модели развертывания диспетчера ресурсов."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/12/2015"
	ms.author="jroth" />

# Автоматическая архивация SQL Server на виртуальных машинах Azure

Служба автоматической архивации автоматически настраивает [управляемое резервное копирование на портал Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) для всех существующих и новых баз данных на виртуальной машине Azure c SQL Server 2014 Standard или Enterprise. Это позволяет настроить регулярную архивацию базы данных с использованием надежного хранилища больших двоичных объектов Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.

## Параметры автоматической архивации

В приведенной ниже таблице описаны параметры настройки автоматической архивации. Фактическая процедура настройки может варьироваться в зависимости от того, используете вы портал Azure или команды Azure Windows PowerShell.

|Настройка|Диапазон (по умолчанию)|Описание|
|---|---|---|
|**Автоматическая архивация**|Включено/отключено (отключено)|Включает или отключает автоматическую архивацию для виртуальной машины Azure под управлением SQL Server 2014 Standard или Enterprise.|
|**Срок хранения**|1–30 дней (30 дней)|Число дней хранения резервной копии.|
|**Учетная запись хранения**|Учетная запись хранения Azure (учетная запись хранения, созданная для указанной виртуальной машины)|Учетная запись хранения Azure для хранения файлов автоматической архивации в хранилище больших двоичных объектов. Там же создается контейнер для хранения всех файлов резервных копий. Имя файла резервной копии содержит дату, время и имя компьютера.|
|**Шифрование**|Включено/отключено (отключено)|Включает или отключает шифрование. Если шифрование включено, сертификаты, используемые для восстановления резервной копии, сохраняются в указанной учетной записи хранения в том же контейнере automaticbackup с использованием того же принципа именования файлов. При изменении пароля создается новый сертификат; при этом старый сертификат сохраняется для восстановления предыдущих резервных копий.|
|**Пароль**|Текст пароля (нет)|Пароль для ключей шифрования. Требуется, только если шифрование включено. Для восстановления зашифрованной резервной копии требуется правильный пароль и соответствующий сертификат, который использовался при создании резервной копии.|

## Настройка автоматической архивации на портале Azure

При создании новой виртуальной машины SQL Server 2014 можно настроить автоматическую архивацию с помощью портала Azure.

>[AZURE.NOTE]Для автоматической архивации используется агент SQL Server IaaS. Для установки и настройки агента на конечной виртуальной машине должен быть установлен агент виртуальных машин Azure. На более новых виртуальных машинах этот параметр по умолчанию включен, но если машина используется давно, агент виртуальных машин Azure может отсутствовать. Если вы используете собственный образ виртуальной машины, установите также агент SQL Server IaaS. Дополнительные сведения см. в статье [Агент виртуальных машин и расширения](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

На снимке экрана портала Azure ниже данные параметры отображаются в разделе **ДОПОЛНИТЕЛЬНАЯ НАСТРОЙКА** | **АВТОМАТИЧЕСКАЯ АРХИВАЦИЯ SQL**.

![Настройка автоматической архивации SQL на портале Azure](./media/virtual-machines-sql-server-automated-backup/IC778483.jpg)

Для уже существующих виртуальных машин SQL Server 2014 выберите параметры **автоматической архивации** в разделе **Конфигурация** свойств виртуальной машины. В окне **Автоматическая архивация** можно включить эту функцию, указать срок хранения, выбрать учетную запись хранения и настроить шифрование. Это показано на следующем снимке экрана.

![Настройка автоматической архивации на портале Azure](./media/virtual-machines-sql-server-automated-backup/IC792133.jpg)

>[AZURE.NOTE]Когда автоматическая архивация активируется впервые, Azure настраивает агент SQL Server IaaS в фоновом режиме. При этом портал Azure не сообщает о том, что выполняется настройка автоматической архивации. Установка и настройка агента занимают несколько минут. После этого новые параметры отобразятся на портале Azure.

## Настройка автоматической архивации с помощью PowerShell

В следующем примере с использованием PowerShell автоматическая архивация настраивается для существующей виртуальной машины SQL Server 2014. Командлет **New-AzureVMSqlServerAutoBackupConfig** настраивает параметры автоматической архивации для хранения резервных копий в учетной записи хранения Azure, указанной в переменной $storageaccount. Эти резервные копии будут храниться в течение 10 дней. Командлет **Set-AzureVMSqlServerExtension** обновляет указанную виртуальную машину Azure в соответствии с заданными параметрами.

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

## Отключение и удаление агента SQL Server IaaS

Чтобы отключить агент SQL Server IaaS для автоматической архивации и установки исправлений, используйте следующую команду:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Чтобы удалить агент SQL Server IaaS, используйте следующий синтаксис:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

Кроме того, удалить расширение можно с помощью команды **Remove-AzureVMSqlServerExtension**:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

>[AZURE.NOTE]При отключении и удалении агента SQL Server IaaS не удаляются настроенные ранее параметры управляемого резервного копирования. Перед отключением и удалением агента SQL Server IaaS необходимо отключить автоматическую архивацию.

## Совместимость

Следующие продукты совместимы с функциями автоматической архивации агента SQL Server IaaS:

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2014 Standard

- SQL Server 2014 Enterprise

## Дальнейшие действия

Автоматическая архивация настраивает управляемое резервное копирование на виртуальных машинах Azure. В связи с этим важно изучить [документацию по управляемой архивации](https://msdn.microsoft.com/library/dn449496.aspx) и понять, как она работает.

Дополнительные сведения об архивации и восстановлении SQL Server на виртуальных машинах Azure см. в разделе [Резервное копирование и восстановление SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-backup-and-restore.md).

Для виртуальных машин SQL Server в Azure существует родственная функция — [автоматическая установка исправлений для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-automated-patching.md).

Просмотрите и другие [ресурсы по запуску SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_1203_2015-->