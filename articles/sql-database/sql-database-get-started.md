<properties
	pageTitle="Руководство по базам данных SQL: создание базы данных SQL | Microsoft Azure"
	description="Из этой статьи вы узнаете, как настроить логический сервер базы данных SQL, правило брандмауэра для сервера, базу данных SQL и демонстрационные данные. Также вы научитесь выполнять подключение с помощью клиентских средств, настраивать пользователей и правила брандмауэра для базы данных."
	keywords="руководство по базам данных SQL, создание базы данных SQL"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/05/2016"
	ms.author="carlrab"/>


# Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Из этого руководства вы узнаете, как с помощью портала Azure выполнять следующие операции.

- Создание логического сервера базы данных SQL Azure для размещения баз данных SQL.
- Создание базы данных SQL без данных, с демонстрационными данными или c данными из резервной копии базы данных SQL.
- Создание правила брандмауэра уровня сервера для одного IP-адреса или диапазона IP-адресов.

Эти же задачи можно выполнять с помощью [C#](sql-database-get-started-csharp.md) или [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Вход](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

[AZURE.INCLUDE [Создание логического сервера базы данных SQL](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Создание базы данных SQL](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Создание базы данных SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Дальнейшие действия
После выполнения инструкций из этого руководства по базам данных SQL у вас будет готовая база данных с демонстрационными данными, которую можно использовать с помощью привычных средств.

- Если вы знакомы с языком Transact-SQL и средой SQL Server Management Studio (SSMS), см. сведения о [подключении и создании запросов к базе данных SQL с помощью SSMS](sql-database-connect-query-ssms.md).

- Если вы знаете Excel, узнайте, как [подключиться к базе данных SQL в Azure с помощью Excel](sql-database-connect-excel.md).

- Если вы готовы написать свой собственный код, выберите язык программирования в [библиотеках подключений для базы данных SQL и SQL Server](sql-database-libraries.md).

- Если вы хотите переместить в Azure локальные базы данных SQL Server, см. статью [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).

- Если вы хотите загрузить данные в новую таблицу из CSV-файла с помощью программы командной строки BCP, см. статью [Загрузка данных из CSV-файла в хранилище данных SQL Azure (неструктурированные файлы)](sql-database-load-from-csv-with-bcp.md).


## Дополнительные ресурсы

[Что такое база данных SQL?](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0907_2016-->