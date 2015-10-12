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
	ms.date="09/30/2015" 
	ms.author="sstein"/>

# Обновление до версии 12 Базы данных SQL с помощью PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


В этой статье описано, как обновить Базу данных SQL до версии 12 с помощью PowerShell.

В ходе обновления Базы данных SQL до версии 12 необходимо также обновить все базы данных Web и Business до нового уровня службы. Следующие указания включают использование рекомендаций по ценовым категориям и пулам эластичных баз данных для [обновления всех баз данных Web и Business](sql-database-upgrade-new-service-tiers.md) на сервере.


## Предварительные требования 

Для обновления сервера до версии 12 с помощью PowerShell необходимо иметь установленный и запущенный Azure PowerShell и перевести его в режим диспетчера ресурсов, чтобы получить доступ к командлетам PowerShell диспетчера ресурсов Azure.

Вы можете скачать и установить модуль Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

Командлеты для создания баз данных SQL Azure и управления ими находятся в модуле диспетчера ресурсов Azure. При запуске Azure PowerShell командлеты в модуле Azure импортируются по умолчанию. Для перехода в модуль диспетчера ресурсов Azure используйте командлет **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

На экране может появиться предупреждение «Командлет Switch-AzureMode является устаревшим и будет удален в следующих версиях». Его можно игнорировать и перейти к следующему разделу.

Дополнительную информацию можно узнать в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).



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

    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Дополнительные сведения см. в статьях [Рекомендации по выбору пула эластичных баз данных Базы данных SQL Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) и [Рекомендации по выбору ценовой категории для Базы данных SQL Azure](sql-database-service-tier-advisor.md).



## Начало обновления

Чтобы запустить обновление сервера, выполните следующий командлет:

    Start-AzureSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Выполнение этой команды запускает процесс обновления. Вы можете настроить вид этой рекомендации и передать измененную рекомендацию в командлет.


## Обновления сервера


    # Adding the account
    #
    Add-AzureAccount
    
    # Switch mode
    #
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


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
     
    # Creating single database mapping
    #
    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap.Name = "DBMain" 
    $databaseMap.TargetEdition = "Standard" 
    $databaseMap.TargetServiceLevelObjective = "S0" 
     
    # Starting the upgrade
    #
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool) 
    




- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)



## Сопутствующая информация

- [Командлеты диспетчера ресурсов Базы данных SQL Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)

<!---HONumber=Oct15_HO1-->