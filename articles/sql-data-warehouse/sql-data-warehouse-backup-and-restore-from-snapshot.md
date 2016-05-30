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
   ms.date="04/30/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Восстановление базы данных после ошибки пользователя в хранилище данных SQL

Хранилище данных SQL предоставляет две основные возможности для восстановления после ошибки пользователя, которая привела к непреднамеренному повреждению или удалению данных.

- Восстановление активной базы данных.
- Восстановление удаленной базы данных.

Обе эти возможности позволяют восстановить новую базу данных на том же сервере. Важно убедиться, что на целевом сервере восстановления есть достаточный объем DTU для новой базы данных. В этой записи блога вы можете узнать больше о том, [как просмотреть и увеличить квоту DTU][].

## Восстановление активной базы данных
Служба хранилища данных SQL Azure защищает все активные базы данных. Для этого она создает моментальные снимки баз данных по меньшей мере каждые 8 часов и хранит их в течение 7 дней вместе с дискретным набором точек восстановления. Моментальные снимки базы данных также создаются при приостановке или удалении базы данных и сохраняются в течение 7 дней. В случае ошибки пользователя, которая привела к непреднамеренному изменению данных, базу данных можно восстановить в любой точке восстановления, срок хранения которой еще не истек.

### Портал Azure

Чтобы восстановить базу данных на портале Azure, выполните указанные ниже действия.

1. Войдите на [портал Azure][].
2. В левой части экрана выберите **ОБЗОР**, а затем — **Базы данных SQL**.
3. Перейдите к нужной базе данных и выберите ее.
4. В верхней части колонки базы данных щелкните **Восстановить**.
5. Укажите новое значение в поле **Имя базы данных**, выберите **точку восстановления**, а затем нажмите кнопку **Создать**.
6. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **УВЕДОМЛЕНИЙ**.

### PowerShell

Azure PowerShell позволяет восстановить базу данных программными средствами с помощью командлета [Restore-AzureRmSqlDatabase][].

> [AZURE.NOTE]  Чтобы использовать Azure Powershell с хранилищем данных SQL, установите Azure PowerShell 1.0.3 или выше. Чтобы узнать версию, выполните командлет **Get-Module -ListAvailable -Name Azure**. Последнюю версию можно установить с помощью [установщика веб-платформы Майкрософт][]. Дополнительную информацию об установке последней версии Azure PowerShell см. в статье [Как установить и настроить Azure PowerShell][].

1. Откройте Windows PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую базу данных, которую надо восстановить.
4. Выведите список точек восстановления для базы данных.
5. Выберите нужные точки восстановления с помощью свойства RestorePointCreationDate.
6. Восстановите базу данных в желаемой точке восстановления.
7. Убедитесь, что восстановленная база данных подключена к сети.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Если вашим сервером является foo.database.windows.net, в приведенных выше командлетах PowerShell в качестве -ServerName используйте значение "foo".

### Интерфейс REST API
Используйте REST для программного восстановления базы данных.

1. Получите список точек восстановления базы данных с помощью операции Get Database Restore Points.
2. Начните восстановление с помощью операции [Create Database Restore Request][].
3. Отследите состояние восстановления с помощью операции [Database Operation Status][].

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной базы данных][].

## Восстановление удаленной базы данных
Хранилище данных Azure SQL делает моментальный снимок базы данных перед ее удалением и хранит его в течение 7 дней. Случайно удаленную базу данных можно восстановить на момент удаления.

### Портал Azure

Чтобы восстановить удаленную базу данных SQL на портале Azure, выполните указанные ниже действия.

1. Войдите на [портал Azure][].
2. В левой части экрана выберите **ОБЗОР**, а затем — **Серверы SQL**.
3. Перейдите к нужному серверу и выберите его.
4. В колонке сервера прокрутите вниз до раздела "Операции" и щелкните **Удаленные базы данных**.
5. Выберите удаленную базу данных, которую хотите восстановить.
5. Укажите новое значение в поле **Имя базы данных** и нажмите кнопку **Создать**.
6. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **УВЕДОМЛЕНИЙ**.


### PowerShell
Для восстановления уделенной базы данных используйте командлет [Restore-AzureRmSqlDatabase][].

1. Откройте Windows PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую удаленную базу данных, которую надо восстановить.
4. Получите конкретную удаленную базу данных.
5. Восстановите удаленную базу данных.
6. Убедитесь, что восстановленная база данных подключена к сети.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Если вашим сервером является foo.database.windows.net, в приведенных выше командлетах PowerShell в качестве -ServerName используйте значение "foo".

### Интерфейс REST API
Используйте REST для программного восстановления базы данных.

1.	Создайте список всех удаленных баз данных, доступных для восстановления, с помощью операции [List Restorable Dropped Databases][].
2.	Получите подробную информацию об удаленной базе данных, которую необходимо восстановить, с помощью операции [Get Restorable Dropped Database][].
3.	Начните восстановление с помощью операции [Create Database Restore Request][].
4.	Отследите состояние восстановления с помощью операции [Database Operation Status][].

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной базы данных][].

## Дальнейшие действия
Дополнительные сведения о функциях обеспечения непрерывности бизнес-процессов в выпусках базы данных SQL Azure см. в [обзоре непрерывности бизнес-процессов базы данных Azure SQL][].

<!--Image references-->

<!--Article references-->
[обзоре непрерывности бизнес-процессов базы данных Azure SQL]: ../sql-database/sql-database-business-continuity.md
[Финализация восстановленной базы данных]: ../sql-database/sql-database-recovered-finalize.md
[Как установить и настроить Azure PowerShell]: ../powershell/powershell-install-configure.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[как просмотреть и увеличить квоту DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[портал Azure]: https://portal.azure.com/
[установщика веб-платформы Майкрософт]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->