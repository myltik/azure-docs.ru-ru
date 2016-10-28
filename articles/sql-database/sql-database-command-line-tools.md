<properties
	pageTitle="Управление базой данных SQL Azure с помощью PowerShell | Microsoft Azure"
	description="Управление базой данных SQL Azure с помощью PowerShell."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="sstein"/>

# Управление базой данных SQL Azure с помощью PowerShell


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

В этой статье представлены командлеты PowerShell, которые используются для выполнения различных задач Базы данных SQL Azure. Полный список см. в статье [Azure SQL Database Cmdlets](https://msdn.microsoft.com/library/mt574084.aspx) (Командлеты Базы данных SQL Azure).


## Создание группы ресурсов

Создайте группу ресурсов для Базы данных SQL и связанных ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837.aspx).

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md). Пример сценария см. в разделе [Создание базы данных SQL с помощью сценария PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## Создание сервера Базы данных SQL

Создайте сервер Базы данных SQL с помощью командлета [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Замените *server1* именем своего сервера. Имена серверов должны быть уникальными по всем серверам Базы данных SQL Azure. Если имя сервера уже используется, отобразится сообщение об ошибке. Выполнение этой команды может занять несколько минут. В подписке уже должна существовать группа ресурсов.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Дополнительные сведения см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md). Пример сценария см. в разделе [Создание базы данных SQL с помощью сценария PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Создание правила брандмауэра для сервера Базы данных SQL

Создайте правило брандмауэра для доступа к серверу, используя командлет [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Выполните следующую команду, заменив начальный и конечный IP-адреса значениями для своего клиента. В подписке уже должны существовать группа ресурсов и сервер.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Чтобы другие службы Azure могли иметь доступ к серверу, создайте правило брандмауэра и задайте адресам `-StartIpAddress` и `-EndIpAddress` значение **0.0.0.0**. Это специальное правило брандмауэра разрешит всему трафику Azure доступ к серверу.

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Пример сценария см. в разделе [Создание базы данных SQL с помощью сценария PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Создание базы данных SQL (пустой)

Создайте базу данных с помощью командлета [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). В подписке уже должны существовать группа ресурсов и сервер.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Дополнительные сведения см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md). Пример сценария см. в разделе [Создание базы данных SQL с помощью сценария PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Изменение уровня производительности базы данных SQL

Выполните масштабирование базы данных с помощью командлета [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). В подписке уже должны существовать группа ресурсов, сервер и база данных. Задайте параметру `-RequestedServiceObjectiveName` единое пространство (как показано в следующем фрагменте кода) для категории "Базовый". Задайте ему значения *S0*, *S1*, *P1*, *P6* и т. д., как в предыдущем примере для других категорий.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Дополнительные сведения см. в статье [Параметры базы данных SQL и производительность: возможности разных уровней служб](sql-database-service-tiers.md). Пример сценария см. в разделе [Пример скрипта PowerShell для изменения уровня обслуживания и уровня производительности базы данных SQL](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## Копирование Базы данных SQL на тот же сервер

Скопируйте базу данных SQL на тот же сервер с помощью командлета [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644.aspx). Параметрам `-CopyServerName` и `-CopyResourceGroupName` задайте значения, соответствующие вашему серверу базы данных-источника и исходной группе ресурсов.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Дополнительные сведения см. в статье [Копирование базы данных SQL Azure](sql-database-copy.md). Пример сценария см. в разделе [Пример сценария PowerShell](sql-database-copy-powershell.md#example-powershell-script).


## Удаление базы данных SQL

Для удаления базы данных SQL воспользуйтесь командлетом [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). В подписке уже должны существовать группа ресурсов, сервер и база данных.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## Удаление сервера Базы данных SQL

Для удаления сервера воспользуйтесь командлетом [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx).

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## Создание пулов эластичных баз данных и управление ими с помощью PowerShell

Сведения о создании пулов эластичных баз данных с помощью PowerShell см. в статье [Создание пула эластичных баз данных с помощью PowerShell](sql-database-elastic-pool-create-powershell.md).

Сведения об управлении пулами эластичных баз данных с помощью PowerShell см. в статье [Мониторинг пула эластичных баз данных и управление им с помощью PowerShell](sql-database-elastic-pool-manage-powershell.md).



## Связанные сведения

- [Командлеты Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Справка по командлетам Azure](https://msdn.microsoft.com/library/azure/dn708514.aspx)

<!---HONumber=AcomDC_0914_2016-->