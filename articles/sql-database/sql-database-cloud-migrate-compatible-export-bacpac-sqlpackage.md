<properties
   pageTitle="Экспорт базы данных SQL Server в BACPAC-файл с помощью SqlPackage"
   description="База данных SQL Microsoft Azure, миграция базы данных, экспорт базы данных, экспорт BACPAC-файла, sqlpackage"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Экспорт базы данных SQL Server в BACPAC-файл с помощью SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

В этой статье показано, как экспортировать базу данных SQL в [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файл, используя программу командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Эта служебная программа поставляется вместе с Visual Studio и SQL Server. Кроме того, вы можете [скачать](https://msdn.microsoft.com/library/mt204009.aspx) последнюю версию SQL Server Data Tools для получения этой служебной программы.

1. Откройте окно командной строки и измените каталог, содержащий служебную программу командной строки sqlpackage.exe. Эта служебная программа поставляется в комплекте с Visual Studio и SQL Server. С помощью функции поиска на компьютере найдите путь к своей среде.
2. Выполните следующую команду sqlpackage.exe со следующими аргументами для вашей среды:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| Аргумент | Описание |
	|---|---|
	| < server_name > | имя исходного сервера |
	| < database_name > | имя исходной базы данных |
	| < target_file > | имя файла и расположение для файла BACPAC |

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Следующий шаг: импорт из BACPAC-файла в Базу данных SQL

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Портал Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_0316_2016-->