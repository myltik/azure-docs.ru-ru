<properties 
    pageTitle="Восстановление базы данных SQL Azure до точки во времени с помощью PowerShell | Microsoft Azure" 
    description="Восстановление базы данных SQL Azure до точки во времени" 
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
    ms.date="06/09/2016"
    ms.author="sstein"/>

# Восстановление базы данных SQL Azure до точки во времени с помощью PowerShell

В этой статье объясняется, как восстановить базу данных до момента времени в прошлом (на основании [сделанных автоматических резервных копий базы данных SQL](sql-database-automated-backups.md)) с помощью PowerShell.

[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]

## Восстановление базы данных до точки во времени в качестве автономной базы данных

1. Получите базу данных, которую необходимо восстановить, используя командлет [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Восстановите базу данных до точки во времени, используя командлет [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## Восстановление базы данных до точки во времени в пул эластичных баз данных
   
1. Получите базу данных, которую необходимо восстановить, используя командлет [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Восстановите базу данных до точки во времени, используя командлет [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## Дальнейшие действия

- [Финализация восстановленной Базы данных SQL Azure](sql-database-recovered-finalize.md)
- [Восстановление состояния системы в определенной временной точке](sql-database-point-in-time-restore.md)
- [Восстановление базы данных SQL Azure до момента времени в прошлом с помощью портала Azure](sql-database-point-in-time-restore-portal.md)
- [Create or Update Database](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Создание и обновление базы данных)
- [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md)

## Дополнительные ресурсы

- [Восстановление удаленной базы данных.](sql-database-restore-deleted-database.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Геовосстановление](sql-database-geo-restore.md)
- [Активная георепликация](sql-database-geo-replication-overview.md)
- [Проектирование приложений для аварийного восстановления в облаке](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->