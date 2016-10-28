<properties
   pageTitle="Определение совместимости базы данных SQL с помощью SqlPackage.exe | Microsoft Azure"
   description="База данных SQL Microsoft Azure, миграция базы данных, совместимость базы данных SQL, SqlPackage"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# Определение совместимости Базы данных SQL с помощью SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Помощник по обновлению](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

В этой статье вы узнаете, как определить, подходит ли база данных SQL Server для переноса с помощью служебной программы командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Использование SqlPackage.exe

1. Откройте окно командной строки и измените каталог, содержащий самую последнюю версию sqlpackage.exe. Эта служебная программа поставляется вместе с последним версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/ru-RU/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.
2. Выполните следующую команду SqlPackage со следующими аргументами для вашей среды:

	'sqlpackage.exe /Action:Export /ssn:<имя\_сервера> /sdn:<имя\_БД> /tf:<целевой\_файл> /p:TableData=<имя\_схемы.имя\_таблицы> > <выходной\_файл> 2>&1'

	| Аргумент | Описание |
	|---|---|
	| <имя\_сервера> | имя исходного сервера |
	| <имя\_БД> | имя исходной базы данных |
	| <целевой\_файл> | имя файла и расположение для файла BACPAC |
	| <имя\_схемы.имя\_таблицы> | таблицы, для которых данные выводятся в конечный файл |
	| <выходной\_файл> | имя и расположение для выходного файла с ошибками, если таковые имеются |

	Используется аргумент /p:TableName, так как необходимо только проверить совместимость базы данных для экспорта в Базу данных SQL Azure версии 12, а не экспортировать данные из всех таблиц. К сожалению, аргумент экспорта для sqlpackage.exe не поддерживает извлечение без таблиц. Необходимо указать хотя бы одну небольшую таблицу. Указанный <выходной\_файл> содержит отчет об ошибках. Строка «> 2>&1» выводит как стандартные выходные данные, так и стандартные ошибки, полученные в результате выполнения команды в указанный выходной файл.

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Откройте выходной файл и просмотрите проблемы совместимости (если они есть).

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Дальнейшие действия

- [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx) [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Устранение проблем совместимости при миграции базы данных](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Перенос совместимой базы данных SQL Server в Базу данных SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Дополнительные ресурсы

- [База данных SQL версии 12.](sql-database-v12-whats-new.md)
- [Частично или полностью неподдерживаемые функции Transact-SQL.](sql-database-transact-sql-information.md)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->