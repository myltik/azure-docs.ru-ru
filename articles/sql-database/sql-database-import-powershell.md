---
title: "Импорт BACPAC-файла для создания базы данных SQL Azure с помощью PowerShell | Документация Майкрософт"
description: "Импорт BACPAC-файла для создания базы данных SQL Azure с помощью PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 8d78da13-43fe-4447-92e0-0a41d0321fd4
ms.service: sql-database
ms.custom: migrate and move; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/31/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4e1cf312cf8213cbeea84b4fb0ab605248800fcd


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Импорт BACPAC-файла для создания базы данных SQL Azure с помощью PowerShell
**Отдельная база данных**

> [!div class="op_single_selector"]
> * [Портал Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> 
> 

В этой статье приведены указания о том, как создать базу данных SQL Azure, импортировав [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) -файл с помощью PowerShell.

База данных создается на основе BACPAC-файла, импортированного из контейнера больших двоичных объектов в службе хранилища Azure. Если в службе хранилища Azure нет BACPAC-файла, ознакомьтесь с разделом [Архивация базы данных SQL Azure в BACPAC-файл с помощью PowerShell](sql-database-export-powershell.md). Если у вас уже есть BACPAC-файл, который находится не в службе хранилища Azure, то вы можете [легко передать его в свою учетную запись хранения Azure с помощью AzCopy](../storage/storage-use-azcopy.md#blob-upload).

> [!NOTE]
> База данных SQL Azure автоматически создает и обслуживает резервные копии для каждой пользовательской базы данных, которую можно восстановить. Дополнительные сведения см. в статье [Подробнее о резервном копировании базы данных SQL](sql-database-automated-backups.md).
> 
> 

Чтобы импортировать базу данных SQL, необходимо следующее.

* Подписка Azure. Если вам нужна подписка Azure, просто щелкните **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ** в верхней части этой страницы. Оформив подписку, вернитесь к этой статье.
* BACPAC-файл базы данных, которую вы хотите импортировать. BACPAC-файл должен находиться в контейнере больших двоичных объектов [учетной записи хранения Azure](../storage/storage-create-storage-account.md) .

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="set-up-the-variables-for-your-environment"></a>Настройка переменных для среды
В некоторых переменных приведенные для примера значения необходимо заменить на значения, соответствующие вашей базе данных и учетной записи хранения.

В качестве имени сервера следует указать сервер, уже существующий в подписке, выбранной на предыдущем шаге. Это должен быть сервер, на котором требуется создать базу данных. Импорт базы данных непосредственно в пул эластичных БД не поддерживается. Однако вы можете сначала выполнить импорт в отдельную базу данных, а затем переместить эту базу данных в пул.

Имя базы данных — это желаемое новой базы данных.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Указанные ниже переменные взяты из учетной записи хранения, где находится BACPAC-файл. Чтобы получить эти значения, перейдите к своей учетной записи хранения на [портале Azure](https://portal.azure.com). Чтобы найти первичный ключ доступа, последовательно выберите **Все параметры** и **Ключи** в колонке учетной записи хранения.

Имя большого двоичного объекта — это имя существующего BACPAC-файла, на основе которого вы хотите создать базу данных. Укажите расширение BACPAC.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


После выполнения командлета [Get-Credential](https://msdn.microsoft.com/library/azure/hh849815\(v=azure.300\).aspx) откроется окно с запросом имени пользователя и пароля. Введите имя для входа и пароль администратора сервера базы данных SQL ($ServerName в приведенном выше примере), а не имя пользователя и пароль своей учетной записи Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Импорт базы данных
Эта команда отправляет в службу запрос об импорте базы данных. Операция импорта может занять некоторое время в зависимости от размера базы данных.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Отслеживание хода выполнения операции
Состояние запроса после выполнения [New-AzureRmSqlDatabaseImport](https://msdn.microsoft.com/library/azure/mt707793\(v=azure.300\).aspx) можно проверить с помощью командлета [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Сценарий импорта базы данных SQL с помощью PowerShell
    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Дальнейшие действия
* Чтобы научиться подключаться к импортированной базе данных SQL и отправлять к ней запросы, ознакомьтесь с разделом [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)




<!--HONumber=Nov16_HO3-->


