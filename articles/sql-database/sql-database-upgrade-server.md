<properties 
	pageTitle="Обновление Azure SQL Server до версии 12 с помощью PowerShell" 
	description="Обновление Azure SQL Server до версии 12 с помощью PowerShell" 
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
	ms.date="09/05/2015" 
	ms.author="sstein"/>

# Обновление Azure SQL Server до версии 12 с помощью PowerShell
 

В этой статье показано, как обновить сервер баз данных SQL до версии 12, используя рекомендуемую ценовую категорию и пул эластичных БД.



## Предварительные требования 

Для обновления сервера до версии 12 с помощью PowerShell необходимо иметь установленный и запущенный Azure PowerShell и перевести его в режим диспетчера ресурсов, чтобы получить доступ к командлетам PowerShell диспетчера ресурсов Azure.

Вы можете скачать и установить модуль Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

Командлеты для создания баз данных SQL Azure и управления ими находятся в модуле диспетчера ресурсов Azure. При запуске Azure PowerShell командлеты в модуле Azure импортируются по умолчанию. Для перехода в модуль диспетчера ресурсов Azure используйте командлет **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

На экране может появиться предупреждение «Командлет Switch-AzureMode является устаревшим и будет удален в следующих версиях». Его можно игнорировать и перейти к следующему разделу.

Дополнительную информацию можно узнать в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).



## Настройка учетных данных

Для запуска командлетов PowerShell по подписке Azure необходимо сначала настроить доступ к учетной записи Azure. Выполните следующую команду; откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

	Add-AzureAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


## Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор (**-SubscriptionId**) или имя подписки (**-SubscriptionName**). Идентификатор или имя можно скопировать из предыдущего шага или, если у вас несколько подписок, запустить командлет **Get-AzureSubscription** и скопировать необходимые сведения о подписке из набора результатов.

Выполните следующий командлет с данными о подписке для задания текущей подписки:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Следующие команды будет выполнены для заданной выше подписки.

## Получение рекомендаций

Для получения рекомендаций по обновлению сервера выполните следующий командлет:

    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Дополнительные сведения см. в статьях [Рекомендации по выбору пула эластичных баз данных SQL](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) и [Рекомендации по выбору ценовой категории для базы данных SQL](sql-database-service-tier-advisor.md).



## Начало обновления

Чтобы запустить обновление сервера, выполните следующий командлет:

    Start-AzureSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Выполнение этой команды запускает процесс обновления. Вы можете настроить вид этой рекомендации и передать измененную рекомендацию в командлет.


## Обновление сервера Azure SQL


    # Adding the account
    #
    Add-AzureAccount
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName 
    $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Настраиваемое сопоставление обновления

Если эти рекомендации не подходят для вашего сервера и организации, вы можете выбрать способ обновления ваших баз данных и сопоставить их с отдельными или эластичными базами данных.

Обновление баз данных в пул эластичных баз данных:

    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100  
    $elasticPool.DatabaseDtuMin = 0  
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"  
    $elasticPool.DatabaseCollection = ("DB1")  
    $elasticPool.Name = "elasticpool_1"  


Обновление баз данных в отдельную базу данных:

    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties  $databaseMap.Name = "DB2"
    $databaseMap.TargetEdition = "Standard"
    $databaseMap.TargetServiceLevelObjective = "S0"
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool)
    




## Сопутствующая информация

- [Командлеты диспетчера ресурсов Базы данных SQL Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Командлеты управления службой Базы данных SQL Azure](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=Sept15_HO2-->