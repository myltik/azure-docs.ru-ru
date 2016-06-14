<properties
   pageTitle="Импорт из BACPAC-файла в Базу данных SQL с помощью SqlPackage"
   description="База данных SQL Microsoft Azure, миграция базы данных, импорт базы данных, импорт BACPAC-файла, sqlpackage"
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

# Импорт из BACPAC-файла в Базу данных SQL с помощью SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Портал Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

В этой статье показано, как импортировать данные из [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файла в Базу данных SQL, используя программу командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Эта служебная программа поставляется вместе с Visual Studio и SQL Server. Кроме того, вы можете [скачать](https://msdn.microsoft.com/library/mt204009.aspx) последнюю версию SQL Server Data Tools для получения этой служебной программы.

> [AZURE.NOTE] При выполнении описанных ниже действий предполагается, что вы уже подготовили сервер Базы данных SQL, имеете сведения о подключении и проверили совместимость исходной базы данных.

## Импорт из BACPAC-файла в базу данных SQL Azure с помощью SqlPackage

Выполните следующие действия для импорта совместимой базы данных SQL Server (или Базы данных SQL Azure) из BACPAC-файла с помощью служебной программы командной строки [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).

> [AZURE.NOTE] При выполнении указанных ниже действий предполагается, что вы уже подготовили сервер базы данных SQL Azure и имеете сведения о подключении.

1. Откройте окно командной строки и измените каталог, содержащий служебную программу командной строки sqlpackage.exe. Эта служебная программа поставляется в комплекте с Visual Studio и SQL Server.
2. Выполните следующую команду sqlpackage.exe со следующими аргументами для вашей среды:

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >

	| Аргумент | Описание |
	|---|---|
	| < server_name > | имя конечного сервера |
	| < database_name > | имя конечной базы данных |
	| < user_name > | имя пользователя на конечном сервере |
	| < password > | пароль пользователя |
	| < source_file > | имя файла и расположение импортируемого файла BACPAC |

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## Дальнейшие действия

- [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Подробнее

- [База данных SQL версии 12](sql-database-v12-whats-new.md).
- [Частично или полностью неподдерживаемые функции Transact-SQL](sql-database-transact-sql-information.md).
- [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/) (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).

<!---HONumber=AcomDC_0601_2016-->