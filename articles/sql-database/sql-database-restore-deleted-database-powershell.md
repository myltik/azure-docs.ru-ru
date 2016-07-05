<properties
	pageTitle="Восстановление удаленной базы данных SQL Azure (PowerShell) | Microsoft Azure"
	description="Восстановление удаленной базы данных SQL Azure (PowerShell)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Восстановление удаленной базы данных SQL Azure с помощью PowerShell

> [AZURE.SELECTOR]
- [Обзор](sql-database-restore-deleted-database.md)
- [Портал Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]


## Восстановление удаленной базы данных в автономную базу данных

1. Создайте резервную копию удаленной базы данных, которую требуется восстановить, используя командлет [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Запустите восстановление из резервной копии удаленной базы данных с помощью командлета [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## Восстановление удаленной базы данных в пул эластичных баз данных

1. Создайте резервную копию удаленной базы данных, которую требуется восстановить, используя командлет [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Запустите восстановление из резервной копии удаленной базы данных с помощью командлета [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01" 

## Дальнейшие действия

- Подробные инструкции по восстановлению удаленной базы данных с помощью портала Azure см. в разделе [Восстановление удаленной базы данных SQL Azure на портале Azure](sql-database-restore-deleted-database-portal.md).
- Чтобы узнать, как восстановить удаленную базу данных с помощью REST API, [ознакомьтесь с данной процедурой](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Подробные сведения о восстановлении удаленной базы данных см. в разделе [Восстановление удаленной базы данных](sql-database-restore-deleted-database.md).
- Подробные сведения об автоматически создаваемых резервных копиях базы данных SQL Azure см. в разделе [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).

## Дополнительные ресурсы

- [Восстановление до точки во времени](sql-database-point-in-time-restore.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Геовосстановление](sql-database-geo-restore.md)
- [Активная георепликация](sql-database-geo-replication-overview.md)
- [Проектирование приложений для аварийного восстановления в облаке](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->