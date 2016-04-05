<properties
    pageTitle="Создание пула эластичных баз данных (PowerShell) | Microsoft Azure"
    description="Узнайте, как создать пул масштабируемых эластичных баз данных с помощью PowerShell для горизонтального масштабирования ресурсов и управления для нескольких баз данных."
	services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="03/27/2016"
    ms.author="sstein"/>

# Создание пула эластичных баз данных с помощью PowerShell

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Узнайте, как создавать [пулы эластичных баз данных](sql-database-elastic-pool.md) и управлять ими с помощью командлетов PowerShell.

Стандартные коды ошибок см. в статье [Коды ошибок SQL для клиентских приложений базы данных SQL: ошибки подключения к базе данных и другие проблемы](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Сейчас пулы эластичных баз данных предоставляются в виде предварительной версии, которая доступна только с серверами Базы данных SQL версии 12. Если у вас есть сервер базы данных SQL версии 11, с помощью PowerShell вы можете в один шаг [обновить его до версии 12 и создать пул](sql-database-upgrade-server-portal.md).


Для работы вам понадобится Azure PowerShell 1.0 или более поздняя версия. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Создание пула

Командлет [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) создает пул.

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Создание эластичной базы данных в пуле

Чтобы создать базу данных непосредственно в пуле, выполните командлет [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) с заданным параметром **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Перемещение автономной базы данных в пул

Чтобы переместить существующую базу данных в пул, выполните командлет [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) с заданным параметром **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Создайте примера пула PowerShell

Этот сценарий создает новый сервер, поэтому когда появится запрос ввести имя пользователя и пароль, введите учетные данные администратора нового сервера (не учетные данные Azure).

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
- [Создание заданий обработки эластичных БД](sql-database-elastic-jobs-overview.md). Эти задания упрощают выполнение сценариев T-SQL для любого количества эластичных баз данных в пуле.


## Справка по эластичным базам данных

Дополнительные сведения об эластичных базах данных и пулах см. в статье [Справочник по пулу эластичных баз данных для базы данных SQL](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0330_2016-->