<properties
   pageTitle="Определение совместимости Базы данных SQL с помощью SqlPackage.exe"
   description="База данных SQL Microsoft Azure, миграция базы данных, совместимость базы данных SQL, SqlPackage"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="carlrab"/>

# Определение совместимости Базы данных SQL с помощью SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Помощник по обновлению](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

В этой статье вы узнаете, как определить, подходит ли база данных SQL Server для миграции с помощью служебной программы командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Использование SqlPackage.exe

1. Откройте окно командной строки и измените каталог, содержащий самую последнюю версию sqlpackage.exe. Эта служебная программа поставляется вместе с Visual Studio и SQL Server. Загрузите [последнюю версию SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), чтобы получить последнюю версию служебной программы SqlPackage.
2. Выполните следующую команду SqlPackage со следующими аргументами для вашей среды:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	| Аргумент | Описание |
	|---|---|
	| < server_name > | имя исходного сервера |
	| < database_name > | имя исходной базы данных |
	| < target_file > | имя файла и расположение для файла BACPAC |
	| < schema_name.table_name > | таблицы, для которых данные будут выведены в конечный файл |
	| < output_file > | имя и расположение для выходного файла с ошибками, если таковые имеются |

	Аргумент /p:TableName используется потому, что нужно проверить только совместимость базы данных для экспорта в базу данных SQL Azure версии 12, а не экспортировать данные из всех таблиц. К сожалению, аргумент экспорта для sqlpackage.exe не поддерживает извлечение без таблицы, поэтому нужно указать одну небольшую таблицу. Аргумент < output_file > будет содержать отчет об ошибках. Строка «> 2>&1» выводит как стандартные выходные данные, так и стандартные ошибки, полученные в результате выполнения команды в указанный выходной файл.

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Откройте выходной файл и просмотрите проблемы совместимости (если они есть).

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Дальнейшие действия

- [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx) [последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Устранение проблем совместимости при миграции базы данных](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Перенос совместимой базы данных SQL Server в Базу данных SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Подробнее

- [База данных SQL версии 12](sql-database-v12-whats-new.md).
- [Частично или полностью неподдерживаемые функции Transact-SQL](sql-database-transact-sql-information.md).
- [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/) (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).

<!---HONumber=AcomDC_0601_2016-->