<properties
   pageTitle="Экспорт базы данных SQL Server в BACPAC-файл с помощью SqlPackage | Microsoft Azure"
   description="База данных SQL Microsoft Azure, миграция базы данных, экспорт базы данных, экспорт BACPAC-файла, sqlpackage"
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

# Экспорт базы данных SQL Server в BACPAC-файл с помощью SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

В этой статье показано, как экспортировать базу данных SQL Server в [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файл, используя программу командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Эта служебная программа поставляется вместе с последним версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/ru-RU/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.

1. Откройте окно командной строки и измените каталог, содержащий служебную программу командной строки sqlpackage.exe. Эта служебная программа поставляется в комплекте с Visual Studio и SQL Server. С помощью функции поиска на компьютере найдите путь к своей среде.
2. Выполните следующую команду sqlpackage.exe со следующими аргументами для вашей среды:

	'sqlpackage.exe /Action:Export /ssn:<имя\_сервера> /sdn:<имя\_БД> /tf:<целевой\_файл>

	| Аргумент | Описание |
	|---|---|
	| <имя\_сервера> | имя исходного сервера |
	| <имя\_БД> | имя исходной базы данных |
	| <целевой\_файл> | имя файла и расположение для файла BACPAC |

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Дальнейшие действия

- [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Импорт BACPAC-файла в базу данных SQL Azure с помощью SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Импорт BACPAC-файла в базу данных SQL Azure с помощью SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Импорт BACPAC-файла в базу данных SQL Azure с помощью портала Azure](sql-database-import.md)
- [Импорт BACPAC-файла в базу данных SQL Azure с помощью PowerShell](sql-database-import-powershell.md)

## Дополнительные ресурсы

- [База данных SQL версии 12.](sql-database-v12-whats-new.md)
- [Частично или полностью неподдерживаемые функции Transact-SQL.](sql-database-transact-sql-information.md)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->