<properties 
	pageTitle="Управление ресурсами Базы данных SQL Azure с помощью PowerShell" 
	description="Управление ресурсами Базы данных SQL с помощью командной строки" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="vinsonyu"/>

# Управление ресурсами Базы данных SQL Azure с помощью PowerShell


В этом разделе мы с помощью сценария PowerShell создадим логический сервер, базу данных и правило брандмауэра в Базе данных SQL Azure.

## Шаг 1. Установка пакета Azure SDK

Чтобы установить Azure PowerShell на локальном компьютере, воспользуйтесь инструкциями в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md). После этого откройте командную строку Azure PowerShell.


## Шаг 2. Настройка скриптов PowerShell
Этот скрипт PowerShell создает сервер, базу данных и правило брандмауэра для сервера.


1. Скопируйте следующий блок командлетов PowerShell в текстовый редактор.
		
		
		Add-AzureAccount #Only needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "<Service Admin Login>" -AdministratorLoginPassword "<ServerLoginPassword>" -Location "<Location>" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "<Database1>" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "<FirewallRuleName>" -StartIpAddress "<IP4StartRange>" -EndIpAddress "<IP4EndRange>" -verbose

2. Замените значения в угловых скобках < > на нужные вам. Чтобы получить список допустимых расположений сервера Базы данных SQL Azure, запустите следующие командлеты в командной строке Azure Powershell.

		Switch-AzureMode -Name AzureResourceManager
		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

##Шаг 3. Выполнение сценария PowerShell

Проверьте отредактированные на втором шаге командлеты Azure PowerShell.

Скопируйте и вставьте их в командную строку Azure PowerShell. Командлеты будут выполнены как последовательность команд PowerShell, в результате чего в Базе данных SQL Azure будут созданы сервер, база данных и правило брандмауэра сервера.

Если вы планируете создавать такие же или подобные им ресурсы SQL Azure снова, можно:

- сохранить блок командлетов как файл скрипта PowerShell (*.ps1);
- сохранить его как Runbook службы автоматизации Azure в разделе «Автоматизация» на портале управления Azure. 

##Пример

Этот сценарий PowerShell создает ресурсы в расположении West US.

		Add-AzureAccount #Needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "admin" -AdministratorLoginPassword "P@ssword" -Location "West US" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "Database1" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "MyFirewallRule" -StartIpAddress "192.168.1.1" -EndIpAddress "192.168.1.1" -verbose

##Ресурсы

Для получения дополнительных сведений о командлетах PowerShell для SQL Azure [щелкните здесь](https://msdn.microsoft.com/library/dn546726.aspx).

<!---HONumber=58-->