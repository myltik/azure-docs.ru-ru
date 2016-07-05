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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью PowerShell


> [AZURE.SELECTOR]
- [Обзор](sql-database-geo-restore.md)
- [Портал Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

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

- Подробные инструкции по восстановлению базы данных SQL Azure с помощью портала Azure из геоизбыточной резервной копии см. в разделе [Геовосстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure](sql-database-geo-restore-portal.md).
- Дополнительные подробные сведения о восстановлении базы данных SQL Azure из геоизбыточной резервной копии см. в разделе[Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью PowerShell](sql-database-geo-restore.md).
- Подробное обсуждение восстановления после сбоя см. в разделе [Восстановление базы данных SQL Azure или переход на базу данных-получатель при отказе](sql-database-disaster-recovery.md).


## Дополнительные ресурсы

- [Сценарии обеспечения непрерывности бизнес-процессов](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->