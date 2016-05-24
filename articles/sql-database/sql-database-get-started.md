<properties
	pageTitle="Руководство по базам данных SQL: создание базы данных SQL | Microsoft Azure"
	description="Узнайте, как настроить логический сервер базы данных SQL, правило брандмауэра для сервера, базу данных SQL, пример данных, как устанавливать подключение с помощью клиентских средств, настраивать пользователей и правило брандмауэра базы данных."
	keywords="руководство по базам данных SQL, создание базы данных SQL"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/14/2016"
	ms.author="carlrab"/>

# Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Из этого руководства вы узнаете, как с помощью портала Azure выполнять следующие операции.

- Создание логического сервера базы данных SQL для размещения баз данных SQL.
- Создание базы данных SQL без данных, с примером данных или данными из резервной копии базы данных SQL.
- Создание правила брандмауэра уровня сервера для одного IP-адреса или диапазона IP-адресов.

Используйте следующие ссылки для выполнения таких же задач с помощью [C#](sql-database-get-started-csharp.md) или [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Вход](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Создание логического сервера базы данных SQL](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Создание базы данных SQL](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Создание базы данных SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Дальнейшие действия
После выполнения инструкций этого руководства по базе данных SQL у вас будет готовая база данных с образцами данных, которую можно использовать с помощью привычных средств.

- Если вы знакомы с языком Transact-SQL и средой SQL Server Management Studio, узнайте о [подключении и создании запросов к базе данных SQL с помощью SSMS](sql-database-connect-query-ssms.md).

- Если вы знаете Excel, узнайте, как [подключиться к базе данных SQL с помощью Excel](sql-database-connect-excel.md).

- Если вы готовы написать свой собственный код, выберите язык программирования в [библиотеках подключений для базы данных SQL и SQL Server](sql-database-libraries.md).

- Если вы хотите переместить в Azure локальные базы данных SQL Server, изучите этот процесс в статье [Перенос базы данных SQL Server в базу данных SQL Azure](sql-database-cloud-migrate.md).


## Подробнее

[Что такое база данных SQL?](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0518_2016-->