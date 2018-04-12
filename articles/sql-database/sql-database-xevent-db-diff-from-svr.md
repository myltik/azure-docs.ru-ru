---
title: Расширенные события в базе данных SQL | Документация Майкрософт
description: В статье описываются расширенные события (XEvents) в Базе данных SQL Azure и отличия соответствующих сеансов событий от сеансов событий в Microsoft SQL Server.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 4a1a8a332628e79972e7c03dbc2ac839f244a002
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="extended-events-in-sql-database"></a>Расширенные события в Базе данных SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

В этом разделе объясняется, чем расширенные события в Базе данных SQL Azure отличается от расширенных событий в Microsoft SQL Server.

- В Базе данных SQL версии 12 функция расширенных событий появилась во второй половине 2015 года.
- SQL Server включает расширенные события с 2008 года.
- Набор функций расширенных событий в Базе данных SQL представляет собой устойчивое подмножество функций SQL Server.

*XEvents* — это неофициальное обозначение расширенных событий, которое встречается в блоках и других неофициальных источниках.

Дополнительные сведения о расширенных событиях для базы данных SQL Azure и Microsoft SQL Server доступны в следующих разделах.

- [Quick Start: Extended events in SQL Server](http://msdn.microsoft.com/library/mt733217.aspx)
- [Расширенные события](http://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>предварительным требованиям

В данной статье предполагается, чтобы вы уже ознакомились со следующими компонентами:

- [Служба Базы данных SQL Azure](https://azure.microsoft.com/services/sql-database/);
- [Расширенные события](http://msdn.microsoft.com/library/bb630282.aspx) в Microsoft SQL Server.

- Большинство документации о расширенных событиях относится и к SQL Server, и к Базе данных SQL.

При выборе файла событий в качестве [целевого объекта](#AzureXEventsTargets)пригодится знание следующих компонентов:

- [Служба хранилища Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Использование Azure PowerShell с хранилищем Azure](../storage/common/storage-powershell-guide-full.md) — статья содержит полную информацию о PowerShell и службе хранилища Azure.

## <a name="code-samples"></a>Примеры кода

Связанные разделы содержат два примера кода.


- [Код целевого объекта "Кольцевой буфер" для расширенных событий в Базе данных SQL](sql-database-xevent-code-ring-buffer.md)
    - Простой короткий сценарий Transact-SQL.
    - В этой статье с примером кода подчеркивается, что после завершения работы с целевым объектом "Кольцевой буфер" необходимо освободить задействованные им ресурсы с помощью инструкции изменения и удаления `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` . Впоследствии вы сможете добавить другой экземпляр кольцевого буфера с помощью оператора `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Код целевого объекта "Файл событий" для расширенных событий в Базе данных SQL](sql-database-xevent-code-event-file.md)
    - Этап 1. PowerShell: создание контейнера хранилища Azure в облаке.
    - Этап 2. Transact-SQL: использование контейнера хранилища Azure.

## <a name="transact-sql-differences"></a>Отличия Transact-SQL


- При выполнении команды [CREATE EVENT SESSION](http://msdn.microsoft.com/library/bb677289.aspx) на сервере SQL Server используется предложение **ON SERVER** . В Базе данных SQL вместо него используется предложение **ON DATABASE** .


- Предложение **ON DATABASE** применяется также в командах Transact-SQL [ALTER EVENT SESSION](http://msdn.microsoft.com/library/bb630368.aspx) и [DROP EVENT SESSION](http://msdn.microsoft.com/library/bb630257.aspx).


- Мы рекомендуем включать параметр сеанса событий **STARTUP_STATE = ON** в операторы **CREATE EVENT SESSION** и **ALTER EVENT SESSION**.
    - Значение **= ON** поддерживает автоматический перезапуск после перенастройки логической базы данных из-за сбоя.

## <a name="new-catalog-views"></a>Новые представления каталога

Функцию расширенных событий поддерживают несколько [представлений каталога](http://msdn.microsoft.com/library/ms174365.aspx). Представления каталога сообщают *метаданные или определения* сеансов событий, созданных пользователями в текущей базе данных. Представления не возвращают сведения об экземплярах активных сеансов событий.

| Имя<br/>представления каталога | ОПИСАНИЕ |
|:--- |:--- |
| **sys.database_event_session_actions** |Возвращает строку для каждого действия с каждым событием в сеансе событий. |
| **sys.database_event_session_events** |Возвращает строку для каждого события в сеансе событий. |
| **sys.database_event_session_fields** |Возвращает строку для каждого настраиваемого столбца, который можно прямо задавать для событий и целевых объектов. |
| **sys.database_event_session_targets** |Возвращает строку для каждого целевого объекта события в сеансе событий. |
| **sys.database_event_sessions** |Возвращает строку для каждого сеанса событий в Базе данных SQL. |

В Microsoft SQL Server аналогичные представления каталогов имеют имена, содержащие *.server\_* вместо *.database\_*. Шаблон имени выглядит как **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Новые динамические административные представления [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx)

База данных SQL Azure включает [динамические административные представления (DMV)](http://msdn.microsoft.com/library/bb677293.aspx) , которые поддерживают расширенные события. DMV сообщают об *активных* сеансах событий.

| Имя DMV | ОПИСАНИЕ |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Возвращает сведения о действиях в сеансе событий. |
| **sys.dm_xe_database_session_events** |Возвращает сведения о событиях в сеансе. |
| **sys.dm_xe_database_session_object_columns** |Отображает значения конфигурации для объектов, привязанных к сеансу. |
| **sys.dm_xe_database_session_targets** |Возвращает сведения о целевых объектах сеанса. |
| **sys.dm_xe_database_sessions** |Возвращает строку для каждого сеанса событий, относящегося к текущей базе данных. |

В Microsoft SQL Server имена аналогичных представлений каталога не содержат указание *\_database* и выглядят следующим образом:

- **sys.dm_xe_sessions** вместо имени<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Общие DMV
Для расширенных событий существуют дополнительные DMV, которые являются общими и для Базы данных SQL Azure, и для Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Поиск доступных расширенных событий, действий и целевых объектов

Для получения списка доступных событий, действий и целевых объектов можно выполнить простую SQL-команду **SELECT** .

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Целевые объекты для сеансов событий в Базе данных SQL

Результаты сеансов событий в Базе данных SQL можно фиксировать в следующих целевых объектах:

- [Целевой объект "Кольцевой буфер"](http://msdn.microsoft.com/library/ff878182.aspx) — сохраняет данные события в памяти на недолгое время.
- [Целевой объект "Счетчик событий"](http://msdn.microsoft.com/library/ff878025.aspx) — подсчитывает все события, произошедшие за время сеанса расширенных событий.
- [Целевой объект "Файл событий"](http://msdn.microsoft.com/library/ff878115.aspx) — записывает все буферы в контейнер хранилища Azure.

API [трассировки событий для Windows (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) недоступен для расширенных событий в Базе данных SQL.

## <a name="restrictions"></a>Ограничения

В облачной среде Базы данных SQL имеется несколько отличий, связанных с обеспечением безопасности.

- Функция расширенных событий основана на изоляционной модели с одним клиентом. Сеанс событий в одной базе данных не может получить доступ к данным или событиям в другой базе данных.
- В контексте базы данных **master** оператор **CREATE EVENT SESSION** не вызывается.

## <a name="permission-model"></a>Модель разрешений

Чтобы вызвать оператор **CREATE EVENT SESSION**, требуется разрешение на **управление**. Владелец базы данных (dbo) имеет разрешение на **управление** .

### <a name="storage-container-authorizations"></a>Авторизации контейнера хранилища

Маркер SAS, сформированный для вашего контейнера хранилища Azure, должен указывать **rwl** для разрешений. Значение **rwl** обеспечивает следующие разрешения:

- чтение
- запись
- список

## <a name="performance-considerations"></a>Рекомендации по производительности

Существуют сценарии, в которых интенсивное использование расширенных событий может задействовать больше активной памяти, чем допустимо для сохранения работоспособности всей системы. В связи с этим система Базы данных SQL Azure динамически устанавливает и корректирует ограничения на объем активной памяти, который может использоваться сеансом событий. Динамические расчеты выполняются с учетом множества факторов.

При появлении сообщения о превышении максимального объема памяти можно предпринять следующие коррекционные меры:

- уменьшить количество одновременно запущенных сеансов событий;
- уменьшить объем памяти, заданный в предложении **MAX\_MEMORY**, с помощью операторов **CREATE** и **ALTER**.

### <a name="network-latency"></a>Задержки сети

Целевой объект **Файл событий** может столкнуться с медленной работой или отказами сети при сохранении данных в большие двоичные объекты хранилища Azure. Другие события в Базе данных SQL могут откладываться до установки подключения к сети. Такая задержка может замедлить вашу работу.

- Чтобы уменьшить этот риск, старайтесь не указывать для параметра **EVENT_RETENTION_MODE** значение **NO_EVENT_LOSS** в определениях сеанса событий.

## <a name="related-links"></a>Связанные ссылки

- [Использование Azure PowerShell со службой хранилища Azure](../storage/common/storage-powershell-guide-full.md)
- [Командлеты службы хранилища Azure](http://msdn.microsoft.com/library/dn806401.aspx)
- [Использование Azure PowerShell с хранилищем Azure](../storage/common/storage-powershell-guide-full.md) — статья содержит полную информацию о PowerShell и службе хранилища Azure.
- [Использование хранилища BLOB-объектов из .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)
- [Публикации в блоге Джонтана Кехайаса (Jonathan Kehayias) о расширенных событий в Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Веб-страница с *обновлениями службы* Azure с ограничением по параметру базы данных SQL Azure:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Другие статьи с примерами кода для работы с расширенными событиями доступны по приведенным ниже ссылкам. Обязательно проверяйте, предназначен ли пример для Microsoft SQL Server или для базы данных SQL Azure. После этого вы сможете решить, какие поправки нужно внести в пример кода.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
