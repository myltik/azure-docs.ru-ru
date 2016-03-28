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
	ms.date="03/16/2016"
	ms.author="rickbyh"/>


# Практическое руководство. Настройка параметров брандмауэра для Базы данных SQL с помощью портала Azure


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)


Для разрешения подключений к серверам и базам данных сервер SQL Azure использует правила брандмауэра. На логическом сервере SQL Azure для базы данных master или пользовательской базы данных можно определить параметры брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных.

> [AZURE.IMPORTANT] Чтобы приложения из Azure могли подключаться к серверу SQL Azure, необходимо разрешить подключения Azure. Чтобы понять, как работают правила брандмауэра, см. раздел [Как настроить брандмауэр базы данных SQL Azure](sql-database-firewall-configure.md). Вам может потребоваться открыть некоторые дополнительные порты TCP при создании соединений внутри границ облака Azure. Дополнительные сведения см. в разделе **Версия 12 Базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и Базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md)


### Добавление правила брандмауэра на уровне сервера с помощью портала Azure


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## Управление существующими правилами брандмауэра с помощью портала Azure

Повторите действия для управлению правилами брандмауэра на уровне сервера.

- Чтобы добавить текущий компьютер, щелкните "Добавить IP-адрес клиента".
- Чтобы добавить дополнительные IP-адреса, введите имя правила, начальный IP-адрес и конечный IP-адрес.
- Чтобы изменить существующее правило, измените значения во всех полях в данном правиле.
- Чтобы удалить существующее правило, наведите указатель мыши на правило и дождитесь появления X в конце строки. Щелкните X, чтобы удалить правило.

Щелкните **Сохранить**, чтобы сохранить изменения.


## Дальнейшие действия

Правило брандмауэра сервера влияет на все базы данных SQL на сервере SQL Azure. Чтобы настроить правило брандмауэра на уровне базы данных, которое повлияет только на одну базу данных, см. раздел [sp\_set\_firewall\_rule (база данных SQL Azure)](https://msdn.microsoft.com/library/dn270017.aspx").

Учебник по созданию базы данных см. в разделе [Создание первой базы данных SQL Azure](sql-database-get-started.md). Дополнительные сведения о подключении к Базе данных SQL Azure из приложений с открытым исходным кодом или приложений сторонних производителей см. в разделе [Рекомендации по подключению к Базе данных SQL Azure программными средствами](https://msdn.microsoft.com/library/azure/ee336282.aspx). Общие сведения о переходе к базам данных см. в разделе [Управление базами данных и учетными записями в Базе данных SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0316_2016-->