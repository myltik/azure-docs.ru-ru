<properties
	pageTitle="Использование PowerShell для резервного копирования и восстановления приложений службы приложений"
	description="Узнайте, как использовать PowerShell для создания и восстановления резервных копий приложения в службе приложений Azure."
	services="app-service"
	documentationCenter=""
	authors="NKing92"
	manager="wpickett"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="nicking"/>
# Использование PowerShell для резервного копирования и восстановления приложений службы приложений

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [ИНТЕРФЕЙС REST API](../app-service-web/websites-csm-backup.md)

Узнайте, как использовать Azure PowerShell для резервного копирования и восстановления [приложений службы приложений](https://azure.microsoft.com/services/app-service/web/). Дополнительные сведения о резервных копиях веб-приложений, включая требования и ограничения, см. в статье [Резервное копирование веб-приложений в службе приложений Azure](../app-service-web/web-sites-backup.md).

## Предварительные требования
Чтобы использовать PowerShell для управления резервными копиями приложения, требуется следующее.

- **URL-адрес SAS**, предоставляющий разрешения на чтение и запись в контейнер службы хранилища Azure. Описание URL-адресов SAS см. в статье [Подписанные URL-адреса. Часть 1: общие сведения о модели SAS](../storage/storage-dotnet-shared-access-signature-part-1.md). С примерами управления службой хранилища Azure с помощью PowerShell можно ознакомиться в разделе [Использование Azure PowerShell со службой хранилища Azure](../storage/storage-powershell-guide-full.md).
- **Строка подключения к базе данных**, если вместе с веб-приложением вы хотите создать резервную копию базы данных.

### Как создать подписанный URL-адрес SAS для использования с командлетами архивации веб-приложений
Подписанный URL-адрес можно создать с помощью PowerShell. Ниже приведен пример того, как создать подписанный URL-адрес, который может использоваться с командлетами, рассмотренными в этой статье.

		$storageAccountName = "<your storage account's name>"
		$storageAccountRg = "<your storage account's resource group>"

		# This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
		$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

		$blobContainerName = "<name of blob container for app backups>"
		$sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## Установка Azure PowerShell 1.3.2 или более поздней версии

Инструкции по установке и использованию Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Создание резервной копии

Выполните командлет New-AzureRmWebAppBackup для создания резервной копии веб-приложения.

		$sasUrl = "<your SAS URL>"
		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Он создает резервную копию, имя которой присваивается автоматически. Если вы хотите самостоятельно указать имя резервной копии, используйте дополнительный параметр BackupName.

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Чтобы включить базу данных в резервную копию, сначала создайте настройки для архивации базы данных. Для этого выполните командлет New-AzureRmWebAppDatabaseBackupSetting, а затем укажите полученное значение в параметре Database командлета New-AzureRmWebAppBackup. Параметр Database принимает массив настроек базы данных, то есть позволяет архивировать сразу несколько баз данных.

		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## Получение резервных копий

Командлет Get-AzureRmWebAppBackupList возвращает массив всех резервных копий для веб-приложения. Этому командлету следует передать имя веб-приложения и его группу ресурсов.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Для получения определенной резервной копии используйте командлет Get-AzureRmWebAppBackup.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

Возможно, вам будет удобнее использовать конвейер, чтобы передать объект веб-приложения в любой из командлетов управления резервным копированием.

		$app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
		$backupList = $app | Get-AzureRmWebAppBackupList
		$backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## Планирование автоматического резервного копирования

Вы можете настроить автоматическое резервное копирование через указанные промежутки времени. Чтобы настроить график резервного копирования, используйте командлет Edit-AzureRmWebAppBackupConfiguration. Этот командлет принимает несколько параметров.

- **Name** — имя веб-приложения.
- **ResourceGroupName** — имя группы ресурсов, которая содержит веб-приложение.
- **Slot** — необязательный параметр. Имя слота веб-приложения.
- **StorageAccountUrl** — подписанный URL-адрес SAS контейнера службы хранилища Azure, в котором будет храниться резервная копия.
- **FrequencyInterval** — числовое значение, которое определяет частоту создания резервных копий. Принимаются только положительные целые числа.
- **FrequencyUnit** — задает единицы времени, в которых выражена частота архивации. Допустимые варианты: Hour (часы) и Day (дни).
- **RetentionPeriodInDays** — количество дней хранения создаваемых автоматически резервных копий. По завершении этого периода резервные копии автоматически удаляются.
- **StartTime** — необязательный параметр. Время начала автоматической архивации. Если это поле имеет значение NULL, то архивация начинается немедленно. Принимаются значения в формате DateTime.
- **Databases** — необязательный параметр. Массив настроек в формате DatabaseBackupSettings, которые описывают базы данных для резервного копирования.
- **KeepAtLeastOneBackup** — необязательный параметр. Его следует указать, если вы хотите всегда хранить в учетной записи хранения одну резервную копию независимо от срока ее создания.

Ниже приведен пример использования этого командлета.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
		  -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
		  -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Чтобы получить текущий график резервного копирования, используйте командлет Get-AzureRmWebAppBackupConfiguration. Это может быть полезно для изменения уже настроенного графика.

		$configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

		# Modify the configuration slightly
		$configuration.FrequencyInterval = 2
		$configuration.FrequencyUnit = "Day"

		# Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
		$configuration | Edit-AzureRmWebAppBackupConfiguration

## Восстановление веб-приложения из резервной копии

Чтобы восстановить веб-приложение из резервной копии, используйте командлет Restore-AzureRmWebAppBackup. Для упрощения процесса вы можете передать этому командлету объект резервной копии, полученный из командлета Get-AzureRmWebAppBackup или Get-AzureRmWebAppBackupList.

Созданный объект резервной копии можно передать в командлет Restore-AzureRmWebAppBackup с использованием конвейера. Добавьте параметр Overwrite, который указывает, что вы хотите заменить содержимое веб-приложения содержимым резервной копии. Если резервная копия содержит базы данных, то эти они также будут восстановлены.

		$backup | Restore-AzureRmWebAppBackup -Overwrite

Ниже приведен пример использования командлета Restore-AzureRmWebAppBackup с указанием всех параметров.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$blobName = "ContosoBackup.zip"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## удаление резервной копии;

Чтобы удалить резервную копию, используйте командлет Remove-AzureRmWebAppBackup. Резервная копия будет удалена из вашей учетной записи хранения. Укажите имя приложения, его группу ресурсов и идентификатор резервной копии, которую требуется удалить.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

Также вы можете передать в командлет Remove-AzureRmWebAppBackup через конвейер объект резервной копии, которую желаете удалить.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
		$backup | Remove-AzureRmWebAppBackup -Overwrite

<!---HONumber=AcomDC_0921_2016-->