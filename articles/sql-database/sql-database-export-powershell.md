<properties 
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
	ms.date="07/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Архивация базы данных SQL Azure в BACPAC-файл с помощью PowerShell

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


В статье содержатся инструкции по архивации базы данных SQL Azure в BACPAC-файл, хранимый в хранилище BLOB-объектов Azure, с помощью PowerShell.

Когда вам нужно создать архив базы данных SQL Azure, можно экспортировать схему базы данных и данные в BACPAC-файл. BACPAC-файл — это просто ZIP-файл с расширением BACPAC. BACPAC-файл можно сохранить в хранилище BLOB-объектов Azure или локальном хранилище, а затем импортировать обратно в базу данных SQL Azure или локальный экземпляр SQL Server.

***Рекомендации***

- Чтобы архив был транзакционно согласован, необходимо обеспечить отсутствие каких-либо операций записи во время экспорта или экспортировать данные из [транзакционно согласованной копии](sql-database-copy.md) базы данных SQL Azure.
- Максимальный размер BACPAC-файла архива в хранилище BLOB-объектов Azure составляет 200 ГБ. Для архивации BACPAC-файла большего размера в локальное хранилище используйте программу командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Эта служебная программа поставляется вместе с Visual Studio и SQL Server. Кроме того, вы можете [скачать](https://msdn.microsoft.com/library/mt204009.aspx) последнюю версию SQL Server Data Tools для получения этой служебной программы.
- Архивация в хранилище Azure класса Premium с помощью BACPAC-файла не поддерживается.
- Если операция экспорта длится более 20 часов, она может быть отменена. Для повышения производительности во время экспорта можно сделать следующее.
 - Временно повысить уровень служб.
 - Прекратить все операции чтения и записи во время экспорта.
 - Использовать кластеризованный индекс для всех больших таблиц. Без кластеризованных индексов экспорт может завершиться ошибкой, если он длится больше 6–12 часов. Это обусловлено тем, что службам экспорта требуется выполнить проверку таблицы, чтобы экспортировать всю таблицу.
 
> [AZURE.NOTE] BACPAC-файлы не предназначены для операций службы архивации и восстановления. База данных SQL Azure автоматически создает резервные копии для каждой пользовательской базы данных. Дополнительные сведения см. в статье [Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL Azure](sql-database-automated-backups.md).

Для работы с этой статьей необходимо следующее:

- Подписка Azure.
- База данных SQL Azure.
- [Учетная запись хранения Azure уровня "Стандартный"](../storage/storage-create-storage-account.md) с контейнером больших двоичных объектов для хранения BACPAC-файла в хранилище уровня "Стандартный".


[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]


## Настройка переменных для конкретной среды

В некоторых переменных приведенные для примера значения необходимо заменить на значения, соответствующие вашей базе данных и учетной записи хранения.

Замените своими значениями.

    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"


Чтобы получить эти значения, перейдите к учетной записи хранения на [портале Azure](https://portal.azure.com). Чтобы найти первичный ключ доступа, последовательно щелкните **Все параметры** и **Ключи** в колонке учетной записи хранения.

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


После выполнения командлета **Get-Credential** откроется окно с запросом имени пользователя и пароля. Введите имя пользователя и пароль администратора SQL Server (это не имя пользователя и пароль учетной записи Azure).

    $credential = Get-Credential

## Экспорт базы данных

Эта команда отправляет в службу запрос об экспорте базы данных. Операция экспорта может занять некоторое время в зависимости от размера базы данных.

> [AZURE.IMPORTANT] Для получения транзакционно согласованного BACPAC-файла сначала [создайте копию базы данных](sql-database-copy-powershell.md), а затем экспортируйте эту копию.


    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password
    

## Отслеживание хода выполнения операции экспорта

Состояние запроса можно проверить, выполнив командлет **New-AzureRmSqlDatabaseExport**. Обычно при проверке состояния сразу же после отправки запроса возвращается результат **Состояние: ожидание** или **Состояние: выполняется**, поэтому результат **Состояние: завершено** может появиться не с первого раза.

Для выполнения этой команды потребуется пароль. Введите имя пользователя и пароль администратора для сервера SQL.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink
    


## Экспорт базы данных SQL с помощью сценария PowerShell


    $ServerName = "servername"
    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName  –AdministratorPassword $credential.Password

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink

## Дальнейшие действия

- [Импорт базы данных SQL Azure](sql-database-import-powershell.md)


## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0720_2016-->