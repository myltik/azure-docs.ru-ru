<properties
	pageTitle="Практическое руководство. Настройка параметров брандмауэра (база данных Azure SQL)"
	description="настройка брандмауэра для баз данных Azure SQL"
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
	ms.topic="get-started-article" 
	ms.date="06/22/2015"
	ms.author="rickbyh"/>


# Практическое руководство. Настройка параметров брандмауэра (база данных Azure SQL)

 Для подключения к серверам и базам данных в базе данных SQL Microsoft Azure используются правила брандмауэра. На сервере базы данных SQL Azure для главной или пользовательской базы данных можно определить значения параметров брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

**Важно**. Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключение к Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключение из службы Azure, см. в разделе [Брандмауэр базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Правила брандмауэра на уровне сервера

Для создания правил брандмауэра на уровне сервера и управления ими можно использовать портал управления Microsoft Azure, Transact-SQL, Azure PowerShell или REST API.

### Управление правилами брандмауэра на уровне сервера с помощью нового портала Azure
1. Перейдите на портал Azure по адресу https://portal.azure.com и войдите с помощью учетной записи администратора Azure или участника.
2. В левом баннере нажмите кнопку "Просмотреть все", прокрутите вниз и выберите серверы SQL.
3. Щелкните сервер, для которого необходимо настроить правила брандмауэра, в отображаемом списке серверов SQL Server.

	![брандмауэр][1]

4. В колонке сервера щелкните "Параметры" наверху и нажмите кнопку "Брандмауэр", чтобы открыть колонку "Параметры брандмауэра" для сервера.
5. Добавление или изменение правила брандмауэра.

	* Чтобы добавить IP-адрес текущего компьютера, щелкните **Добавить IP-адрес клиента** в верхней части колонки.
	* Чтобы добавить дополнительные IP-адреса, введите **ИМЯ ПРАВИЛА**, **НАЧАЛЬНЫЙ IP-АДРЕС** и **КОНЕЧНЫЙ IP-АДРЕС**.
	* Чтобы изменить существующее правило, измените значения во всех полях в данном правиле.
	* Чтобы удалить существующее правило, щелкните правило, нажмите кнопку с многоточием (...) в конце строки, а затем кнопку **Удалить**.
6. Нажмите кнопку "Сохранить" в верхней части колонки "Параметры брандмауэра", чтобы сохранить изменения. ![колонка брандмауэра][2] 

## Управление правилами брандмауэра на уровне сервера с помощью портала управления 

1. На портале управления щелкните **Базы данных SQL**. Появляется список всех баз данных и их соответствующих серверов.
1. В верхней части страницы щелкните **Серверы**.
2. Щелкните стрелку рядом с сервером, для которого требуется выполнить операции управления правилами брандмауэра.
3. В верхней части страницы щелкните **Настроить**.

	*  Чтобы добавить текущий компьютер, нажмите кнопку "Добавить в разрешенные IP-адреса".
	*  Чтобы добавить дополнительные IP-адреса, введите имя правила, начальный IP-адрес и конечный IP-адрес.
	*  Чтобы изменить существующее правило, измените значения во всех полях в данном правиле.
	*  Чтобы удалить существующее правило, наведите указатель мыши на правило и дождитесь появления X в конце строки. Щелкните X, чтобы удалить правило.
8. Чтобы сохранить изменения, в нижней части страницы щелкните **Сохранить**.

## Управление правилами брандмауэра на уровне сервера с помощью Transact-SQL
1. Откройте окно запроса с помощью портала управления или среды SQL Server Management Studio.
2. Убедитесь, что вы подключены к главной базе данных (master).
3. В окне запроса можно создавать, обновлять или удалять правила брандмауэра на уровне сервера.
4. Для создания или обновления правил брандмауэра на уровне сервера выполните хранимую процедуру sp_set_firewall_rule. В следующем примере показано, как включить диапазон IP-адресов на сервере Contoso.

		EXEC sp_set_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Для удаления правил брандмауэра на уровне сервера выполните хранимую процедуру sp_delete_firewall_rule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.
 
		EXEC sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
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

	Для создания нового или обновления существующего правила брандмауэра на уровне базы данных выполните хранимую процедуру sp_set_database_firewall_rule. В следующем примере показано, как создать новое правило с именем ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Для удаления существующего правила брандмауэра на уровне базы данных выполните хранимую процедуру sp_delete_database_firewall_rule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.
 
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

Учебник по созданию базы данных см. в разделе [Создание первой базы данных SQL Azure](sql-database-get-started.md). Дополнительные сведения о подключении к базе данных Azure SQL из приложений с открытым исходным кодом или приложений сторонних производителей см. в разделе [Рекомендации по подключение к базе данных Azure SQL программным образом](https://msdn.microsoft.com/library/azure/ee336282.aspx). Общие сведения о доступе к базам данных см. в разделе [Управление базами данных и учетными записями в базе данных SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=July15_HO4-->