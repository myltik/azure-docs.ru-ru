---
title: "Копирование базы данных SQL Azure с помощью PowerShell | Документация Майкрософт"
description: "Создание копии Базы данных SQL Azure с помощью PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6d9839d7-9303-48d2-be0f-21ce84f95a94
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 09/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: edfbc0d94d9d5b33a25788772a98173187e394b8
ms.openlocfilehash: 031a17d179cee8d6cceb9ed3aefd993add1958b8
ms.lasthandoff: 02/11/2017


---
# <a name="copy-an-azure-sql-database-using-powershell"></a>Копирование базы данных SQL Azure с помощью PowerShell

В этой статье показано, как с помощью PowerShell скопировать базу данных SQL на тот же сервер, на другой сервер или в [эластичный пул](sql-database-elastic-pool.md). Для операции копирования базы данных используется командлет [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx) . 

> [!NOTE]
> Кроме того, это можно сделать с помощью [портала Azure](sql-database-copy-portal.md) или [Transact-SQL](sql-database-copy-transact-sql.md).
>

Для работы с этой статьей необходимо следующее:

* База данных SQL Azure (база данных, которая будет копироваться). Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
* Последняя версия Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

Многие новые функции базы данных SQL поддерживаются только при использовании [модели развертывания с помощью Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), поэтому в примерах используются соответствующие [командлеты Azure PowerShell для базы данных SQL](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx). Существующие [классические командлеты для Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) в классической модели развертывания поддерживаются для обеспечения обратной совместимости. Мы рекомендуем использовать командлеты для Resource Manager.

> [!NOTE]
> Операция копирования может занять некоторое время в зависимости от размера базы данных.
> 
> 

## <a name="copy-a-sql-database-to-the-same-server"></a>Копирование Базы данных SQL на тот же сервер
При создании копии на том же сервере, не указывайте параметр `-CopyServerName` (или задайте ему значение того же сервера).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Копирование Базы данных SQL на другой сервер
При создании копии на другом сервере включите параметр `-CopyServerName` и задайте ему значение другого сервера. Сервер, на который выполняется *копирование* , должен уже существовать. Если он находится в другой группе ресурсов, то необходимо также включить параметр `-CopyResourceGroupName` .

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-pool"></a>Копирование базы данных SQL в эластичный пул
Чтобы создать копию базы данных SQL в пуле, задайте параметру `-ElasticPoolName` значение существующего пула.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Разрешение имен для входа
Сведения о разрешении имен для входа после завершения операции копирования см. в разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes).

## <a name="example-powershell-script"></a>Пример сценария PowerShell
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

    # Copy a database into an elastic pool
    # ---------------------------------------------
    $poolName = "pool1"

    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName





## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об управлении пользователями и именами для входа при копировании базы данных на другой логический сервер см. в статье [Настройка безопасности Базы данных SQL Azure и управление ею для геовосстановления или отработки отказа](sql-database-geo-replication-security-config.md).
* Рекомендации по экспорту базы данных в BACPAC-файл с помощью PowerShell см. в [этой статье](sql-database-export-powershell.md).
* [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
* [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687\(v=azure.300\).aspx)
* [Управление именами для входа](sql-database-manage-logins.md)
* [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)
* [Экспорт базы данных в BACPAC](sql-database-export.md)
* [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
* [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)
* [Справочник по командлетам PowerShell для Базы данных SQL Azure](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)


