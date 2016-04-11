<properties
   pageTitle="Устранение проблем совместимости Базы данных SQL Server перед миграцией в Базу данных SQL"
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
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Устранение проблем совместимости базы данных SQL Server с помощью SQL Server Management Studio перед миграцией в базу данных SQL

Существует несколько способов решения обнаруженных проблем совместимости исходной Базы данных SQL Server.

> [AZURE.SELECTOR]
- С помощью [мастера миграции SQL Azure (SAMW)](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Использование [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Использование [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Использование среды SQL Server Management Studio

Среда Management Studio позволяет устранять проблемы совместимости, используя команды Transact-SQL, например **ALTER DATABASE**.

## Следующий шаг: выбор метода миграции и ее выполнение

[Выберите метод миграции](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_0330_2016-->