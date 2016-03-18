<properties 
    pageTitle="Настройка новой базы данных SQL с помощью PowerShell | Microsoft Azure" 
    description="Узнайте, как создать новую базу данных SQL с помощью PowerShell. Стандартные задачи по настройке базы данных можно выполнить с помощью командлетов PowerShell." 
    keywords="создание новой базы данных SQL, настройка базы данных"
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="01/20/2016"
    ms.author="sstein"/>

# Создание новой базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell 

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


Узнайте, как создать новую базу данных SQL и выполнить стандартные задачи по ее настройке с использованием командлетов PowerShell.


Чтобы выполнять командлеты PowerShell, необходимо установить и запустить Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

- Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.


## Настройка учетных данных и выбор подписки

Теперь, после запуска модуля диспетчера ресурсов Azure, вам доступны все командлеты, необходимые для создания базы данных SQL.

Сначала вам необходимо установить доступ к своей учетной записи Azure, поэтому выполните указанный ниже командлет и введите свои учетные данные в открывшемся экране входа в систему. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

	Add-AzureRmAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам нужно знать ее идентификатор. Идентификатор можно скопировать из предыдущего шага или, если у вас несколько подписок, запустить командлет **Get-AzureRmSubscription** и скопировать необходимые сведения о подписке из набора результатов. Если у вас есть подписка, запустите следующий командлет:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

После успешного выполнения командлета **Select-AzureRmSubscription** вы вернетесь в командную строку PowerShell. Если подписок несколько, выполните командлет **Get-AzureRmSubscription** и убедитесь, что для подписки, которую вы хотите использовать, отображается информация **IsCurrent: True**.

## Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра

Теперь вы можете запустить командлеты под выбранной подпиской Azure, и следующим шагом станет установка группы ресурсов, содержащей сервер, на котором будет создаваться база данных. Вы можете отредактировать следующую команду, чтобы использовать любое допустимое расположение. Чтобы получить список допустимых расположений, выполните командлет **(Get-AzureRmLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations**.

Выполните следующую команду, чтобы создать новую группу ресурсов:

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

После успешного создания группы ресурсов на экране появится информация, содержащая текст **ProvisioningState: Succeeded**.


### Создание сервера 

Базы данных SQL создаются внутри серверов баз данных SQL Azure. Чтобы создать новый сервер, выполните командлет **New-AzureRmSqlServer**. Замените строку ServerName на имя вашего сервера. Это имя должно быть уникальным для всех серверов SQL Azure, и, если оно уже занято, появится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут. Команду можно изменить, указав любое действительное расположение, однако вам необходимо использовать то же расположение, в котором вы создали группу ресурсов на предыдущем этапе.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

При выполнении этой команды появится окно, запрашивающее **Имя пользователя** и **Пароль**. Это не учетные данные Azure; введите имя пользователя и пароль, которые станут учетными данными администратора нового сервера.

После успешного создания сервера на экране появятся сведения о сервере.

### Настройка серверного правила брандмауэра для доступа к серверу

Установите правило брандмауэра для доступа к серверу. Выполните следующую команду, заменив начальный и конечный IP-адреса значениями, допустимыми для вашего компьютера.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

После успешного создания правила на экране появятся сведения о правиле брандмауэра.

Чтобы разрешить другим службам Azure доступ к серверу, добавьте правило брандмауэра и задайте для параметров StartIpAddress и EndIpAddress значение 0.0.0.0. Обратите внимание, что это разрешает доступ к серверу трафику Azure от любой подписки Azure.

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md).


## Создание новой базы данных SQL

Сейчас у вас есть группа ресурсов, сервер и правило брандмауэра, настроенные для получения доступа к серверу.

Следующая команда создает новую (пустую) базу данных SQL с уровнем обслуживания Standard и уровнем производительности S1:


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


После успешного создания базы данных отображаются сведения о базе данных.

## Создание новой базы данных SQL с помощью сценария PowerShell

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Дальнейшие действия
Вы создали новую базу данных SQL и выполнили ее базовую настройку. Теперь можете перейти к следующим шагам:

- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)


## Дополнительные ресурсы

- [база данных SQL Azure;](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0302_2016-->