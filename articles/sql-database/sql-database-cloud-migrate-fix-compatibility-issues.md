<properties
   pageTitle="Устранение проблем совместимости Базы данных SQL Server перед миграцией в Базу данных SQL"
   description="База данных SQL Microsoft Azure, миграция базы данных, совместимость, мастер миграции SQL Azure"
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
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Устранение проблем совместимости Базы данных SQL Server перед миграцией в Базу данных SQL

Существует несколько способов решения обнаруженных проблем совместимости исходной Базы данных SQL Server.

> [AZURE.SELECTOR]
- Use [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Использование мастера миграции SQL Azure

Средство Codeplex [мастера миграции SQL Azure](http://sqlazuremw.codeplex.com/) можно использовать для создания сценария T-SQL из несовместимой исходной базы данных, который затем преобразуется мастером, чтобы сделать его совместимым с Базой данных SQL, а затем подключить к Базе данных SQL Azure для выполнения сценария. Это средство также будет анализировать файлы трассировки, чтобы определить проблемы совместимости. Сценарии могут создаваться только со схемой или могут содержать данные в формате BCP. Дополнительная документация, в том числе пошаговые инструкции, доступна на сайте Codeplex в разделе [Мастер миграции SQL Azure](http://sqlazuremw.codeplex.com/).

 ![Схема переноса SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  >[AZURE.NOTE]Обратите внимание, что не все несовместимые схемы, которые способен обнаружить мастер, могут быть устранены с помощью встроенных средств преобразования. Если имеются несовместимые сценарии, которые не удается обработать, то о них будет сообщено как об ошибках. Такие сценарии будут внедрены в созданный сценарий с соответствующими комментариями. Если обнаружено много ошибок, используйте Visual Studio или SQL Server Management Studio для исправления каждой ошибки, которую невозможно исправить с помощью мастера миграции SQL Server.

## Следующий шаг: выбор метода миграции и ее выполнение

[Выберите метод миграции](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->