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
	ms.date="12/16/2015"
	ms.author="rickbyh"/>


# Практическое руководство. Настройка параметров брандмауэра для Базы данных SQL с помощью портала Azure


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Для подключения к серверам и базам данных в Базе данных SQL используются правила брандмауэра. На логическом сервере Базы данных SQL для базы данных master или пользовательской базы данных можно определить значения параметров брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [AZURE.IMPORTANT]Чтобы приложения из Azure могли подключаться к серверу базы данных, необходимо разрешить подключения Azure. Дополнительные сведения о правилах брандмауэра и о том, как разрешить подключения из Azure, см. в разделе [Брандмауэр Базы данных SQL Azure](sql-database-firewall-configure.md). Вам может потребоваться открыть некоторые дополнительные порты TCP при создании соединений внутри границ облака Azure. Дополнительные сведения см. в разделе **Версия 12 Базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и Базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md)


### Управление правилами брандмауэра на уровне сервера с помощью портала Azure


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## Управление правилами брандмауэра на уровне сервера 

1. На портале Azure щелкните **Базы данных SQL**. Появляется список всех баз данных и их соответствующих серверов.
2. В верхней части страницы щелкните **Серверы**.
3. Щелкните стрелку рядом с сервером, для которого требуется выполнить операции управления правилами брандмауэра.
4. В верхней части страницы щелкните **Настроить**.

	*  Чтобы добавить текущий компьютер, нажмите кнопку "Добавить в разрешенные IP-адреса".
	*  Чтобы добавить дополнительные IP-адреса, введите имя правила, начальный IP-адрес и конечный IP-адрес.
	*  Чтобы изменить существующее правило, измените значения во всех полях в данном правиле.
	*  Чтобы удалить существующее правило, наведите указатель мыши на правило и дождитесь появления X в конце строки. Щелкните X, чтобы удалить правило.
5. Чтобы сохранить изменения, в нижней части страницы нажмите кнопку **Сохранить**.


## Дальнейшие действия

Учебник по созданию баз данных см. в разделе [Создание своей первой Базы данных SQL Azure](sql-database-get-started.md). Дополнительные сведения о подключении к Базе данных SQL Azure из приложений с открытым исходным кодом или приложений сторонних производителей см. в разделе [Рекомендации по подключению к Базе данных SQL Azure программными средствами](https://msdn.microsoft.com/library/azure/ee336282.aspx). Общие сведения о переходе к базам данных см. в разделе [Управление базами данных и именами для входа в Базу данных SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_1217_2015-->