<properties
	pageTitle="Настройка правила брандмауэра уровня сервера базы данных SQL | Microsoft Azure"
	description="Сведения о настройке брандмауэра для IP-адресов, через которые осуществляется доступ к Azure SQL Server."
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
	ms.date="08/30/2016"
	ms.author="rickbyh;carlrab"/>


# Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure


> [AZURE.SELECTOR]
- [Обзор](sql-database-firewall-configure.md)
- [Портал Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [ИНТЕРФЕЙС REST API](sql-database-configure-firewall-settings-rest.md)

Для разрешения подключений к серверам и базам данных сервер SQL Azure использует правила брандмауэра. На логическом сервере SQL Azure для базы данных master или пользовательской базы данных можно определить параметры брандмауэра на уровне сервера и базы данных, которые будут выборочно разрешать доступ к базе данных. В этой статье рассматриваются правила брандмауэра уровня сервера.

> [AZURE.IMPORTANT] Чтобы приложения из Azure могли подключаться к серверу SQL Azure, необходимо разрешить подключения Azure. Принцип действия правил брандмауэра описан в статье [Как настроить брандмауэр базы данных SQL Azure](sql-database-firewall-configure.md). Если вы создаете подключения внутри облака Azure, вам, возможно, придется открыть дополнительные TCP-порты. Дополнительные сведения см. в разделе **Версия 12 Базы данных SQL: внешняя и внутренняя программа** статьи [Порты, кроме 1433, для ADO.NET 4.5 и Базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md)

**Рекомендация.** Используйте правила брандмауэра уровня сервера для администраторов, если у вас много баз данных с одинаковыми требованиями доступа и вы не хотите тратить время на настройку каждой базы данных по отдельности. Корпорация Майкрософт рекомендует использовать правила брандмауэра уровня базы данных всегда, когда это возможно, чтобы повысить уровень безопасности и сделать базу данных более портативной.

[AZURE.INCLUDE [Создание базы данных SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Управление существующими правилами брандмауэра с помощью портала Azure

Повторите действия для управлению правилами брандмауэра на уровне сервера.

- Чтобы добавить текущий компьютер, щелкните "Добавить IP-адрес клиента".
- Чтобы добавить дополнительные IP-адреса, введите имя правила, начальный IP-адрес и конечный IP-адрес.
- Чтобы изменить существующее правило, измените значения во всех полях в данном правиле.
- Чтобы удалить существующее правило, наведите указатель мыши на правило и дождитесь появления X в конце строки. Щелкните X, чтобы удалить правило.

Щелкните **Сохранить**, чтобы сохранить изменения.

## Дальнейшие действия

Практическое руководство по использованию Transact-SQL для создания правил брандмауэра уровня сервера и уровня базы данных см. в статье [Настройка правил брандмауэра уровня сервера базы данных SQL Azure и уровня базы данных SQL Azure с помощью T-SQL](sql-database-configure-firewall-settings-tsql.md).

Практические руководства по созданию правил брандмауэра уровня сервера с помощью других методов см. в следующих разделах.

- [Настройка правил брандмауэра уровня сервера базы данных SQL с помощью PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Практическое руководство. Настройка брандмауэра базы данных SQL Azure с помощью REST API](sql-database-configure-firewall-settings-rest.md)

Учебник по созданию базы данных SQL доступен в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md). Дополнительные сведения о подключении к базе данных SQL Azure из приложений с открытым кодом или приложений сторонних производителей см. в статье [Библиотеки подключений для Базы данных SQL и SQL Server](https://msdn.microsoft.com/library/azure/ee336282.aspx). Общие сведения о навигации по базам данных см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## Дополнительные ресурсы

- [Защита базы данных](sql-database-security.md)
- [Центр обеспечения безопасности для ядра СУБД SQL Server и базы данных Azure SQL](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0831_2016-->