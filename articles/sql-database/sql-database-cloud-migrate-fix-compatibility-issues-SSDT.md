<properties
   pageTitle="Устранение проблем совместимости Базы данных SQL Server перед миграцией в Базу данных SQL"
   description="База данных SQL Microsoft Azure, миграция базы данных, совместимость, мастер миграции SQL Azure, SSDT"
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
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-SSMS.md)

## Использование SQL Server Data Tools для Visual Studio

SQL Server Data Tools для Visual Studio (SSDT) позволяет импортировать схему базы данных в проект базы данных Visual Studio для анализа. Для анализа укажите целевую платформу для проекта как Базу данных SQL версии 12, а затем соберите проект. Если построение выполнено успешно, база данных совместима. Если построение завершается неудачей, вы можете устранить ошибки SSDT (или одним из других средств, рассмотренных в этом разделе). После успешного построения проекта, вы можете опубликовать его в качестве копии базы данных-источника, а затем использовать функцию сравнения данных в SSDT, чтобы скопировать данные из базы данных-источника в базу данных, совместимую с SQL Azure версии 12. Затем вы можете выполнить миграцию обновленной базы данных. Чтобы использовать этот параметр, загрузите [последнюю версию SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Схема переноса VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  >[AZURE.NOTE]Если требуется перенести только схему, можно опубликовать ее в Базе данных SQL Azure непосредственно из Visual Studio. Этот метод используется, если схема баз данных требует больше изменений, чем может обработать мастер миграции.

## Следующий шаг: выбор метода миграции и ее выполнение

[Выберите метод миграции](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->