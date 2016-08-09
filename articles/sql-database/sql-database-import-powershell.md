<properties 
    pageTitle="Импорт BACPAC-файла для создания новой базы данных SQL Azure с помощью PowerShell | Microsoft Azure" 
    description="Импорт BACPAC-файла для создания новой базы данных SQL Azure с помощью PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="07/06/2016"
    ms.author="sstein"/>

# Импорт BACPAC-файла для создания новой базы данных SQL Azure с помощью PowerShell

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

В этой статье приведены указания о том, как создать базу данных SQL Azure, импортировав BACPAC-файл с помощью PowerShell.

BACPAC-файл — это файл с расширением .bacpac, который содержит схему базы данных и данные. Дополнительные сведения см. в разделе «Пакет резервного копирования (BACPAC)» в статье [Приложения уровня данных](https://msdn.microsoft.com/library/ee210546.aspx).

База данных создается на основе BACPAC-файла, импортированного из контейнера больших двоичных объектов хранилища Azure. Если в службе хранилища Azure нет BACPAC-файла, его можно создать, выполнив одно из действий, описанных в статье [Архивация базы данных SQL Azure в BACPAC-файл с помощью PowerShell](sql-database-export-powershell.md).

> [AZURE.NOTE] База данных SQL Azure автоматически создает и обслуживает резервные копии для каждой пользовательской базы данных, которую можно восстановить. Дополнительные сведения см. в статье [Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL Azure](sql-database-automated-backups.md).


Чтобы импортировать базу данных SQL, необходимо следующее.

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- BACPAC-файл базы данных, которую вы хотите импортировать. BACPAC-файл должен находиться в контейнере больших двоичных объектов [учетной записи хранения Azure (классика)](../storage/storage-create-storage-account.md).



[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]



## Настройка переменных для среды

В некоторых переменных приведенные для примера значения необходимо заменить на значения, соответствующие вашей базе данных и учетной записи хранения.

В качестве имени сервера необходимо указать сервер, который уже существует в подписке, выбранной на предыдущей этапе, и на котором вы хотите создать базу данных. Обратите внимание на то, что импорт базы данных напрямую в пул эластичных БД не поддерживается, однако для начала можно импортировать ее в отдельную базу данных, а потом переместить ее в пул.

Имя базы данных — это желаемое новой базы данных.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Указанные ниже переменные взяты из учетной записи хранения, где находится BACPAC-файл. Чтобы получить эти значения, перейдите к учетной записи хранения на [портале Azure](https://portal.azure.com). Чтобы найти первичный ключ доступа, последовательно щелкните **Все параметры** и **Ключи** в колонке учетной записи хранения.

Имя большого двоичного объекта — это имя существующего BACPAC-файла, на основе которого вы хотите создать базу данных. Укажите расширение BACPAC.

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


После выполнения командлета **Get-Credential** откроется окно с запросом имени пользователя и пароля. Введите имя входа и пароль администратора сервера базы данных SQL ($ServerName в приведенном выше примере), а не имя пользователя и пароль вашей учетной записи Azure.

    $credential = Get-Credential


## Импорт базы данных

Эта команда отправляет в службу запрос об импорте базы данных. Операция импорта может занять некоторое время в зависимости от размера базы данных.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
    

## Отслеживание хода выполнения операции

Состояние запроса можно проверить, выполнив командлет **New-AzureRmSqlDatabaseImport**.

Обычно при проверке состояния сразу после запроса возвращается значение **Ожидание** или **Выполняется** и указывается текущий процент выполнения, так что командлет может выдать результат **Состояние: завершено** не с первого раза.

Для выполнения этой команды потребуется пароль. Введите имя входа и пароль администратора для сервера SQL.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink
 


## Сценарий импорта базы данных SQL с помощью PowerShell


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
 
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink

    

## Дальнейшие действия

- Чтобы научиться подключаться к импортированной базе данных SQL и отправлять к ней запросы, ознакомьтесь с разделом [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md).

<!---HONumber=AcomDC_0727_2016-->