---
title: "Приостановка и возобновление переноса данных (база данных Stretch) | Документация Майкрософт"
description: "Узнайте, как приостановить или возобновить перенос данных в Azure."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: d45c05ad-254e-4950-a652-3d5cc40ed967
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8b5f4461a20c1407ba2ce834ec6dd970f4e7544


---
# <a name="pause-and-resume-data-migration-stretch-database"></a>Приостановка и возобновление переноса данных (база данных Stretch)
Чтобы приостановить или возобновить перенос данных в Azure, выберите **Stretch** для таблицы в SQL Server Management Studio, а затем выберите **Приостановить**, чтобы приостановить перенос данных, или **Возобновить**, чтобы возобновить перенос данных. Чтобы приостановить или возобновить перенос данных, также можно использовать Transact\-SQL.

Вы можете приостановить перенос данных, если хотите устранить неполадки на локальном сервере или увеличить доступную пропускную способность сети.

## <a name="pause-data-migration"></a>Приостановка переноса данных
### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Использование SQL Server Management Studio для приостановки переноса данных
1. В SQL Server Management Studio в обозревателе объектов выберите таблицу, совместимую со Stretch, для которой хотите приостановить перенос данных.
2. Щелкните ее правой кнопкой мыши и выберите **Stretch**, а затем щелкните **Приостановить**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Использование Transact\-SQL для приостановки переноса данных
Выполните следующую команду:

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Возобновление переноса данных
### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Использование SQL Server Management Studio для возобновления переноса данных
1. В SQL Server Management Studio в обозревателе объектов выберите таблицу, совместимую со Stretch, для которой хотите возобновить перенос данных.
2. Щелкните ее правой кнопкой мыши и выберите **Stretch**, а затем щелкните **Возобновить**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Использование Transact\-SQL для возобновления переноса данных
Выполните следующую команду:

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Как проверить, активна миграция или приостановлена
### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Использование SQL Server Management Studio, чтобы проверить, активна миграция или приостановлена
В SQL Server Management Studio откройте **монитор базы данных Stretch** и проверьте значение столбца **Состояние миграции**. Дополнительные сведения см. в статье [Monitor and troubleshoot data migration (Stretch Database)](sql-server-stretch-database-monitor.md) (Мониторинг и устранение неполадок переноса данных (база данных Stretch)).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Использование Transact-SQL, чтобы проверить, активна миграция или приостановлена
Выполните запрос к представлению каталога **sys.remote_data_archive_tables** и проверьте значение столбца **is_migration_paused**. Дополнительные сведения см. в статье [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Дополнительные материалы
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Мониторинг и устранение неполадок переноса данных (база данных Stretch)](sql-server-stretch-database-monitor.md) 




<!--HONumber=Nov16_HO3-->


