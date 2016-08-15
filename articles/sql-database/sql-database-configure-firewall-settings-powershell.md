<properties
	pageTitle="Настройка правил брандмауэра уровня сервера базы данных SQL с помощью PowerShell | Microsoft Azure"
	description="Сведения о настройке брандмауэра для IP-адресов, через которые осуществляется доступ к базам данных SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="sstein"/>


# Настройка правил брандмауэра уровня сервера базы данных SQL с помощью PowerShell


> [AZURE.SELECTOR]
- [Обзор](sql-database-firewall-configure.md)
- [Портал Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)


Для подключения к серверам и базам данных в Базе данных SQL Azure используются правила брандмауэра. На сервере Базы данных SQL для базы данных master или пользовательской базы данных можно определить параметры брандмауэра уровня сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [AZURE.IMPORTANT] Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключения Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключения из Azure, см. в разделе [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md). Вам может потребоваться открыть некоторые дополнительные порты TCP при создании соединений внутри границ облака Azure. Дополнительные сведения см. в разделе "Версия 12 Базы данных SQL: внешняя и внутренняя программа" статьи [Порты, кроме 1433, для ADO.NET 4.5 и Базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md).


[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]

## Создание правил брандмауэра для сервера

Правила брандмауэра уровня сервера можно создавать, обновлять и удалять с помощью Azure PowerShell.

Для создания нового правила брандмауэра на уровне сервера выполните командлет New-AzureRmSqlServerFirewallRule. В следующем примере показано, как включить диапазон IP-адресов на сервере Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'		

Для изменения существующего правила брандмауэра на уровне сервера выполните командлет Set-AzureSqlDatabaseServerFirewallRule. В следующем примере показано, как изменить диапазон допустимых IP-адресов для правила с именем ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Для удаления существующего правила брандмауэра на уровне сервера выполните командлет Remove-AzureSqlDatabaseServerFirewallRule. В следующем примере показано, как удалить правило с именем ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## Управление правилами брандмауэра с помощью PowerShell

Для управления правилами брандмауэра можно также использовать PowerShell. Дополнительные сведения см. в следующих статьях:

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603860.aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603588.aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603789.aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/mt603586.aspx)


## Дальнейшие действия

Информацию о том, как использовать Transact-SQL для создания правил брандмауэра уровня сервера и уровня базы данных, см. в разделе [Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью TSQL](sql-database-configure-firewall-settings-tsql.md).

Информацию о том, как создавать правила брандмауэра уровня сервера с помощью других методов, см. в следующих разделах.

- [Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью PowerShell](sql-database-configure-firewall-settings.md)
- [Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью REST API](sql-database-configure-firewall-settings-rest.md)

Учебник по созданию базы данных SQL доступен в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md). Дополнительные сведения о подключении к базе данных SQL Azure из приложений с открытым кодом или приложений сторонних производителей см. в статье [Библиотеки подключений для Базы данных SQL и SQL Server](https://msdn.microsoft.com/library/azure/ee336282.aspx). Общие сведения о навигации по базам данных см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## Дополнительные ресурсы

- [Защита базы данных](sql-database-security.md)
- [Центр обеспечения безопасности для ядра СУБД SQL Server и базы данных Azure SQL](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_0803_2016-->