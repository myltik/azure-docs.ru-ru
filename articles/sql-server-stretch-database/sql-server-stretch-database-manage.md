---
title: "Управление базами данных Stretch и устранение неполадок | Документация Майкрософт"
description: "Узнайте, как управлять растяжением баз данных и устранять неполадки."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 8a43c0fc-64d3-4042-8921-a36542aa8933
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 681ad472e53a17600b589d8354d9fdb5c9c3c574


---
# <a name="manage-and-troubleshoot-stretch-database"></a>Управление растяжением баз данных и устранение неполадок
Для управления растяжением баз данных и устранения неполадок используйте средства и методы, описанные в этом разделе.

## <a name="manage-local-data"></a>Управление локальными данными
### <a name="a-namelocalinfoaget-info-about-local-databases-and-tables-enabled-for-stretch-database"></a><a name="LocalInfo"></a>Получение информации о локальных базах данных и таблицах, для которых включена база данных Stretch
Откройте представления каталога **sys.databases** и **sys.tables**, чтобы просмотреть сведения о базах данных и таблицах SQL Server, для которых включена функция Stretch. Дополнительные сведения см. в статьях [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) и [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Чтобы узнать, сколько места использует в SQL Server таблица, для которой включена функция Stretch, выполните следующую инструкцию.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Управление миграцией данных
### <a name="check-the-filter-function-applied-to-a-table"></a>Проверка функции фильтра, примененной к таблице
Откройте представление каталога **sys.remote\_data\_archive\_tables** и проверьте значение столбца **filter\_predicate**, чтобы определить функцию, которую использует база данных Stretch для выбора строк для переноса. Если значение равно null, то всю таблицу можно перенести. Дополнительные сведения см. в статье [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) и [Выбор строк для миграции с использованием функции фильтров (база данных Stretch)](sql-server-stretch-database-predicate-function.md).

### <a name="a-namemigrationacheck-the-status-of-data-migration"></a><a name="Migration"></a>Проверка состояния переноса данных
Выберите **Задачи | Stretch | Мониторинг** для базы данных в SQL Server Management Studio, чтобы отслеживать перенос данных в мониторе базы данных Stretch. Дополнительные сведения см. в статье [Мониторинг переноса данных и устранение неполадок при этой операции (база данных Stretch)](sql-server-stretch-database-monitor.md).

Или откройте динамическое административное представление **sys.dm\_db\_rda\_migration\_status**, чтобы узнать, сколько пакетов и строк данных было перенесено.

### <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>Устранение неполадок переноса данных
Рекомендации по устранению неполадок см. в статье [Мониторинг переноса данных и устранение неполадок при этой операции (база данных Stretch)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Управление удаленными данными
### <a name="a-nameremoteinfoaget-info-about-remote-databases-and-tables-used-by-stretch-database"></a><a name="RemoteInfo"></a>Получение информации об удаленных базах данных и таблицах, используемых базой данных Stretch
Откройте представления каталога **sys.remote\_data\_archive\_databases** и **sys.remote\_data\_archive\_tables**, чтобы просмотреть сведения об удаленных базах данных и таблицах, в которых хранятся перенесенные данные. Дополнительные сведения см. в статьях [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) и [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Чтобы узнать, сколько пространства в Azure использует таблица с поддержкой переноса, выполните следующую инструкцию.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Удаление перенесенных данных
Если вы хотите удалить данные, уже перенесенные в Azure, выполните действия, описанные в статье [sys.sp_rda_reconcile_batch (Transact-SQL)](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Управление схемой таблицы
### <a name="dont-change-the-schema-of-the-remote-table"></a>Не изменяйте схему удаленной таблицы.
Не изменяйте схему удаленной таблицы Azure, связанной с таблицей SQL Server, для которой настроено растяжение базы данных. В частности, не изменяйте имя и тип данных столбца. Функция растяжения базы данных делает различные предположения в отношении схемы удаленной таблицы по отношению к схеме таблицы SQL Server. При изменении схемы удаленной таблицы растяжение базы данных для измененной таблицы перестает работать.

### <a name="reconcile-table-columns"></a>Согласование столбцов таблицы
Если вы случайно удалили столбцы из удаленной таблицы, выполните командлет **sp_rda_reconcile_columns**, чтобы добавить в удаленную таблицу столбцы, которые существуют в таблице SQL Server с поддержкой Stretch, но не в удаленной таблице. Дополнительные сведения см. в статье [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

> [!IMPORTANT]
> При восстановлении столбцов, случайно удаленных вами из удаленной таблицы, командлет **sp_rda_reconcile_columns** не восстанавливает данные, которые содержались ранее в удаленных столбцах.
> 
> 

Командлет **sp_rda_reconcile_columns** не удаляет из удаленной таблицы столбцы, которые существуют в ней, но те, которых нет в таблице SQL Server с поддержкой Stretch. Если в удаленной таблице Azure есть столбцы, которых больше нет в таблице SQL Server с поддержкой Stretch, эти лишние столбцы не препятствуют нормальной работе базы данных Stretch. При необходимости вы можете вручную удалить лишние столбцы.  

## <a name="manage-performance-and-costs"></a>Управление производительностью и расходами
### <a name="troubleshoot-query-performance"></a>Решение проблем с производительностью запросов
Запросы, которые включают таблицы с поддержкой Stretch, должны выполняться медленнее, чем раньше — до включения функции Stretch. Если производительность запросов значительно снизилась, просмотрите следующие возможные проблемы.

* Находится ли сервер Azure находится в другом географическом регионе по сравнению с сервером SQL Server? Настройте сервер Azure, так чтобы он находился в одном географическом регионе с SQL Server для снижения задержки в сети.
* Возможно ухудшение характеристик работы сети. Обратитесь к администратору сети для получения сведений о последних неполадках или простоях.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Повысьте уровень производительности ресурсоемких операций в Azure, таких как индексирование.
При сборке, повторной сборке или реорганизации индекса для большой таблицы, настроенной для базы данных Stretch, если предполагается большое количество запросов к перенесенным данным в Azure в этот период, необходимо увеличить уровень производительности соответствующей удаленной базы данных Azure на период этой операции. Дополнительные сведения об уровнях производительности и ценовых категориях см. на [этой странице](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Вы не можете приостановить службу базы данных SQL Server Stretch в Azure
 Убедитесь, что выбраны соответствующие уровень производительности и ценовая категория. Если уровень производительности для ресурсоемкой операции повышен временно, по завершении такой операции восстановите производительность до прежнего уровня. Дополнительные сведения об уровнях производительности и ценовых категориях см. на [этой странице](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Изменение области запросов
 Запросы к таблицам с поддержкой Stretch по умолчанию возвращают как локальные, так и удаленные данные. Вы можете изменить область запросов для всех запросов, отправляемых всеми пользователями, или только для одного запроса, отправляемого администратором.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Изменение области запросов для всех запросов, отправляемых всеми пользователями
 Чтобы изменить область всех запросов, отправляемых всеми пользователями, выполните хранимую процедуру **sys.sp_rda_set_query_mode**. Вы можете сузить область запроса только до локальных данных, отключить все запросы или восстановить настройку по умолчанию. Дополнительные сведения см. в статье [sys.sp_rda_set_query_mode (Transact-SQL)](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="a-namequeryhintsachange-the-scope-of-queries-for-a-single-query-by-an-administrator"></a><a name="queryHints"></a>Изменение администратором области запросов для одного запроса
 Чтобы участник роли db_owner мог изменить область одного запроса, добавьте указание запроса **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *значение* \)** в инструкцию SELECT. Указание запроса REMOTE_DATA_ARCHIVE_OVERRIDE может иметь следующие значения.  

* **LOCAL_ONLY.** Запрос только локальных данных.  
* **REMOTE_ONLY.** Запрос только удаленных данных.  
* **STAGE_ONLY.** Запрос только данных в таблице, где база данных Stretch размещает строки, пригодные для миграции, и сохраняет перенесенные строки в течение указанного периода после миграции. Это указание запроса является единственным способом отправить запрос к промежуточной таблице.  

Например, следующий запрос возвращает только локальные результаты.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="a-nameadminhintsamake-administrative-updates-and-deletes"></a><a name="adminHints"></a>Осуществление административных операций обновления и удаления
 По умолчанию вы не можете удалять или обновлять в таблице с поддержкой Stretch строки, пригодные для миграции, или уже перенесенные. Когда вам необходимо устранить проблему, участник роли db_owner может выполнить операцию UPDATE или DELETE, добавив указание запроса **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *значение* \)** в инструкцию. Указание запроса REMOTE_DATA_ARCHIVE_OVERRIDE может иметь следующие значения.  

* **LOCAL_ONLY.** Обновление или удаление только локальных данных.  
* **REMOTE_ONLY.** Обновление или удаление только удаленных данных.  
* **STAGE_ONLY.** Обновление или удаление только данных в таблице, где база данных Stretch размещает строки, пригодные для миграции, и сохраняет перенесенные строки в течение указанного периода после миграции.  

## <a name="see-also"></a>Дополнительные материалы
[Мониторинг базы данных Stretch](sql-server-stretch-database-monitor.md)

[Резервное копирование баз данных с поддержкой Stretch](sql-server-stretch-database-backup.md)

[Восстановление баз данных с поддержкой Stretch](sql-server-stretch-database-restore.md)




<!--HONumber=Nov16_HO3-->


