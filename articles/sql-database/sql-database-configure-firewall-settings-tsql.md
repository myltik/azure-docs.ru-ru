<properties
	pageTitle="Практическое руководство. Настройка брандмауэра базы данных SQL | Microsoft Azure"
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
	ms.date="04/26/2016"
	ms.author="rickbyh"/>


# Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью TSQL


> [AZURE.SELECTOR]
- [Обзор](sql-database-firewall-configure.md)
- [Портал Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)


Для подключения к серверам и базам данных в Базе данных SQL Microsoft Azure используются правила брандмауэра. На сервере Базы данных SQL Azure для главной или пользовательской базы данных можно определить значения параметров брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [AZURE.IMPORTANT] Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключения Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключения из Azure, см. в разделе [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md). Вам может потребоваться открыть некоторые дополнительные порты TCP при создании соединений внутри границ облака Azure. Дополнительные сведения см. в разделе **Версия 12 Базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и Базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Управление правилами брандмауэра на уровне сервера с помощью Transact-SQL

Правило брандмауэра уровня сервера с помощью Transact-SQL может создавать только пользователь с именем пользователя субъекта или администратор Azure Active Directory.

1. Откройте окно запроса и подключитесь к главной виртуальной базе данных с помощью SQL Server Management Studio.
2. В окне запроса можно создавать, обновлять или удалять правила брандмауэра на уровне сервера.
3. Для создания или обновления правил брандмауэра на уровне сервера выполните хранимую процедуру sp\_set\_firewall\_rule. В следующем примере показано, как задать диапазон IP-адресов на сервере Contoso.<br/>Для начала узнайте, какие правила уже существуют.

		SELECT * FROM sys.firewall_rules ORDER BY name;

	Добавьте правило брандмауэра.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Для удаления правил брандмауэра на уровне сервера выполните хранимую процедуру sp\_delete\_firewall\_rule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Дополнительные сведения об этих хранимых процедурах см. на страницах [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) и [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## Правила брандмауэра на уровне базы данных

Правило брандмауэра уровня базы данных может создавать только пользователь базы данных с разрешением **управления** для базы данных (например, владелец базы данных).

1. После создания брандмауэра на уровне сервера для своего IP-адреса откройте окно запроса на классическом портале или с помощью среды SQL Server Management Studio.
2. Подключение к базе данных, для которой нужно создать правило брандмауэра на уровне базы данных.

	Для создания нового или обновления существующего правила брандмауэра на уровне базы данных выполните хранимую процедуру sp\_set\_database\_firewall\_rule. В следующем примере показано, как создать новое правило с именем ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
		    @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Для удаления существующего правила брандмауэра на уровне базы данных выполните хранимую процедуру sp\_delete\_database\_firewall\_rule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Дополнительные сведения об этих хранимых процедурах см. на страницах [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) и [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## Дальнейшие действия

Руководство по созданию базы данных SQL см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md). Дополнительные сведения о подключении к базе данных SQL Azure из приложений с открытым исходным кодом или приложений сторонних производителей см. в статье [Библиотеки подключений для Базы данных SQL и SQL Server](https://msdn.microsoft.com/library/azure/ee336282.aspx). Общие сведения о переходе к базам данных см. в статье [Безопасность баз данных SQL: управление доступом к базе данных и защита входа в систему](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!---HONumber=AcomDC_0615_2016-->