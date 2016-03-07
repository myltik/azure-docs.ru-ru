<properties
   pageTitle="Восстановление базы данных после ошибки пользователя в хранилище данных SQL | Microsoft Azure"
   description="Процедура восстановления базы данных после ошибки пользователя в хранилище данных SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/17/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Восстановление базы данных после ошибки пользователя в хранилище данных SQL

Хранилище данных SQL предоставляет две основные возможности для восстановления после ошибки пользователя, которая привела к непреднамеренному повреждению или удалению данных:

- Восстановление активной базы данных.
- Восстановление удаленной базы данных.

Обе эти возможности позволяют восстановить новую базу данных на том же сервере.

Существует два разных API, которые поддерживают восстановление базы данных хранилища данных SQL: Azure PowerShell и REST API. Для доступа к функции восстановления хранилища данных SQL можно использовать любой из этих API.

## Восстановление активной базы данных
В случае ошибки пользователя, которая привела к непреднамеренному изменению данных, базу данных можно восстановить в любой точке восстановления, срок хранения которой еще не истек. Моментальные снимки активной базы данных создаются как минимум через каждые 8 часов и хранятся в течение 7 дней.

### PowerShell

Используйте Azure PowerShell для программного восстановления базы данных. Чтобы загрузить модуль Azure PowerShell, запустите [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Чтобы узнать версию, выполните командлет Get-Module -ListAvailable -Name Azure. В этой статье используются командлеты, выполняемые в Azure PowerShell версии 1.0.4.

Для восстановления базы данных используйте командлет [Start-AzureSqlDatabaseRestore][].

1. Откройте Windows PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую базу данных, которую надо восстановить.
4. Откройте список точек восстановления базы данных (требуется режим управления ресурсами Azure).
5. Выберите нужные точки восстановления с помощью свойства RestorePointCreationDate.
6. Восстановите базу данных в желаемой точке восстановления.
7. Проследите за ходом восстановления.

```

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>").RestorePointCreationDate)[-10 .. -1]

	# Or for all restore points
	Get-AzureRmSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database name to restore
(Get-AzureRmSqlDatabase -ServerName "<YourServerName>" -ResourceGroupName "<YourResourceGroupName>").DatabaseName | where {$_ -ne "master" }
#or
Get-AzureRmSqlDatabase -ServerName "<YourServerName>" –ResourceGroupName "<YourResourceGroupName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabaseName "<YourDatabaseName>" -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Обратите внимание, что если вашим сервером является foo.database.windows.net, в приведенных выше командлетах PowerShell в качестве -ServerName используйте значение foo.

### Интерфейс REST API
Используйте REST для программного восстановления базы данных.

1. Получите список точек восстановления базы данных с помощью операции Get Database Restore Points.
2. Начните восстановление с помощью операции [Create Database Restore Request][].
3. Отследите состояние восстановления с помощью операции [Database Operation Status][].

Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Завершение восстановленной базы данных][].

## Восстановление удаленной базы данных
Удаленную базу данных можно восстановить на момент удаления. Хранилище данных Azure SQL делает моментальный снимок базы данных перед ее удалением и хранит его в течение 7 дней.

### PowerShell
Azure PowerShell позволяет восстановить удаленную базу данных программными средствами. Чтобы загрузить модуль Azure PowerShell, запустите [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).

Чтобы восстановить удаленную базу данных, используйте командлет [Start-AzureSqlDatabaseRestore][].

1. Откройте Microsoft Azure PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую удаленную базу данных, которую надо восстановить.
4. Найдите базу данных и дату ее удаления в списке удаленных баз данных.

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. Получите конкретную удаленную базу данных и начинайте восстановление.

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Обратите внимание, что если вашим сервером является foo.database.windows.net, в приведенных выше командлетах PowerShell в качестве -ServerName используйте значение foo.

### Интерфейс REST API
Используйте REST для программного восстановления базы данных.

1.	Создайте список всех удаленных баз данных, доступных для восстановления, с помощью операции [List Restorable Dropped Databases][].
2.	Получите подробную информацию об удаленной базе данных, которую необходимо восстановить, с помощью операции [Get Restorable Dropped Database][].
3.	Начните восстановление с помощью операции [Create Database Restore Request][].
4.	Отследите состояние восстановления с помощью операции [Database Operation Status][].

Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Завершение восстановленной базы данных][].


## Дальнейшие действия
Дополнительные сведения о функциях обеспечения непрерывности бизнес-процессов в других выпусках базы данных Azure SQL см. в статье [Обзор непрерывности бизнес-процессов базы данных Azure SQL][].


<!--Image references-->

<!--Article references-->
[Обзор непрерывности бизнес-процессов базы данных Azure SQL]: sql-database/sql-database-business-continuity.md
[Завершение восстановленной базы данных]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0224_2016-->