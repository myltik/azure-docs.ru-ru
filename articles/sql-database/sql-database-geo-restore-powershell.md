<properties 
    pageTitle="Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью PowerShell | Microsoft Azure" 
    description="Восстановление базы данных SQL Azure на новый сервер из геоизбыточной резервной копии" 
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
    ms.workload="NA" 
    ms.date="07/17/2016"
    ms.author="sstein"/>

# Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью PowerShell


> [AZURE.SELECTOR]
- [Обзор](sql-database-recovery-using-backups.md)
- [Геовосстановление: портал Azure](sql-database-geo-restore-portal.md)

В этой статье показано, как восстановить базу данных на новый сервер с помощью геовосстановления и PowerShell.

[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]

## Геовосстановление базы данных в автономную базу данных

1. Получите геоизбыточную резервную копию базы данных, которую необходимо восстановить, с помощью командлета [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Запустите восстановление из геоизбыточной резервной копии с помощью командлета [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## Геовосстановление базы данных в пул эластичных баз данных

1. Получите геоизбыточную резервную копию базы данных, которую необходимо восстановить, с помощью командлета [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Запустите восстановление из геоизбыточной резервной копии с помощью командлета [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx). Укажите имя пула, в который необходимо восстановить базу данных.
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  


## Дальнейшие действия

- Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md).
- Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).
- Чтобы узнать об использовании автоматически создаваемых резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md).
- Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md).
- Чтобы узнать об использовании автоматически создаваемых резервных копий для архивации, ознакомьтесь с [копированием базы данных](sql-database-copy.md).

<!---HONumber=AcomDC_0727_2016-->