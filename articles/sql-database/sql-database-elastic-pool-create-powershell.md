<properties
    pageTitle="Создание пула эластичных баз данных с помощью PowerShell | Microsoft Azure"
    description="Узнайте, как создать масштабируемый пул эластичных баз данных с помощью PowerShell для горизонтального масштабирования ресурсов и управления несколькими базами данных SQL Azure."
	services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Создание пула эластичных баз данных с помощью PowerShell

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Узнайте, как создавать [пулы эластичных баз данных](sql-database-elastic-pool.md) и управлять ими с помощью командлетов PowerShell.

Стандартные коды ошибок см. в статье [Коды ошибок SQL для клиентских приложений базы данных SQL: ошибки подключения к базе данных и другие проблемы](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Сейчас пулы эластичных баз данных предоставляются в виде предварительной версии, которая доступна только с серверами Базы данных SQL версии 12. Если у вас есть сервер базы данных SQL версии 11, с помощью PowerShell вы можете в один шаг [обновить его до версии 12 и создать пул](sql-database-upgrade-server-portal.md).


Для работы вам понадобится Azure PowerShell 1.0 или более поздняя версия. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Создание пула

Командлет [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) создает новый пул. Значения eDTU на пул, а также минимальные и максимальные значения DTU ограничены значением уровня службы ("Базовый", "Стандартный" или "Премиум"). См. раздел [eDTU и размеры хранилища для эластичных баз данных и пулов эластичных баз данных](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Создание эластичной базы данных в пуле

Используйте командлет [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) и укажите целевой пул в качестве параметра **ElasticPoolName**. Сведения о перемещении существующей базы данных в пул см. в разделе [Перемещение базы данных в пул эластичных БД](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Создание пула и его заполнение несколькими новыми базами данных 

Создание большого количества баз данных в пуле может занять некоторое время, если эта операция выполняется с помощью портала или командлетов PowerShell, которые создают базы данных поочередно. Сведения об автоматизации создания баз данных в новом пуле см. в документации [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## Пример. Создание пула с помощью PowerShell 

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



## Дальнейшие действия

- [Управление пулом.](sql-database-elastic-pool-manage-powershell.md)
- [Обзор заданий обработки эластичных баз данных](sql-database-elastic-jobs-overview.md). Эти задания упрощают выполнение сценариев T-SQL для любого количества эластичных баз данных в пуле.
- [Общие сведения о возможностях эластичных баз данных](sql-database-elastic-scale-introduction.md). Использование средств эластичных баз данных для масштабирования.

<!---HONumber=AcomDC_0601_2016-->