---
title: "PowerShell: экспорт базы данных SQL Azure в BACPAC-файл | Документация Майкрософт"
description: "Экспорт базы данных SQL Azure в BACPAC-файл с помощью PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 41774c7f1c038ee017d719e59ed79fb68999dac8
ms.lasthandoff: 03/29/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-to-a-bacpac-file-by-using-powershell"></a>Экспорт базы данных SQL Azure или SQL Server в BACPAC-файл с помощью PowerShell

В статье содержатся инструкции по экспорту базы данных SQL Azure или SQL Server в BACPAC-файл, сохраняемый в хранилище BLOB-объектов Azure, с помощью PowerShell. Общие сведения об экспорте в BACPAC-файл см. в [этой статье](sql-database-export.md).

> [!NOTE]
> Вы также можете экспортировать файл базы данных SQL Azure в BACPAC-файл с помощью [портала Azure](sql-database-export-portal.md), [SQL Server Management Studio](sql-database-export-ssms.md) или [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо следующее:

* Подписка Azure.
* База данных SQL Azure.
* [Учетная запись хранения Azure уровня "Стандартный"](../storage/storage-create-storage-account.md)с контейнером больших двоичных объектов для хранения BACPAC-файла в хранилище уровня "Стандартный".

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>Экспорт базы данных
Командлет [New AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) отправляет к службе запрос на экспорт базы данных. Операция экспорта может занять некоторое время в зависимости от размера базы данных.

> [!IMPORTANT]
> Для получения транзакционно согласованного BACPAC-файла сначала [создайте копию базы данных](scripts/sql-database-copy-database-to-new-server-powershell.md), а затем экспортируйте эту копию.
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Отслеживание хода выполнения операции экспорта
Состояние запроса после выполнения командлета [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com//powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) можно проверить, выполнив командлет [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus). Если выполнить его немедленно после запроса, то обычно возвращается сообщение **Состояние: выполняется**. Отображение сообщения **Состояние: выполнен** означает, что экспорт завершен.

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Пример экспорта базы данных SQL
В следующем примере существующая база данных SQL экспортируется в BACPAC-файл. В нем также демонстрируется, как проверить состояние операции экспорта.

Чтобы запустить пример, значения некоторых переменных необходимо заменить значениями, соответствующими вашей базе данных и учетной записи хранения. На [портале Azure](https://portal.azure.com)перейдите к своей учетной записи хранения, чтобы получить имя учетной записи хранения, имя контейнера больших двоичных объектов и значение ключа. Чтобы отобразить ключ, щелкните **Ключи доступа** в колонке учетной записи хранения.

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

## <a name="automate-export-using-azure-automation"></a>Автоматизация экспорта с помощью службы автоматизации Azure

В настоящий момент служба Azure SQL Database Automated Export находится на этапе предварительной версии, и ее использование будет прекращено 1 марта 2017 года. Начиная с 1 декабря 2016 года будет невозможно настроить автоматический экспорт для какой-либо базы данных SQL. Все существующие задания автоматического экспорта продолжат работать до 1 марта 2017 года. После 1 декабря 2016 года можно использовать [долгосрочное хранение резервных копий](sql-database-long-term-retention.md) или [службу автоматизации Azure](../automation/automation-intro.md), чтобы периодически архивировать базы данных SQL с помощью PowerShell согласно выбранному расписанию. Пример сценария можно скачать из [репозитория GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 


## <a name="next-steps"></a>Дальнейшие действия
* Чтобы узнать, как импортировать базу данных Azure SQL с помощью PowerShell, ознакомьтесь с разделом [Импорт BACPAC-файла для создания новой базы данных SQL Azure с помощью PowerShell](scripts/sql-database-import-from-bacpac-powershell.md).
* Чтобы узнать об импорте BACPAC-файла в базу данных SQL Azure с помощью SQLPackage, см. [эту статью](sql-database-import-sqlpackage.md).
* Чтобы узнать об импорте BACPAC-файла в базу данных SQL Azure с помощью портала Azure, см. [эту статью](sql-database-import-portal.md).
* Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](sql-database-cloud-migrate.md).
* Дополнительные сведения о длительном периоде удержания резервных копий базы данных SQL Azure как альтернативе экспорту базы данных для создания архива см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](sql-database-long-term-retention.md).
* Чтобы узнать об импорте BACPAC-файла в базу данных SQL Server, ознакомьтесь с разделом [Импорт файла BACPAC для создания новой пользовательской базы данных](https://msdn.microsoft.com/library/hh710052.aspx)



## <a name="additional-resources"></a>Дополнительные ресурсы
* [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus)


