<properties
	pageTitle="Практическое руководство. Настройка параметров брандмауэра | Microsoft Azure"
	description="Сведения о настройке брандмауэра для IP-адресов, через которые осуществляется доступ к базам данных SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="09/04/2015"
	ms.author="rickbyh"/>


# Практическое руководство. Настройка параметров брандмауэра для Базы данных SQL


Для подключения к серверам и базам данных в Базе данных SQL Microsoft Azure используются правила брандмауэра. На сервере Базы данных SQL Azure для главной или пользовательской базы данных можно определить значения параметров брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [AZURE.IMPORTANT]Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключения Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключения из Azure, см. в статье [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md). Вам может потребоваться открыть некоторые дополнительные порты TCP при создании соединений внутри границ облака Azure. Дополнительные сведения см. в разделе **Версия 12 Базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и Базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Правила брандмауэра на уровне сервера

Для создания правил брандмауэра на уровне сервера и управления ими можно использовать портал управления Microsoft Azure, Transact-SQL, Azure PowerShell или REST API.

### Управление правилами брандмауэра на уровне сервера с помощью нового портала Azure


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## Управление правилами брандмауэра на уровне сервера с помощью портала управления 

1. На портале управления выберите элемент **Базы данных SQL**. Появляется список всех баз данных и их соответствующих серверов.
2. В верхней части страницы щелкните **Серверы**.
3. Щелкните стрелку рядом с сервером, для которого требуется выполнить операции управления правилами брандмауэра.
4. В верхней части страницы щелкните **Настроить**.

	*  Чтобы добавить текущий компьютер, нажмите кнопку "Добавить в разрешенные IP-адреса".
	*  Чтобы добавить дополнительные IP-адреса, введите имя правила, начальный IP-адрес и конечный IP-адрес.
	*  Чтобы изменить существующее правило, измените значения во всех полях в данном правиле.
	*  Чтобы удалить существующее правило, наведите указатель мыши на правило и дождитесь появления X в конце строки. Щелкните X, чтобы удалить правило.
5. Чтобы сохранить изменения, в нижней части страницы нажмите кнопку **Сохранить**.

## Управление правилами брандмауэра на уровне сервера с помощью Transact-SQL

1. Откройте окно запроса с помощью портала управления или среды SQL Server Management Studio.
2. Убедитесь, что вы подключены к главной базе данных (master).
3. В окне запроса можно создавать, обновлять или удалять правила брандмауэра на уровне сервера.
4. Для создания или обновления правил брандмауэра на уровне сервера выполните хранимую процедуру sp\_set\_firewall\_rule. В следующем примере показано, как задать диапазон IP-адресов на сервере Contoso.<br/>Для начала узнайте, какие правила уже имеются.

		SELECT * FROM sys.database_firewall_rules ORDER BY name;

	Добавьте правило брандмауэра.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Для удаления правил брандмауэра на уровне сервера выполните хранимую процедуру sp\_delete\_firewall\_rule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
## Управление правилами брандмауэра на уровне сервера с помощью Azure PowerShell
1. Запустите Azure PowerShell.
2. Правила брандмауэра на уровне сервера можно создавать, обновлять и удалять с помощью Azure PowerShell. 

	Для создания нового правила брандмауэра на уровне сервера выполните командлет New-AzureSqlDatabaseServerFirewallRule. В следующем примере показано, как включить диапазон IP-адресов на сервере Contoso.
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	Для изменения существующего правила брандмауэра на уровне сервера выполните командлет Set-AzureSqlDatabaseServerFirewallRule. В следующем примере показано, как изменить диапазон допустимых IP-адресов для правила с именем ContosoFirewallRule.
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	Для удаления существующего правила брандмауэра на уровне сервера выполните командлет Remove-AzureSqlDatabaseServerFirewallRule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso
 
## Управление правилами брандмауэра на уровне сервера с помощью REST API
1. Запрос на управление правилами брандмауэра с помощью REST API должен пройти проверку подлинности. Дополнительные сведения см. в разделе "Проверка подлинности запросов управления службами".
2. Правила на уровне сервера можно создавать, обновлять или удалять с помощью REST API

	Для создания или обновления правила брандмауэра на уровне сервера выполните метод POST следующим образом:
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	Текст запроса

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	Для удаления существующего правила брандмауэра на уровне сервера выполните метод DELETE следующим образом:
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule
 
## Правила брандмауэра на уровне базы данных

1. После создания брандмауэра на уровне сервера для своего IP-адреса откройте окно запроса на портале управления или с помощью среды SQL Server Management Studio.
2. Подключение к базе данных, для которой нужно создать правило брандмауэра на уровне базы данных.

	Для создания нового или обновления существующего правила брандмауэра на уровне базы данных выполните хранимую процедуру sp\_set\_database\_firewall\_rule. В следующем примере показано, как создать новое правило с именем ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Для удаления существующего правила брандмауэра на уровне базы данных выполните хранимую процедуру sp\_delete\_database\_firewall\_rule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## Управление правилами брандмауэра с помощью интерфейса API REST управления службой

* [Создание правила брандмауэра](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Удаление правила брандмауэра](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Получение правила брандмауэра](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [Вывод списка правил брандмауэра](https://msdn.microsoft.com/library/azure/dn505715.aspx)

## Управление правилами брандмауэра с помощью PowerShell

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## Дальнейшие действия

Руководство по созданию баз данных см. в статье [Создание своей первой Базы данных SQL Azure](sql-database-get-started.md). Дополнительные сведения о подключении к Базе данных Azure SQL из приложений с открытым исходным кодом или приложений сторонних производителей см. в статье [Рекомендации по подключению к Базе данных SQL Azure программными средствами](https://msdn.microsoft.com/library/azure/ee336282.aspx). Общие сведения о переходе к базам данных см. в статье [Управление базами данных и именами для входа в Базе данных SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=Sept15_HO4-->