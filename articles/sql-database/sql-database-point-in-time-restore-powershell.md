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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# Восстановление базы данных SQL Azure до точки во времени с помощью PowerShell

> [AZURE.SELECTOR]
- [Обзор](sql-database-recovery-using-backups.md)
- [Восстановление до точки во времени: портал Azure](sql-database-point-in-time-restore-portal.md)

В этой статье объясняется, как восстановить базу данных до предшествующей точки времени из [созданных автоматически резервных копий базы данных SQL](sql-database-automated-backups.md) с помощью PowerShell.

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

- Об обеспечении непрерывности бизнес-процессов можно узнать в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md).
- Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).
- Чтобы изучить сценарии проектирования и восстановления непрерывности бизнес-процессов, ознакомьтесь со [сценариями обеспечения непрерывности](sql-database-business-continuity-scenarios.md).
- Чтобы узнать об использовании создаваемых автоматически резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md).
- Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md).
- Чтобы узнать об использовании создаваемых автоматически резервных копий для архивации, ознакомьтесь с [копированием базы данных](sql-database-copy.md).

<!---HONumber=AcomDC_0629_2016-->