<properties
    pageTitle="Настройка новой базы данных SQL с помощью PowerShell | Microsoft Azure"
    description="Узнайте, как создать новую базу данных SQL с помощью PowerShell. Стандартные задачи по настройке базы данных можно выполнить с помощью командлетов PowerShell."
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
    ms.date="05/09/2016"
    ms.author="sstein"/>

# Создание новой базы данных SQL и стандартная настройка базы данных с использованием командлетов PowerShell


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Узнайте, как создать базу данных SQL с помощью командлетов PowerShell. Сведения о создании эластичных баз данных см. в статье [Создание пула эластичных баз данных с помощью PowerShell](sql-database-elastic-pool-create-powershell.md).


[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]

## Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра

Теперь вы можете запустить командлеты в рамках выбранной подписки Azure. Следующий шаг — установка группы ресурсов с сервером, на котором будет создана база данных. Вы можете отредактировать следующую команду, чтобы использовать любое допустимое расположение. Чтобы получить список допустимых расположений, выполните командлет **(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location**.

Выполните следующую команду, чтобы создать новую группу ресурсов:

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Когда группа ресурсов будет создана, на экране появится следующее сообщение: **ProvisioningState: Succeeded**.


### Создание сервера

Базы данных SQL создаются на серверах баз данных SQL Azure. Чтобы создать новый сервер, выполните командлет **New-AzureRmSqlServer**. Замените строку *ServerName* именем вашего сервера. Это имя должно быть уникальным в пределах всех серверов с базами данных SQL Azure. Если имя сервера уже используется, отобразится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут. Команду можно изменить, указав любое действительное расположение. Но вам нужно использовать то же расположение, в котором была создана группа ресурсов (см.выше).

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

При выполнении этой команды отображается запрос на ввод имени пользователя и пароля. Не указывайте учетные данные Azure. Вместо этого введите имя пользователя и пароль, которые станут учетными данными администратора нового сервера.

После успешного создания сервера на экране появятся сведения о сервере.

### Настройка серверного правила брандмауэра для доступа к серверу

Установите правило брандмауэра для доступа к серверу. Выполните следующую команду, заменив начальный и конечный IP-адреса значениями, используемыми на вашем компьютере.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

После успешного создания правила на экране появятся сведения о правиле брандмауэра.

Чтобы предоставить другим службам Azure доступ к серверу, добавьте правило брандмауэра и задайте для параметров StartIpAddress и EndIpAddress значение 0.0.0.0. Обратите внимание, что это разрешает доступ к серверу трафику Azure от любой подписки Azure.

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md).


## Создание новой базы данных SQL

Сейчас у вас есть группа ресурсов, сервер и правило брандмауэра, настроенные для получения доступа к серверу.

Следующая команда создает пустую базу данных SQL категории "Стандартный" с уровнем производительности S1:


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


После успешного создания базы данных отображаются сведения о базе данных.

## Создание новой базы данных SQL с помощью сценария PowerShell

Ниже приведен пример скрипта PowerShell для создания базы данных SQL.

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

- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md).


## Дополнительные ресурсы

- [база данных SQL Azure;](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0803_2016-->