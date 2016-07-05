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
- [Обзор](sql-database-point-in-time-restore.md)
- [Портал Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

В этой статье объясняется, как восстановить базу данных до точки времени в прошлом из [созданных автоматических резервных копий базы данных SQL](sql-database-automated-backups.md) с помощью PowerShell.

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

- Подробные инструкции по восстановлению до точки во времени с помощью портала Azure см. в разделе [Восстановление базы данных SQL Azure до точки во времени с помощью портала Azure](sql-database-point-in-time-restore-portal.md).
- Чтобы узнать о восстановлении до точки во времени с помощью REST API, ознакомьтесь с [данным способом восстановления](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Общие сведения о восстановлении до точки во времени см. в разделе [Обзор. Функция восстановления до точки во времени в Базе данных SQL](sql-database-point-in-time-restore.md).
- Восстановление после ошибки пользователя или приложения подробно рассматривается в разделе [Восстановление базы данных SQL Azure после ошибки пользователя](sql-database-user-error-recovery.md).

## Дополнительные ресурсы

- [Сценарии обеспечения непрерывности бизнес-процессов](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->