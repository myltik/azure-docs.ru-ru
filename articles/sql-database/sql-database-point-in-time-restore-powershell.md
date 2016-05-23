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
    ms.date="05/10/2016"
    ms.author="sstein"/>

# Восстановление базы данных SQL Azure до точки во времени с помощью PowerShell

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

В этой статье объясняется, как восстановить базу данных до точки во времени с помощью PowerShell.

[**Восстановление до точки во времени**](sql-database-point-in-time-restore.md) — это возможность самообслуживания, которая позволяет восстановить базу данных из автоматических резервных копий, которые создаются для всех баз данных, до любого момента времени за период хранения базы данных. Дополнительные сведения об автоматических резервных копиях и периодах хранения см. в статье [Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL](sql-database-business-continuity.md).

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
- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)


## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->