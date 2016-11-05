---
title: Устранение проблем совместимости базы данных SQL Server перед переносом в базу данных SQL | Microsoft Docs
description: База данных SQL Microsoft Azure, миграция базы данных, совместимость, мастер миграции SQL Azure
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab

---
# Устранение проблем совместимости базы данных SQL Server с помощью мастера миграции SQL Azure перед миграцией в базу данных SQL Azure
> [!div class="op_single_selector"]
> * С помощью [мастера миграции SQL Azure (SAMW)](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * Использование [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * Использование [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> 
> 

В этой статье вы узнаете, как найти и устранить проблемы совместимости базы данных SQL Server с помощью мастера миграции SQL Azure, прежде чем выполнить миграцию в базу данных SQL Azure.

## Использование мастера миграции SQL Azure
Используйте инструмент CodePlex, [мастер миграции Базы данных SQL Microsoft Azure ](http://sqlazuremw.codeplex.com/), чтобы создать сценарий T-SQL для несовместимой исходной базы данных. Этот сценарий затем преобразуется мастером, чтобы обеспечить его совместимость с базой данных SQL. После этого следует подключиться к базе данных SQL Azure, чтобы выполнить данный сценарий. Этот инструмент также анализирует файлы трассировки, чтобы выявить проблемы совместимости. Сценарии могут создаваться только со схемой или могут содержать данные в формате BCP. Дополнительная документация, в том числе пошаговые инструкции, доступна на сайте Codeplex в разделе [Мастер миграции SQL Azure](http://sqlazuremw.codeplex.com/).

 ![Схема переноса SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

> [!NOTE]
> Не все несовместимые схемы, которые способен обнаружить мастер, могут быть исправлены с помощью встроенных средств преобразования. Если имеются несовместимые сценарии, которые не удается обработать, то о них будет сообщено как об ошибках. Такие сценарии будут внедрены в созданный сценарий с соответствующими комментариями. Если обнаружено много ошибок, используйте Visual Studio или SQL Server Management Studio для исправления каждой ошибки, которую невозможно исправить с помощью мастера миграции SQL Server.
> 
> 

## Дальнейшие действия
* [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Перенос совместимой базы данных SQL Server в Базу данных SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Дополнительные ресурсы
* [База данных SQL версии 12.](sql-database-v12-whats-new.md)
* [Частично или полностью неподдерживаемые функции Transact-SQL.](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->