<properties
	pageTitle="Автоматическая архивация SQL Server на виртуальных машинах (Resource Manager) | Microsoft Azure"
	description="Описание функции автоматической архивации для SQL Server на виртуальных машинах Azure с использованием модели развертывания Resource Manager. "
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/14/2016"
	ms.author="jroth" />

# Автоматическая архивация SQL Server на виртуальных машинах Azure (Resource Manager)

> [AZURE.SELECTOR]
- [Диспетчер ресурсов](virtual-machines-windows-sql-automated-backup.md)
- [Классический](virtual-machines-windows-classic-sql-automated-backup.md)

Служба автоматической архивации автоматически настраивает [управляемое резервное копирование на портал Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) для всех существующих и новых баз данных на виртуальной машине Azure c SQL Server 2014 Standard или Enterprise. Это позволяет настроить регулярную архивацию базы данных с использованием надежного хранилища больших двоичных объектов Azure. Автоматическая архивация зависит от [Расширения агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] Классическая модель развертывания. Версию этой статьи для классической модели развертывания см. в разделе [Автоматическая архивация SQL Server на виртуальных машинах Azure классическая модель](virtual-machines-windows-classic-sql-automated-backup.md).

## Предварительные требования

Для использования автоматической архивации необходимо выполнить следующие предварительные требования.

**Операционная система**

- Windows Server 2012
- Windows Server 2012 R2

**Версия/выпуск SQL Server**

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

**Конфигурация базы данных**:

- Целевые базы данных должны использовать полную модель восстановления.

**Azure PowerShell**:

- [Установите последнюю версию Azure PowerShell](../powershell-install-configure.md), если планируете настраивать автоматизированную архивацию с помощью PowerShell.

>[AZURE.NOTE] Автоматическая архивация зависит от расширения агента IaaS для SQL Server. В текущей коллекции образов виртуальных машин SQL это расширение присутствует по умолчанию. Дополнительные сведения см. в статье [Расширение агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## Параметры

В приведенной ниже таблице описаны параметры настройки автоматической архивации. Фактическая процедура настройки может варьироваться в зависимости от того, используете вы портал Azure или команды Azure Windows PowerShell.

|Настройка|Диапазон (по умолчанию)|Описание|
|---|---|---|
|**Автоматическая архивация**|Включено/отключено (отключено)|Включает или отключает автоматическую архивацию для виртуальной машины Azure под управлением SQL Server 2014 Standard или Enterprise.|
|**Срок хранения**|1–30 дней (30 дней)|Число дней хранения резервной копии.|
|**Учетная запись хранения**|Учетная запись хранения Azure (учетная запись хранения, созданная для указанной виртуальной машины)|Учетная запись хранения Azure для хранения файлов автоматической архивации в хранилище больших двоичных объектов. Там же создается контейнер для хранения всех файлов резервных копий. Имя файла резервной копии содержит дату, время и имя компьютера.|
|**Шифрование**|Включено/отключено (отключено)|Включает или отключает шифрование. Если шифрование включено, сертификаты, используемые для восстановления резервной копии, сохраняются в указанной учетной записи хранения в том же контейнере automaticbackup с использованием того же принципа именования файлов. При изменении пароля создается новый сертификат; при этом старый сертификат сохраняется для восстановления предыдущих резервных копий.|
|**Пароль**|Текст пароля (нет)|Пароль для ключей шифрования. Требуется, только если шифрование включено. Для восстановления зашифрованной резервной копии требуется правильный пароль и соответствующий сертификат, который использовался при создании резервной копии.|

## Настройка на портале
Для настройки автоматизированной архивации во время подготовки виртуальных машин или для существующих виртуальных машин можно использовать портал Azure.

### Новые виртуальные машины
При создании новой виртуальной машины SQL Server 2014 с моделью развертывания с помощью Resource Manager настройте автоматизированную архивацию, используя портал Azure.

В колонке **Параметры SQL Server** выберите **Автоматизированная архивация**. Колонка **Автоматизированная архивация SQL** показана на следующем снимке экрана портала Azure.

![Настройка автоматической архивации SQL на портале Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

За контекстом обратитесь к полному описанию в разделе [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-windows-portal-sql-server-provision.md).

### Существующие виртуальные машины
Выберите существующую виртуальную машину SQL Server. Затем в колонке **Параметры** выберите раздел **Конфигурация SQL Server**.

![Автоматизированная архивация SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

В колонке **Конфигурация SQL Server** нажмите кнопку **Изменить** в разделе "Автоматизированная архивация".

![Настройка автоматизированной архивации SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

По завершении нажмите кнопку **ОК** в нижней части колонки **Конфигурация SQL Server**, чтобы сохранить изменения.

Если автоматизированная архивация включается впервые, Azure настроит агент IaaS SQL Server в фоновом режиме. При этом портал Azure может не сообщать о том, что выполняется настройка автоматической архивации. Установка и настройка агента занимают несколько минут. После этого новые параметры отобразятся на портале Azure.

>[AZURE.NOTE] Можно также настроить автоматизированную архивацию с помощью шаблона. Чтобы получить дополнительные сведения, ознакомьтесь с [шаблоном быстрого запуска Azure для автоматизированной архивации](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## Настройка с помощью PowerShell

После подготовки виртуальной машины SQL используйте PowerShell для настройки автоматической архивации.

В следующем примере с использованием PowerShell автоматическая архивация настраивается для существующей виртуальной машины SQL Server 2014. Команда **AzureRM.Compute\\New-AzureVMSqlServerAutoBackupConfig** настраивает параметры автоматизированной архивации для хранения архивов в учетной записи хранения Azure, связанной с виртуальной машиной. Эти резервные копии будут храниться в течение 10 дней. Команда **Set-AzureRmVMSqlServerExtension** обновляет указанную виртуальную машину Azure в соответствии с заданными параметрами.

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Установка и настройка агента SQL Server IaaS занимают несколько минут.

Чтобы включить шифрование, измените предыдущий скрипт таким образом, чтобы он передавал параметр **EnableEncryption** вместе с паролем (защищенной строкой) для параметра **CertificatePassword**. Следующий скрипт активирует параметры автоматической архивации их предыдущего примера и добавляет шифрование.

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
	$password = "P@ssw0rd"
	$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
	$autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

	Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Чтобы отключить автоматизированную архивацию, выполните тот же сценарий без параметра **-Enable** команды **AzureRM.Compute\\New-AzureVMSqlServerAutoBackupConfig**. Отсутствие параметра **-Enable** означает, что функцию нужно отключить. Как и установка, отключение автоматической архивации занимает несколько минут.

>[AZURE.NOTE] При удалении агента IaaS для SQL Server заданные ранее параметры автоматической архивации не удаляются. Перед отключением и удалением агента SQL Server IaaS необходимо отключить автоматическую архивацию.

## Дальнейшие действия

Автоматическая архивация настраивает управляемое резервное копирование на виртуальных машинах Azure. В связи с этим важно изучить [документацию по управляемой архивации](https://msdn.microsoft.com/library/dn449496.aspx) и понять, как она работает.

Дополнительные сведения об архивации и восстановлении SQL Server на виртуальных машинах Azure см. в разделе [Резервное копирование и восстановление SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-backup-recovery.md).

Сведения о других доступных задачах автоматизации см. в разделе [Расширение агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в [обзоре использования SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!-----HONumber=AcomDC_0720_2016--->
