<properties
    pageTitle="Настройка новой базы данных SQL с помощью PowerShell | Microsoft Azure"
    description="Узнайте, как создать базу данных SQL с помощью PowerShell. Стандартные задачи по настройке базы данных можно выполнить с помощью командлетов PowerShell."
    keywords="создание новой базы данных SQL, настройка базы данных"
	services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# Создание базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Узнайте, как создать базу данных SQL с помощью командлетов PowerShell. Сведения о создании эластичных баз данных см. в статье [Создание пула эластичных баз данных с помощью PowerShell](sql-database-elastic-pool-create-powershell.md).


[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]

## Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра

Теперь вы можете запустить командлеты в рамках выбранной подписки Azure. Следующий шаг — настройка группы ресурсов с сервером, на котором будет создана база данных. Вы можете отредактировать следующую команду, чтобы использовать любое допустимое расположение. Чтобы получить список допустимых расположений, выполните командлет **(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location**.

Выполните следующую команду, чтобы создать группу ресурсов:

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### Создание сервера

Базы данных SQL создаются на серверах баз данных SQL Azure. Чтобы создать сервер, выполните командлет **New-AzureRmSqlServer**. Это имя должно быть уникальным в пределах всех серверов с базами данных SQL Azure. Если имя сервера уже используется, отобразится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут. Команду можно изменить, указав любое действительное расположение. Но вам нужно использовать то же расположение, в котором была создана группа ресурсов (см.выше).

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

При выполнении этой команды отображается запрос на ввод имени пользователя и пароля. Не указывайте учетные данные Azure. Вместо этого введите имя пользователя и пароль, чтобы создать администратора сервера. В скрипте в конце статьи показано, как указать учетные данные сервера в коде.

После успешного создания сервера на экране появятся сведения о сервере.

### Настройка серверного правила брандмауэра для доступа к серверу

Чтобы получить доступ к серверу, настройте правило брандмауэра. Выполните следующую команду, заменив начальный и конечный IP-адреса значениями, используемыми на вашем компьютере.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

После успешного создания правила на экране появятся сведения о правиле брандмауэра.

Чтобы предоставить другим службам Azure доступ к серверу, добавьте правило брандмауэра и задайте для параметров StartIpAddress и EndIpAddress значение 0.0.0.0. Это правило включает для сервера трафик Azure от любой подписки Azure.

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md).


## Создание базы данных SQL

Сейчас у вас есть группа ресурсов, сервер и правило брандмауэра, настроенные для получения доступа к серверу.

Следующая команда создает пустую базу данных SQL категории "Стандартный" с уровнем производительности S1:


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


После успешного создания базы данных отображаются сведения о базе данных.

## Создание базы данных SQL с помощью сценария PowerShell

Следующий скрипт PowerShell создает базу данных SQL и все зависимые ресурсы. Замените все значения `{variables}` значениями, которые относятся к подписке и ресурсам (при вводе удалите **{}**).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## Дальнейшие действия
Вы создали базу данных SQL и выполнили ее базовую настройку. Теперь можно перейти к следующим шагам:

- [Управление базой данных SQL Azure с помощью PowerShell](sql-database-manage-powershell.md)
- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)


## дополнительные ресурсы.

- [база данных SQL Azure;](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1005_2016-->