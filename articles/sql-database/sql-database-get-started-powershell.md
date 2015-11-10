<properties 
    pageTitle="Создание базы данных SQL Azure с помощью PowerShell" 
    description="Создание базы данных SQL Azure с помощью PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="10/20/2015"
    ms.author="sstein"/>

# Создание базы данных SQL с помощью PowerShell

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


## Обзор

В этой статье описано, как создать базу данных SQL с помощью PowerShell.

> [AZURE.IMPORTANT]Начиная с выпуска предварительной версии Azure PowerShell 1.0, командлет Switch-AzureMode больше не доступен, а командлеты, которые были в модуле Azure ResourceManger, переименованы. В примерах в этой статье используется новое соглашение об именовании предварительной версии PowerShell 1.0. Дополнительные сведения см. в разделе [Устаревший командлет Switch-AzureMode в Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


Чтобы выполнить командлеты PowerShell, необходимо установить и запустить Azure PowerShell. А из-за удаления Switch-AzureMode необходимо скачать и установить последнюю версию Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

- Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.


## Настройка учетных данных и выбор подписки

Теперь, после запуска модуля диспетчера ресурсов Azure, вам доступны все командлеты, необходимые для создания базы данных SQL.

Сначала вам необходимо установить доступ к своей учетной записи Azure, поэтому выполните указанный ниже командлет и введите свои учетные данные в открывшемся экране входа в систему. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

	Add-AzureRMAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам нужно знать ее идентификатор. Идентификатор или имя можно скопировать из предыдущего шага или, если у вас несколько подписок, запустить командлет **Get-AzureRMSubscription** и скопировать необходимые сведения о подписке из набора результатов. Если у вас есть подписка, запустите следующий командлет:

	Select-AzureRMSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

После успешного выполнения командлета **Select-AzureRMSubscription** вы вернетесь в командную строку PowerShell. Если подписок несколько, выполните командлет **Get-AzureRMSubscription** и убедитесь в том, что в подписке, которую необходимо использовать, отображается **IsCurrent: True**.

## Создание группы ресурсов, сервера и правила брандмауэра

Теперь вы можете запустить командлеты под выбранной подпиской Azure, и следующим шагом станет установка группы ресурсов, содержащей сервер, на котором будет создаваться база данных. Вы можете отредактировать следующую команду, чтобы использовать любое допустимое расположение. Выполните команду **(Get-AzureRMLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** для получения списка допустимых расположений.

Выполните следующую команду, чтобы создать новую группу ресурсов:

	New-AzureRMResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

После успешного создания новой группы ресурсов на экране появится информация, содержащая текст **ProvisioningState : Succeeded**.


### Создание сервера 

Базы данных SQL создаются внутри серверов баз данных SQL Azure. Выполните командлет **New-AzureRMSqlServer**, чтобы создать новый сервер. Замените строку ServerName на имя вашего сервера. Это имя должно быть уникальным для всех серверов SQL Azure, и, если оно уже занято, появится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут. Команду можно изменить, указав любое действительное расположение, однако вам необходимо использовать то же расположение, в котором вы создали группу ресурсов на предыдущем этапе.

	New-AzureRMSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

При выполнении этой команды появится окно, запрашивающее **Имя пользователя** и **Пароль**. Это не учетные данные Azure; введите имя пользователя и пароль, которые станут учетными данными администратора нового сервера.

После успешного создания сервера на экране появятся сведения о сервере.

### Настройка серверного правила брандмауэра для доступа к серверу

Установите правило брандмауэра для доступа к серверу. Выполните следующую команду, заменив начальный и конечный IP-адреса значениями, допустимыми для вашего компьютера.

	New-AzureRMSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

После успешного создания правила на экране появятся сведения о правиле брандмауэра.

Чтобы разрешить другим службам Azure доступ к серверу, добавьте правило брандмауэра и задайте для параметров StartIpAddress и EndIpAddress значение 0.0.0.0. Обратите внимание, что это разрешает доступ к серверу трафику Azure от любой подписки Azure.

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md).


## Создание базы данных SQL

Сейчас у вас есть группа ресурсов, сервер и правило брандмауэра, настроенные для получения доступа к серверу.

Следующая команда создает новую (пустую) базу данных SQL с уровнем обслуживания Standard и уровнем производительности S1:


	New-AzureRMSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


После успешного создания базы данных отображаются сведения о базе данных.

## Создание базы данных SQL с помощью сценария PowerShell

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
    
    
    Add-AzureRMAccount
    Select-AzureRMSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRMResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRMSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRMSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Дальнейшие действия

- [Соединение с SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)


## Дополнительные ресурсы

- [База данных SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Nov15_HO2-->