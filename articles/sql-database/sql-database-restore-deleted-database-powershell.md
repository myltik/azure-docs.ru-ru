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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Восстановление удаленной базы данных SQL Azure с помощью PowerShell


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

В этой статье показано, как восстановить удаленную базу данных SQL Azure.

Если база данных удалена, База данных SQL Azure позволяет восстановить ее на момент удаления. База данных SQL Azure хранит резервную копию удаленной базы данных в течение срока хранения.

Срок хранения удаленной базы данных определяется в зависимости от уровня службы базы данных во время ее существования или от количества дней, в течение которых существовала база данных. Выбирается меньшее значение. Дополнительные сведения о хранении баз данных см. в статье [Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL](sql-database-business-continuity.md).


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

- [Финализация восстановленной Базы данных SQL Azure](sql-database-recovered-finalize.md)
- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)



## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->