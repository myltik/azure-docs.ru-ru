<properties
   pageTitle="Устранение проблем совместимости базы данных SQL Server с помощью SQL Server Management Studio перед переносом в базу данных SQL | Microsoft Azure"
   description="База данных SQL Microsoft Azure, миграция базы данных, совместимость, мастер миграции SQL Azure"
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
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Устранение проблем совместимости базы данных SQL Server с помощью SQL Server Management Studio перед миграцией в базу данных SQL

> [AZURE.SELECTOR]
- С помощью [мастера миграции SQL Azure (SAMW)](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Использование [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Использование [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Опытные пользователи могут устранить проблемы совместимости базы данных SQL Server с помощью SQL Server Management Studio перед миграцией в базу данных SQL Azure.


> [AZURE.IMPORTANT] Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## Использование среды SQL Server Management Studio

Среда Management Studio позволяет устранять проблемы совместимости, используя команды Transact-SQL, например **ALTER DATABASE**. Этот метод в основном предназначен для опытных пользователей, которые уверенно используют Transact-SQL в действующей базе данных. В противном случае рекомендуется использовать SSDT.



## Дальнейшие действия

- [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Перенос совместимой базы данных SQL Server в Базу данных SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Дополнительные ресурсы

- [База данных SQL версии 12.](sql-database-v12-whats-new.md)
- [Частично или полностью неподдерживаемые функции Transact-SQL.](sql-database-transact-sql-information.md)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0706_2016-->