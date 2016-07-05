<properties
   pageTitle="Восстановление хранилища данных SQL Azure (PowerShell) | Microsoft Azure"
   description="Задачи PowerShell для восстановления хранилища данных SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/17/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Восстановление хранилища данных SQL Azure (PowerShell)

> [AZURE.SELECTOR]
- [Обзор][]
- [Портал][]
- [PowerShell][]
- [REST][]

Из этой статьи вы узнаете, как восстановить хранилище данных SQL Azure с помощью PowerShell.

## Перед началом работы

### Проверьте ресурсы DTU Базы данных SQL. 

Каждое хранилище данных SQL размещается на логическом сервере SQL Server. Этот логический сервер имеет предельную емкость, измеряемую в DTU. Перед восстановлением хранилища данных SQL важно убедиться, что логический сервер SQL Server, на котором размещена база данных, имеет достаточное количество DTU для восстанавливаемой базы данных. В этой записи блога вы можете узнать больше о том, [как просмотреть и увеличить квоту DTU][].

### Установка PowerShell

Чтобы использовать Azure PowerShell с хранилищем данных SQL, установите Azure PowerShell 1.0 или более поздней версии. Чтобы узнать текущую версию, выполните командлет **Get-Module -ListAvailable -Name Azure**. Последнюю версию можно установить с [установщика веб-платформы Майкрософт][]. Дополнительную информацию об установке последней версии Azure PowerShell см. в статье [Установка и настройка Azure PowerShell][].

## Восстановление активной или приостановленной базы данных

Для восстановления базы данных из моментального снимка используйте командлет PowerShell [Restore-AzureRmSqlDatabase][].

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

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной базы данных SQL Azure][].


## Восстановление удаленной базы данных.

Для восстановления удаленной базы данных используйте командлет [Restore-AzureRmSqlDatabase][].

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

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной базы данных SQL Azure][].


## Восстановление из географического региона Azure

Для восстановления базы данных используйте командлет [Restore-AzureRmSqlDatabase][].

1. Откройте Windows PowerShell.
2. Подключитесь к своей учетной записи Azure и выведите список всех подписок, связанных с ней.
3. Выберите подписку, содержащую базу данных, которую надо восстановить.
4. Получите базу данных, которую требуется восстановить.
5. Создайте запрос на восстановление базы данных.
6. Проверьте состояние геовосстановленной базы данных.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной базы данных SQL Azure][].


Восстановленная база данных будет поддерживать прозрачное шифрование данных, если исходная база данных поддерживает прозрачное шифрование данных.


## Дальнейшие действия
Дополнительные сведения о функциях обеспечения непрерывности бизнес-процессов в выпусках Базы данных SQL Azure см. в статье [Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL][].

<!--Image references-->

<!--Article references-->
[Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL]: sql-database-business-continuity.md
[Finalize a recovered database]: sql-database-recovered-finalize.md
[Установка и настройка Azure PowerShell]: powershell-install-configure.md
[Обзор]: ./sql-data-warehouse-restore-database-overview.md
[Портал]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Финализация восстановленной базы данных SQL Azure]: ./sql-database-recovered-finalize.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[как просмотреть и увеличить квоту DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[установщика веб-платформы Майкрософт]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0622_2016-->