<properties
   pageTitle="Восстановление базы данных в хранилище данных SQL Azure (PowerShell) | Microsoft Azure"
   description="Задачи PowerShell для восстановления работающей, удаленной или недоступной базы данных в хранилище данных SQL Azure."
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
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Архивация и восстановление базы данных в хранилище данных SQL Azure (PowerShell)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-overview-manage-database-restore.md)
- [Портал](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Задачи PowerShell для восстановления работающей, удаленной или недоступной базы данных в хранилище данных SQL.

Задачи раздела

- Восстановление активной базы данных.
- Восстановление удаленной базы данных.
- Восстановление недоступной базы данных из другого географического региона Azure

[AZURE.INCLUDE [Политика хранения резервных копий хранилища данных SQL](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Перед началом работы

### Проверьте ресурсы DTU Базы данных SQL. 
Так как восстановление хранилища данных SQL выполняется в новую базу данных на логическом сервере SQL Server, важно убедиться, что на используемом сервере имеется достаточно единиц DTU для новой базы данных. В этой записи блога вы можете узнать больше о том, [как просмотреть и увеличить квоту DTU][].

### Установка PowerShell

Чтобы использовать Azure PowerShell с хранилищем данных SQL, установите Azure PowerShell 1.0 или более поздней версии. Чтобы узнать текущую версию, выполните командлет **Get-Module -ListAvailable -Name Azure**. Последнюю версию можно установить с [установщика веб-платформы Майкрософт][]. Дополнительную информацию об установке последней версии Azure PowerShell см. в статье [Как установить и настроить Azure PowerShell][].

## Восстановление активной базы данных.

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

>[AZURE.NOTE] Если вашим сервером является foo.database.windows.net, в приведенных выше командлетах PowerShell в качестве -ServerName используйте значение "foo".

Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной базы данных][].

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

>[AZURE.NOTE] Если вашим сервером является foo.database.windows.net, в приведенных выше командлетах PowerShell в качестве -ServerName используйте значение "foo".

Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной базы данных][].

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

### Настройка базы данных после выполнения географического восстановления
Далее приведен контрольный список задач, который можно использовать для подготовки восстановленной базы данных к использованию в рабочей среде.

1. **Обновление строк подключения**. Убедитесь, что строки подключения клиентских средств указывают на только что восстановленную базу данных.
2. **Изменение правил брандмауэра**. Проверьте правила брандмауэра на целевом сервере и убедитесь в том, что в них разрешены подключения клиентских компьютеров или Azure к серверу и только что восстановленной базе данных.
3. **Проверка имен для входа на сервер и пользователей базы данных**. Проверьте существование всех имен для входа, используемых вашим приложением, на сервере, на котором размещена восстановленная база данных. Повторно создайте отсутствующие имена для входа и предоставьте им соответствующие разрешения на работу с восстановленной базой данных. 
4. **Включение аудита**. Если для доступа к базе данных требуется аудит, то после восстановления базы данных его необходимо включить.

Восстановленная база данных будет поддерживать прозрачное шифрование данных, если исходная база данных поддерживает прозрачное шифрование данных.


## Дальнейшие действия
Дополнительные сведения см. в статьях [Непрерывность бизнес-процессов в базе данных SQL Azure][] и [Management overview][] (Обзор управления).

<!--Image references-->

<!--Article references-->
[Непрерывность бизнес-процессов в базе данных SQL Azure]: sql-database-business-continuity.md
[Финализация восстановленной базы данных]: sql-database-recovered-finalize.md
[Как установить и настроить Azure PowerShell]: powershell-install-configure.md
[Management overview]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[как просмотреть и увеличить квоту DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[установщика веб-платформы Майкрософт]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0608_2016-->