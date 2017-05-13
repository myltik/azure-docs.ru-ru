---
title: "Экспорт базы данных SQL Azure в BACPAC-файл | Документация Майкрософт"
description: "Экспорт базы данных SQL Azure в BACPAC-файл с помощью портала Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/05/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 06183c5e1b61c47a7229c2abcc64217ee57c2bac
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Экспорт базы данных SQL Azure в BACPAC-файл

В этой статье рассматривается экспорт базы данных SQL Azure в [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файл. В этой статье рассматривается использование следующих методов:
- [портал Azure](https://portal.azure.com).
- программа командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx);
- командлет [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport);
- мастер [экспорта приложения уровня данных](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) в [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).

> [!IMPORTANT] 
> В настоящий момент служба Azure SQL Database Automated Export находится на этапе предварительной версии, и ее использование будет прекращено 1 марта 2017 года. Начиная с 1 декабря 2016 года будет невозможно настроить автоматический экспорт для какой-либо базы данных SQL. Все существующие задания автоматического экспорта продолжат работать до 1 марта 2017 года. После 1 декабря 2016 года можно использовать [долгосрочное хранение резервных копий](sql-database-long-term-retention.md
) или [службу автоматизации Azure](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md), чтобы периодически архивировать базы данных SQL с помощью PowerShell согласно выбранному расписанию. Пример сценария PowerShell можно скачать из [репозитория GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export).
>

> [!IMPORTANT] 
> В настоящий момент служба Azure SQL Database Automated Export находится на этапе предварительной версии, и ее использование будет прекращено 1 марта 2017 года. Начиная с 1 декабря 2016 года будет невозможно настроить автоматический экспорт для какой-либо базы данных SQL. Все существующие задания автоматического экспорта продолжат работать до 1 марта 2017 года. После 1 декабря 2016 года можно использовать [долгосрочное хранение резервных копий](sql-database-long-term-retention.md
) или [службу автоматизации Azure](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md), чтобы периодически архивировать базы данных SQL с помощью PowerShell согласно выбранному расписанию. Пример сценария PowerShell можно скачать из [репозитория GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export).
>

## <a name="overview"></a>Обзор

Если нужно экспортировать базу данных для создания архива или для перехода на другую платформу, можно экспортировать схему и данные базы данных в BACPAC-файл. BACPAC-файл — это ZIP-файл с расширением BACPAC, содержащий метаданные и данные из базы данных SQL Server. BACPAC-файл можно сохранить в хранилище BLOB-объектов Azure или локальном хранилище, а затем импортировать обратно в базу данных SQL Azure или локальный экземпляр SQL Server. 

> [!IMPORTANT]
> Если вы экспортируете данные из SQL Server, чтобы потом перенести их в базу данных SQL Azure, см. в статью [Migrate a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md) (Миграция базы данных SQL Server в базу данных SQL).
> 

## <a name="considerations"></a>Рекомендации

* Чтобы экспорт был транзакционно согласованным, необходимо обеспечить отсутствие операций записи во время экспорта или экспортировать данные из [транзакционно согласованной копии](sql-database-copy.md) базы данных SQL Azure.
* Максимальный размер BACPAC-файла при экспорте в хранилище BLOB-объектов составляет 200 ГБ. Для архивации BACPAC-файла большего размера выполняйте экспорт в локальное хранилище.
* Экспорт BACPAC-файла в службу хранилища Azure уровня "Премиум" с использованием методов, описанных в этой статье, не поддерживается.
* Если операция экспорта из базы данных SQL Azure длится более 20 часов, ее можно отменить. Для повышения производительности во время экспорта можно сделать следующее.
  * Временно повысить уровень служб.
  * Прекратить все операции чтения и записи во время экспорта.
  * Используйте для всех больших таблиц [кластеризованный индекс](https://msdn.microsoft.com/library/ms190457.aspx) со значениями, отличными от NULL. Без кластеризованных индексов экспорт может завершиться ошибкой, если он длится больше 6–12 часов. Это обусловлено тем, что службам экспорта требуется выполнить проверку таблицы, чтобы экспортировать всю таблицу. Хороший способ определить, оптимизированы ли таблицы для экспорта, — выполнить **DBCC SHOW_STATISTICS** и убедиться, что значение *RANGE_HI_KEY* не равно NULL и имеет хорошее распределение. Дополнительную информацию см. в разделе [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPAC-файлы не предназначены для операций службы архивации и восстановления. База данных SQL Azure автоматически создает резервные копии для каждой пользовательской базы данных. Дополнительные сведения см. в статьях [Обзор обеспечения непрерывности бизнес-процессов с помощью базы данных SQL Azure](sql-database-business-continuity.md) и[Подробнее о резервном копировании базы данных SQL](sql-database-automated-backups.md).  
> 

## <a name="azure-portal"></a>Портал Azure

Чтобы экспортировать базу данных с помощью портала Azure, откройте страницу для базы данных и щелкните **Экспорт** на панели инструментов. Введите имя BACPAC-файла, укажите учетную запись хранения Azure и контейнер для экспорта, а также учетные данные для подключения к базе данных-источнику.  

   ![Экспорт базы данных](./media/sql-database-export/database-export.png)

Чтобы отслеживать ход выполнения операции экспорта, откройте страницу логического сервера, содержащего экспортируемую базу данных. Прокрутите ее вниз до раздела **Операции** и щелкните журнал **Импорт и экспорт**.

## <a name="sqlpackage-utility"></a>Служебная программа SQLPackage

Экспорт базы данных SQL с помощью программы командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) описывается в разделе о [параметрах и свойствах операции экспорта](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). Служебная программа SqlPackage поставляется вместе с последними версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.

Рекомендуется использовать служебную программу SqlPackage для масштабирования и обеспечения производительности в большинстве рабочих сред. Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

В этом примере показано, как экспортировать базу данных с помощью SqlPackage.exe, используя универсальную аутентификацию Active Directory.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio"></a>SQL Server Management Studio

Самые последние версии SQL Server Management Studio также содержат мастер для экспорта базы данных SQL Azure в BACPAC-файл. Ознакомьтесь с разделом [Export a Data-tier Application](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) (Экспорт приложения уровня данных).

## <a name="powershell"></a>PowerShell

Используйте командлет [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport), чтобы отправить запрос на экспорт базы данных к службе Базы данных SQL Azure. Операция экспорта может занять некоторое время в зависимости от размера базы данных.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Чтобы проверить состояние запроса на экспорт, используйте командлет [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). Если выполнить его немедленно после запроса, то обычно возвращается сообщение **Состояние: выполняется**. Отображение сообщения **Состояние: выполнен** означает, что экспорт завершен.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

### <a name="export-sql-database-example"></a>Пример экспорта базы данных SQL
В следующем примере существующая база данных SQL Azure экспортируется в BACPAC-файл. В нем также демонстрируется, как проверить состояние операции экспорта.

Чтобы запустить пример, значения некоторых переменных необходимо заменить значениями, соответствующими вашей базе данных и учетной записи хранения. На [портале Azure](https://portal.azure.com)перейдите к своей учетной записи хранения, чтобы получить имя учетной записи хранения, имя контейнера больших двоичных объектов и значение ключа. Чтобы отобразить ключ, щелкните **Ключи доступа** в колонке учетной записи хранения.

Замените следующие значения `VARIABLE-VALUES` значениями для своих ресурсов Azure. В качестве имени базы данных следует указать существующую базу данных, которую требуется экспортировать.

```powershell
$subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId

# Database to export
$DatabaseName = "DATABASE-NAME"
$ResourceGroupName = "RESOURCE-GROUP-NAME"
$ServerName = "SERVER-NAME
$serverAdmin = "ADMIN-NAME"
$serverPassword = "ADMIN-PASSWORD" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

# Generate a unique filename for the BACPAC
$bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

# Storage account info for the BACPAC
$BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
$BacpacUri = $BaseStorageUri + $bacpacFilename
$StorageKeytype = "StorageAccessKey"
$StorageKey = "YOUR STORAGE KEY"

$exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password

$exportRequest

# Check status of the export
Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о долгосрочном хранении резервных копий базы данных SQL Azure как альтернативе экспорту базы данных для создания архива приведены в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](sql-database-long-term-retention.md).
- Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Чтобы узнать об импорте BACPAC-файла в базу данных SQL Server, ознакомьтесь с разделом [Импорт файла BACPAC для создания новой пользовательской базы данных](https://msdn.microsoft.com/library/hh710052.aspx).
* Сведения об экспорте BACPAC-файла из базы данных SQL Server см. в разделах [Export a Data-tier Application](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) (Экспорт приложения уровня данных) и [Migrate your SQL Server database to Azure SQL Database](sql-database-migrate-your-sql-server-database.md) (Перенос базы данных SQL Server в базу данных SQL Azure).

