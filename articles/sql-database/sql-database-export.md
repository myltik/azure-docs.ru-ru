---
title: "Экспорт базы данных SQL Azure в BACPAC-файл | Документация Майкрософт"
description: "Экспорт базы данных SQL Azure в BACPAC-файл с помощью портала Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: ff24360696c54091ab93e7e1c8f28d95c226bc0a
ms.lasthandoff: 03/29/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>Экспорт базы данных SQL Azure или SQL Server в BACPAC-файл

В этой статье рассматривается экспорт базы данных SQL Azure или SQL Server в BACPAC-файл. 

> [!IMPORTANT]
> В настоящий момент служба Azure SQL Database Automated Export находится на этапе предварительной версии, и ее использование будет прекращено 1 марта 2017 года. Начиная с 1 декабря 2016 года будет невозможно настроить автоматический экспорт для какой-либо базы данных SQL. Все существующие задания автоматического экспорта продолжат работать до 1 марта 2017 года. После 1 декабря 2016 года можно использовать [долгосрочное хранение резервных копий](sql-database-long-term-retention.md) или [службу автоматизации Azure](../automation/automation-intro.md), чтобы периодически архивировать базы данных SQL с помощью PowerShell согласно выбранному расписанию. Пример сценария можно скачать из [репозитория GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 
>

## <a name="overview"></a>Обзор

Если нужно экспортировать базу данных для создания архива или для перехода на другую платформу, можно экспортировать схему и данные базы данных в BACPAC-файл. BACPAC-файл — это просто ZIP-файл с расширением BACPAC. BACPAC-файл можно сохранить в хранилище BLOB-объектов Azure или локальном хранилище, а затем импортировать обратно в базу данных SQL Azure или локальный экземпляр SQL Server. 

* Вы можете экспортировать базу данных SQL Azure с помощью [портала Azure](sql-database-export-portal.md), [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) или [SQL Server Management Studio](sql-database-export-ssms.md).
* Базу данных SQL Server можно экспортировать с помощью [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) или [SQL Server Management Studio](sql-database-export-ssms.md).

> [!IMPORTANT]
> Если вы экспортируете данные из SQL Server, чтобы потом перенести их в базу данных SQL Azure, см. в статью [Migrate a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md) (Миграция базы данных SQL Server в базу данных SQL).
> 

## <a name="considerations"></a>Рекомендации

* Чтобы экспорт был транзакционно согласованным, необходимо обеспечить отсутствие операций записи во время экспорта или экспортировать данные из [транзакционно согласованной копии](sql-database-copy.md) базы данных SQL Azure.
* Максимальный размер BACPAC-файла при экспорте в хранилище BLOB-объектов составляет 200 ГБ. Для архивации BACPAC-файла большего размера выполняйте экспорт в локальное хранилище.
* Экспорт BACPAC-файла в службу хранилища Azure уровня "Премиум" с использованием методов, описанных в этой статье, не поддерживается.
* Если операция экспорта из базы данных SQL Azure длится более 20 часов, ее можно отменить. Для повышения производительности во время экспорта можно сделать следующее.
  * Временно повысить уровень служб.
  * Прекратить все операции чтения и записи во время экспорта.
  * Используйте для всех больших таблиц [кластеризованный индекс](https://msdn.microsoft.com/library/ms190457.aspx) со значениями, отличными от NULL. Без кластеризованных индексов экспорт может завершиться ошибкой, если он длится больше 6–12 часов. Это обусловлено тем, что службам экспорта требуется выполнить проверку таблицы, чтобы экспортировать всю таблицу. Хороший способ определить, оптимизированы ли таблицы для экспорта, — выполнить **DBCC SHOW_STATISTICS** и убедиться, что значение *RANGE_HI_KEY* не равно NULL и имеет хорошее распределение. Дополнительную информацию см. в разделе [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPAC-файлы не предназначены для операций службы архивации и восстановления. База данных SQL Azure автоматически создает резервные копии для каждой пользовательской базы данных. Дополнительные сведения см. в статьях [Обзор обеспечения непрерывности бизнес-процессов с помощью базы данных SQL Azure](sql-database-business-continuity.md) и[Подробнее о резервном копировании базы данных SQL](sql-database-automated-backups.md).  
> 


## <a name="next-steps"></a>Дальнейшие действия

* Описание процесса миграции базы данных SQL Server в базу данных SQL Azure см. в [этой статье](sql-database-cloud-migrate.md).
* Общие сведения о копировании базы данных SQL Azure см. в [этой статье](sql-database-copy.md).
* Вы можете создать базу данных SQL Azure с помощью [портала Azure](sql-database-copy-portal.md), [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) или [Transact-SQL](sql-database-copy-transact-sql.md). 

