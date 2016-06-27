<properties
	pageTitle="Практическое руководство. Настройка брандмауэра базы данных SQL Azure | Microsoft Azure"
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


# Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью REST API


> [AZURE.SELECTOR]
- [Обзор](sql-database-firewall-configure.md)
- [Портал Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)


Для подключения к серверам и базам данных в Базе данных SQL Microsoft Azure используются правила брандмауэра. На сервере Базы данных SQL Azure для главной или пользовательской базы данных можно определить значения параметров брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [AZURE.IMPORTANT] Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключения Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключения из Azure, см. в разделе [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md). Вам может потребоваться открыть некоторые дополнительные порты TCP при создании соединений внутри границ облака Azure. Дополнительные сведения см. в разделе **Версия 12 Базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и Базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md)


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


## Управление правилами брандмауэра с помощью интерфейса API REST управления службой

* [Создание правила брандмауэра](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Удаление правила брандмауэра](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Получение правила брандмауэра](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [Вывод списка правил брандмауэра](https://msdn.microsoft.com/library/azure/dn505715.aspx)
 
## Дальнейшие действия

Учебник по созданию базы данных см. в разделе [Создание первой базы данных SQL Azure](sql-database-get-started.md). Дополнительные сведения о подключении к Базе данных SQL Azure из приложений с открытым исходным кодом или приложений сторонних производителей см. в разделе [Рекомендации по подключению к Базе данных SQL Azure программными средствами](https://msdn.microsoft.com/library/azure/ee336282.aspx). Общие сведения о переходе к базам данных см. в разделе [Управление базами данных и учетными записями в Базе данных SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0615_2016-->