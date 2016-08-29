.<properties
    pageTitle="Архивация базы данных SQL Azure в BACPAC-файл с помощью PowerShell"
    description="Архивация базы данных SQL Azure в BACPAC-файл с помощью PowerShell"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/15/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Архивация базы данных SQL Azure в BACPAC-файл с помощью PowerShell

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


В статье содержатся инструкции по архивации базы данных SQL Azure в [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файл, хранимый в хранилище BLOB-объектов Azure, с помощью PowerShell.

Когда вам нужно создать архив базы данных SQL Azure, вы можете экспортировать схему базы данных и данные в BACPAC-файл. BACPAC-файл — это просто ZIP-файл с расширением BACPAC. BACPAC-файл можно позже сохранить в хранилище BLOB-объектов или локальном хранилище в локальном расположении. Его можно также импортировать обратно в базу данных SQL Azure или локальный экземпляр SQL Server.

**Рекомендации**

- Чтобы архив был транзакционно согласованным, необходимо обеспечить отсутствие операций записи во время экспорта или экспортировать данные из [транзакционно согласованной копии](sql-database-copy.md) базы данных SQL Azure.
- Максимальный размер BACPAC-файла архива в хранилище BLOB-объектов Azure составляет 200 ГБ. Для архивации BACPAC-файла большего размера в локальное хранилище используйте программу командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Эта служебная программа поставляется вместе с Visual Studio и SQL Server. Кроме того, вы можете [скачать](https://msdn.microsoft.com/library/mt204009.aspx) последнюю версию SQL Server Data Tools для получения этой служебной программы.
- Архивация в хранилище Azure уровня "Премиум" с помощью BACPAC-файла не поддерживается.
- Если операция экспорта длится более 20 часов, она может быть отменена. Для повышения производительности во время экспорта можно сделать следующее.
 - Временно повысить уровень служб.
 - Прекратить все операции чтения и записи во время экспорта.
 - Используйте для всех больших таблиц [кластеризованный индекс](https://msdn.microsoft.com/library/ms190457.aspx) со значениями, отличными от NULL. Без кластеризованных индексов экспорт может завершиться ошибкой, если он длится больше 6–12 часов. Это обусловлено тем, что службам экспорта требуется выполнить проверку таблицы, чтобы экспортировать всю таблицу. Хороший способ определить, оптимизированы ли таблицы для экспорта, — выполнить **DBCC SHOW\_STATISTICS** и убедиться, что значение *RANGE\_HI\_KEY* не равно NULL и имеет хорошее распределение. Дополнительную информацию см. в разделе [DBCC SHOW\_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [AZURE.NOTE] BACPAC-файлы не предназначены для операций службы архивации и восстановления. База данных SQL Azure автоматически создает резервные копии для каждой пользовательской базы данных. Дополнительные сведения см. в статье [Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL Azure](sql-database-automated-backups.md).

Для работы с этой статьей необходимо следующее:

- Подписка Azure.
- База данных SQL Azure.
- [Учетная запись хранения Azure уровня "Стандартный"](../storage/storage-create-storage-account.md) с контейнером больших двоичных объектов для хранения BACPAC-файла в хранилище уровня "Стандартный".


[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]




## Экспорт базы данных

Командлет [New AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx) отправляет запрос на экспорт базы данных к службе. Операция экспорта может занять некоторое время в зависимости от размера базы данных.

> [AZURE.IMPORTANT] Для получения транзакционно согласованного BACPAC-файла сначала [создайте копию базы данных](sql-database-copy-powershell.md), а затем экспортируйте эту копию.


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## Отслеживание хода выполнения операции экспорта

Состояние запроса после выполнения **New-AzureRmSqlDatabaseExport** можно проверить, выполнив командлет [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx). Если выполнить его немедленно после запроса, то обычно возвращается сообщение **Состояние: выполняется**. Отображение сообщения **Состояние: выполнен** означает, что экспорт завершен.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## Пример экспорта базы данных SQL

В следующем примере существующая база данных SQL экспортируется в BACPAC-файл. В нем также демонстрируется, как проверить состояние операции экспорта.

Чтобы запустить пример, значения некоторых переменных необходимо заменить значениями, соответствующими вашей базе данных и учетной записи хранения. На [портале Azure](https://portal.azure.com) перейдите к своей учетной записи хранения, чтобы получить имя учетной записи хранения, имя контейнера больших двоичных объектов и значение ключа. Чтобы отобразить ключ, щелкните **Ключи доступа** в колонке учетной записи хранения.

Замените следующие значения `VARIABLE-VALUES` значениями для своих ресурсов Azure. В качестве имени базы данных следует указать существующую базу данных, которую требуется экспортировать.



    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## Дальнейшие действия

- Чтобы узнать, как импортировать базу данных Azure SQL с помощью PowerShell, ознакомьтесь с разделом [Импорт BACPAC-файла для создания новой базы данных SQL Azure с помощью PowerShell](sql-database-import-powershell.md).


## Дополнительные ресурсы

- [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx)
- [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx)

<!---HONumber=AcomDC_0817_2016-->