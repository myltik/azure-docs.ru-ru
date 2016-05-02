<properties
	pageTitle="Практическое руководство. Настройка брандмауэра базы данных SQL Azure | Microsoft Azure"
	description="Сведения о настройке брандмауэра для IP-адресов, через которые осуществляется доступ к базам данных SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="02/04/2016"
	ms.author="rickbyh"/>


# Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью PowerShell


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)


Для подключения к серверам и базам данных в Базе данных SQL Microsoft Azure используются правила брандмауэра. На сервере Базы данных SQL Azure для главной или пользовательской базы данных можно определить значения параметров брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [AZURE.IMPORTANT] Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключения Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключения из Azure, см. в разделе [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md). Вам может потребоваться открыть некоторые дополнительные порты TCP при создании соединений внутри границ облака Azure. Дополнительные сведения см. в разделе **Версия 12 Базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и Базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Управление правилами брандмауэра на уровне сервера с помощью Azure PowerShell
1. Запустите Azure PowerShell.
2. Правила брандмауэра на уровне сервера можно создавать, обновлять и удалять с помощью Azure PowerShell. 

	Для создания нового правила брандмауэра на уровне сервера выполните командлет New-AzureSqlDatabaseServerFirewallRule. В следующем примере показано, как включить диапазон IP-адресов на сервере Contoso.
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	Для изменения существующего правила брандмауэра на уровне сервера выполните командлет Set-AzureSqlDatabaseServerFirewallRule. В следующем примере показано, как изменить диапазон допустимых IP-адресов для правила с именем ContosoFirewallRule.
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	Для удаления существующего правила брандмауэра на уровне сервера выполните командлет Remove-AzureSqlDatabaseServerFirewallRule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso


## Управление правилами брандмауэра с помощью PowerShell

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## Дальнейшие действия

Учебник по созданию базы данных см. в разделе [Создание первой базы данных SQL Azure](sql-database-get-started.md). Дополнительные сведения о подключении к Базе данных SQL Azure из приложений с открытым исходным кодом или приложений сторонних производителей см. в разделе [Рекомендации по подключению к Базе данных SQL Azure программными средствами](https://msdn.microsoft.com/library/azure/ee336282.aspx). Общие сведения о переходе к базам данных см. в разделе [Управление базами данных и учетными записями в Базе данных SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0420_2016-->