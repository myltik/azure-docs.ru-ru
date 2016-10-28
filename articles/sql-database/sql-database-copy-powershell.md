<properties 
    pageTitle="Копирование базы данных SQL Azure с помощью PowerShell | Microsoft Azure" 
    description="Создание копии Базы данных SQL Azure с помощью PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/08/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Копирование базы данных SQL Azure с помощью PowerShell


> [AZURE.SELECTOR]
- [Обзор](sql-database-copy.md)
- [Портал Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

В этой статье показано, как с помощью PowerShell скопировать базу данных SQL на одном сервере, с одного сервера на другой или в [пул эластичных баз данных](sql-database-elastic-pool.md). Для операции копирования базы данных используется командлет [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx).


Для работы с этой статьей необходимо следующее:

- База данных SQL Azure (база данных, которая будет копироваться). Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
- Последняя версия Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


Многие новые функции Базы данных SQL поддерживаются только при использовании [модели развертывания с помощью Azure Resource Manager](../resource-group-overview.md), поэтому в примерах используются соответствующие [командлеты Azure PowerShell для Базы данных SQL](https://msdn.microsoft.com/library/azure/mt574084.aspx). Существующие [классические командлеты для Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/dn546723.aspx) в классической модели развертывания поддерживаются для обеспечения обратной совместимости. Мы рекомендуем использовать командлеты для Resource Manager.


>[AZURE.NOTE] Операция копирования может занять некоторое время в зависимости от размера базы данных.


## Копирование Базы данных SQL на тот же сервер

При создании копии на том же сервере, не указывайте параметр `-CopyServerName` (или задайте ему значение того же сервера).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## Копирование Базы данных SQL на другой сервер

При создании копии на другом сервере включите параметр `-CopyServerName` и задайте ему значение другого сервера. Сервер, на который выполняется *копирование*, должен уже существовать. Если он находится в другой группе ресурсов, то необходимо также включить параметр `-CopyResourceGroupName`.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## Копирование базы данных SQL в пул эластичных баз данных

Чтобы создать копию базы данных SQL в пуле, задайте параметру `-ElasticPoolName` значение существующего пула.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## Разрешение имен для входа

Сведения о разрешении имен для входа после завершения операции копирования см. в разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes).


## Пример сценария PowerShell

Следующий сценарий предполагает, что все группы ресурсов, серверы и пул уже существуют (замените значения переменных своими существующими ресурсами). Все должно существовать, за исключением копии базы данных.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## Дальнейшие действия

- В разделе [Копирование Базы данных SQL Azure](sql-database-copy.md) доступны общие сведения о копировании базы данных SQL Azure.
- В статье [Копирование базы данных SQL Azure с помощью портала Azure](sql-database-copy-portal.md) рассматривается копирование базы данных с помощью портала Azure.
- В разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md) рассматривается копирование базы данных с помощью Transact-SQL.
- В разделе [Как управлять безопасностью базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md) описывается управление пользователями и именами для входа при копировании базы данных на другой логический сервер.


## Дополнительные ресурсы

- [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [Управление именами для входа](sql-database-manage-logins.md)
- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)
- [Экспорт базы данных в BACPAC](sql-database-export.md)
- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)
- [Справочник по командлетам PowerShell для Базы данных SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0914_2016-->