<properties 
	pageTitle="Обновление Azure SQL Server до версии 12 с помощью PowerShell" 
	description="Обновление Azure SQL Server до версии 12 с помощью PowerShell." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="sstein"/>

# Обновление до версии 12 Базы данных SQL с помощью PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


В этой статье описано, как обновить Базу данных SQL до версии 12 с помощью PowerShell.

В ходе обновления Базы данных SQL до версии 12 необходимо также обновить все базы данных Web и Business до нового уровня службы. Следующие указания включают использование рекомендаций по ценовым категориям и пулам эластичных баз данных для [обновления всех баз данных Web и Business](sql-database-upgrade-new-service-tiers.md) на сервере.


## Предварительные требования 

Для обновления сервера до версии 12 с помощью PowerShell необходимо иметь установленный и запущенный Azure PowerShell и перевести его в режим диспетчера ресурсов, чтобы получить доступ к командлетам PowerShell диспетчера ресурсов Azure.

> [AZURE.IMPORTANT]Начиная с выпуска предварительной версии Azure PowerShell 1.0, командлет Switch-AzureMode больше не доступен, а командлеты, которые были в модуле Azure ResourceManger, переименованы. В примерах в этой статье используется новое соглашение об именовании предварительной версии PowerShell 1.0. Дополнительные сведения см. в разделе [Устаревший командлет Switch-AzureMode в Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).

Чтобы выполнить командлеты PowerShell, необходимо установить и запустить Azure PowerShell. А из-за удаления Switch-AzureMode необходимо скачать и установить последнюю версию Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


## Настройка учетных данных и выбор подписки

Для запуска командлетов PowerShell по подписке Azure необходимо сначала настроить доступ к учетной записи Azure. Выполните следующую команду; откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

	Add-AzureAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.

Для выбора подписки вам понадобится идентификатор (**-SubscriptionId**) или имя подписки (**-SubscriptionName**). Идентификатор или имя можно скопировать из предыдущего шага или, если у вас несколько подписок, запустить командлет **Get-AzureSubscription** и скопировать необходимые сведения о подписке из набора результатов.

Выполните следующий командлет с данными о подписке для задания текущей подписки:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Следующие команды будет выполнены для заданной выше подписки.

## Получение рекомендаций

Для получения рекомендаций по обновлению сервера выполните следующий командлет:

    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Дополнительные сведения см. в статьях [Рекомендации по выбору пула эластичных баз данных Базы данных SQL Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) и [Рекомендации по выбору ценовой категории для Базы данных SQL Azure](sql-database-service-tier-advisor.md).



## Начало обновления

Чтобы запустить обновление сервера, выполните следующий командлет:

    Start-AzureRMSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Выполнение этой команды запускает процесс обновления. Вы можете настроить вид этой рекомендации и передать измененную рекомендацию в командлет.


## Обновления сервера


    # Adding the account
    #
    Add-AzureAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRMSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Настраиваемое сопоставление обновления

Если эти рекомендации не подходят для вашего сервера и организации, вы можете выбрать способ обновления ваших баз данных и сопоставить их с отдельными или эластичными базами данных.

Параметры ElasticPoolCollection и DatabaseCollection являются необязательными:
    
    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties 
    $elasticPool.DatabaseDtuMax = 100 
    $elasticPool.DatabaseDtuMin = 0 
    $elasticPool.Dtu = 800 
    $elasticPool.Edition = "Standard" 
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”) 
    $elasticPool.Name = "elasticpool_1" 
    $elasticPool.StorageMb = 800 
     
    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap1.Name = "DBMain1" 
    $databaseMap1.TargetEdition = "Standard" 
    $databaseMap1.TargetServiceLevelObjective = "S0"
    
    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap2.Name = "DBMain2" 
    $databaseMap2.TargetEdition = "Standard" 
    $databaseMap2.TargetServiceLevelObjective = "S2"
     
    # Starting the upgrade
    #
    Start-AzureRMSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    




## Сопутствующая информация

- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=Oct15_HO3-->