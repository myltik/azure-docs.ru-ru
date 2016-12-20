---
title: "Создание пула эластичных баз данных с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как создать масштабируемый пул эластичных баз данных с помощью PowerShell для горизонтального масштабирования ресурсов и управления несколькими базами данных SQL Azure."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 37a707ee-9223-43ae-8c35-1ccafde8b83e
ms.service: sql-database
ms.custom: sharded databases pool
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: a877c17a503e58c49ae781aed61ed120d069c737
ms.openlocfilehash: 19ea620789b31ee68d8e34acd161eeb820947b23


---
# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Создание пула эластичных баз данных с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал Azure](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
>
>

Узнайте, как создавать [пулы эластичных баз данных](sql-database-elastic-pool.md) и управлять ими с помощью командлетов PowerShell.

Стандартные коды ошибок см. в статье [Коды ошибок SQL для клиентских приложений базы данных SQL: ошибки подключения к базе данных и другие проблемы](sql-database-develop-error-messages.md).

> [!NOTE]
> Пулы эластичных БД общедоступны во всех регионах Azure, кроме северо-центрального региона США и западной Индии, в которых сейчас доступна только предварительная версия.  Общедоступная версия появится в этих регионах в ближайшее время. Кроме того, в настоящее время пулы эластичных БД не поддерживают базы данных, использующие [выполняющуюся в памяти OLTP или аналитику](sql-database-in-memory.md).
>
>

Для работы вам понадобится Azure PowerShell 1.0 или более поздняя версия. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="create-a-new-pool"></a>Создание пула
Командлет [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) создает новый пул. Значения eDTU на пул, а также минимальные и максимальные значения DTU ограничены значением уровня службы ("Базовый", "Стандартный" или "Премиум"). См. раздел [Ограничения eDTU и хранилища для пулов эластичных БД и эластичных баз данных](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Создание эластичной базы данных в пуле
Используйте командлет [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) и укажите целевой пул в качестве параметра **ElasticPoolName**. Сведения о перемещении существующей базы данных в пул см. в разделе [Перемещение базы данных в пул эластичных БД](sql-database-elastic-pool-manage-powershell.md#move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Создание пула и его заполнение несколькими новыми базами данных
Создание большого количества баз данных в пуле может занять некоторое время, если эта операция выполняется с помощью портала или командлетов PowerShell, которые создают базы данных поочередно. Сведения об автоматизации создания баз данных в новом пуле см. в документации [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Пример. Создание пула с помощью PowerShell
Этот скрипт создает группу ресурсов Azure и сервер. При появлении запроса укажите имя и пароль пользователя-администратора для нового сервера (не свои учетные данные Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Дальнейшие действия
* [Управление пулом.](sql-database-elastic-pool-manage-powershell.md)
* [Управление масштабируемыми облачными базами данных](sql-database-elastic-jobs-overview.md). Задания обработки для эластичных баз данных упрощают выполнение сценариев T-SQL для любого количества баз данных в пуле.
* [Развертывание с помощью базы данных SQL Azure](sql-database-elastic-scale-introduction.md). Использование средств эластичных баз данных для масштабирования.



<!--HONumber=Dec16_HO2-->


