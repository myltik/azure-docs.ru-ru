<properties
	pageTitle="Расширенные события в Базе данных SQL | Microsoft Azure"
	description="В статье описываются расширенные события (XEvents) в Базе данных SQL Azure и отличия соответствующих сеансов событий от сеансов событий в Microsoft SQL Server."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""
	tags=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="genemi"/>


# Расширенные события в Базе данных SQL


В этом разделе объясняется, чем расширенные события в Базе данных SQL Azure отличается от расширенных событий в Microsoft SQL Server.


- В Базе данных SQL версии 12 функция расширенных событий появилась во второй половине 2015 года.
- SQL Server включает расширенные события с 2008 года.
- Набор функций расширенных событий в Базе данных SQL представляет собой устойчивое подмножество функций SQL Server.


*XEvents* — это неофициальное обозначение расширенных событий, которое встречается в блоках и других неофициальных источниках.


> [AZURE.NOTE] На октябрь 2015 года функция сеанса расширенных событий активирована в Базе данных SQL на уровне предварительной версии. Дата выхода общедоступной версии пока не установлена.
>
> Сообщения о выходе общедоступных версий публикуются на странице [Обновления служб](https://azure.microsoft.com/updates/?service=sql-database) Azure.


## Предварительные требования


В данной статье предполагается, чтобы вы уже знакомы со следующими компонентами:


- [Служба Базы данных SQL Azure](https://azure.microsoft.com/services/sql-database/);


- [Расширенные события](http://msdn.microsoft.com/library/bb630282.aspx) в Microsoft SQL Server.
 - Большинство документации о расширенных событиях относится и к SQL Server, и к Базе данных SQL.


При выборе файла событий в качестве [целевого объекта](#AzureXEventsTargets) пригодится знание следующих компонентов:


- [Служба хранилища Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
 - [Использование Azure PowerShell с хранилищем Azure](../storage/storage-powershell-guide-full.md) — статья содержит полную информацию о PowerShell и службе хранилища Azure.


## Примеры кода


Связанные разделы содержат два примера кода.


- [Код целевого объекта "Кольцевой буфер" для расширенных событий в Базе данных SQL](sql-database-xevent-code-ring-buffer.md)
 - Простой короткий сценарий Transact-SQL.
 - В этой статье с примером кода подчеркивается, что после завершения работы с целевым объектом "Кольцевой буфер" необходимо освободить задействованные им ресурсы с помощью оператора изменения и удаления `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;`. Впоследствии вы сможете добавить другой экземпляр кольцевого буфера с помощью оператора `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Код целевого объекта "Файл событий" для расширенных событий в Базе данных SQL](sql-database-xevent-code-event-file.md)
 - Этап 1. PowerShell: создание контейнера хранилища Azure в облаке.
 - Этап 2. Transact-SQL: использование контейнера хранилища Azure.


## Отличия Transact-SQL


- При выполнении команды [CREATE EVENT SESSION](http://msdn.microsoft.com/library/bb677289.aspx) на сервере SQL Server используется предложение **ON SERVER**. В Базе данных SQL вместо него используется предложение **ON DATABASE**.


- Предложение **ON DATABASE** применяется также в командах Transact-SQL [ALTER EVENT SESSION](http://msdn.microsoft.com/library/bb630368.aspx) и [DROP EVENT SESSION](http://msdn.microsoft.com/library/bb630257.aspx).


- Рекомендуется включать параметр сеанса событий **STARTUP\_STATE = ON** в операторы **CREATE EVENT SESSION** и **ALTER EVENT SESSION**.
 - Значение **= ON** поддерживает автоматический перезапуск после перенастройки логической базы данных из-за сбоя.


## Новые представления каталога


Функцию расширенных событий поддерживают несколько [представлений каталога](http://msdn.microsoft.com/library/ms174365.aspx). Представления каталога сообщают *метаданные или определения* сеансов событий, созданных пользователями в текущей базе данных. Представления не возвращают сведения об экземплярах активных сеансов событий.


| Имя<br/>представления каталога | Описание |
| :-- | :-- |
| **sys.database\_event\_session\_actions** | Возвращает строку для каждого действия с каждым событием в сеансе событий. |
| **sys.database\_event\_session\_events** | Возвращает строку для каждого события в сеансе событий. |
| **sys.database\_event\_session\_fields** | Возвращает строку для каждого настраиваемого столбца, который можно прямо задавать для событий и целевых объектов. |
| **sys.database\_event\_session\_targets** | Возвращает строку для каждого целевого объекта события в сеансе событий. |
| **sys.database\_event\_sessions** | Возвращает строку для каждого сеанса событий в Базе данных SQL. |


В Microsoft SQL Server, аналогичные представления каталогов имеют имена, содержащие *.server\_* вместо *.database\_*. Шаблон имени выглядит как **sys.server\_event\_%**.


## Новые динамические административные представления [\(DMV\)](http://msdn.microsoft.com/library/ms188754.aspx)


База данных SQL Azure включает [динамические административные представления \(DMV\)](http://msdn.microsoft.com/library/bb677293.aspx), которые поддерживают расширенные события. DMV сообщают об *активных* сеансах событий.


| Имя DMV | Описание |
| :-- | :-- |
| **sys.dm\_xe\_database\_session\_event\_actions** | Возвращает сведения о действиях в сеансе событий. |
| **sys.dm\_xe\_database\_session\_events** | Возвращает сведения о событиях в сеансе. |
| **sys.dm\_xe\_database\_session\_object\_columns** | Отображает значения конфигурации для объектов, привязанных к сеансу. |
| **sys.dm\_xe\_database\_session\_targets** | Возвращает сведения о целевых объектах сеанса. |
| **sys.dm\_xe\_database\_sessions** | Возвращает строку для каждого сеанса событий, относящегося к текущей базе данных. |


В Microsoft SQL Server имена аналогичных представлений каталога не содержат указание *\_database* и выглядят следующим образом:


- **sys.dm\_xe\_sessions** вместо имени <br/>**sys.dm\_xe\_database\_sessions**.


### Общие DMV


Для расширенных событий существуют дополнительные DMV, которые являются общими и для Базы данных SQL Azure, и для Microsoft SQL Server:


- **sys.dm\_xe\_map\_values**
- **sys.dm\_xe\_object\_columns**
- **sys.dm\_xe\_objects**
- **sys.dm\_xe\_packages**



 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## Поиск доступных расширенных событий, действий и целевых объектов


Для получения списка доступных событий, действий и целевых объектов можно выполнить простую SQL-команду **SELECT**.


```
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



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## Целевые объекты для сеансов событий в Базе данных SQL


Результаты сеансов событий в Базе данных SQL можно фиксировать в следующих целевых объектах:


- [Целевой объект "Кольцевой буфер"](http://msdn.microsoft.com/library/ff878182.aspx) — сохраняет данные события в памяти на недолгое время.
- [Целевой объект "Счетчик событий"](http://msdn.microsoft.com/library/ff878025.aspx) — подсчитывает все события, произошедшие за время сеанса расширенных событий.
- [Целевой объект "Файл событий"](http://msdn.microsoft.com/library/ff878115.aspx) — записывает все буферы в контейнер хранилища Azure.


API [трассировки событий для Windows \(ETW\)](http://msdn.microsoft.com/library/ms751538.aspx) недоступен для расширенных событий в Базе данных SQL.


## Ограничения


Облачная среда Базы данных SQL имеет несколько отличий, связанных с обеспечением безопасности.


- Функция расширенных событий основана на изоляционной модели с одним клиентом. Сеанс событий в одной базе данных не может получить доступ к данным или событиям в другой базе данных.

- В контексте **главной** базы данных оператор **CREATE EVENT SESSION** не вызывается.


## Модель разрешений


Для вызова оператора **CREATE EVENT SESSION** требуется разрешение на **управление**. Владелец базы данных \(dbo\) имеет разрешение на **управление**.


### Авторизации контейнера хранилища


Маркер SAS, сформированный для вашего контейнера хранилища Azure, должен указывать **rwl** для разрешений. Это обеспечивает следующие разрешения:


- чтение
- запись
- список


## Рекомендации по производительности


Существуют сценарии, в которых интенсивное использование расширенных событий может задействовать больше активной памяти, чем допустимо для сохранения работоспособности всей системы. В связи с этим система Базы данных SQL Azure динамически устанавливает и корректирует ограничения на объем активной памяти, который может использоваться сеансом событий. Динамические расчеты выполняются с учетом множества факторов.


При появлении сообщения о превышении максимального объема памяти можно предпринять следующие коррекционные меры:


- уменьшить количество одновременно запущенных сеансов событий;


- уменьшить объем памяти, заданный в предложении **MAX\_MEMORY**, с помощью операторов **CREATE** и **ALTER**.


### Задержки сети


Целевой объект **Файл событий** может столкнуться с медленной работой или отказами сети при сохранении данных в большие двоичные объекты хранилища Azure. Другие события в Базе данных SQL могут откладываться до установки подключения к сети. Такая задержка может замедлить вашу работу.

- Чтобы уменьшить этот риск, старайтесь не указывать для параметра **EVENT\_RETENTION\_MODE** значение **NO\_EVENT\_LOSS** в определениях сеанса событий.


## Связанные ссылки


- [Использование Azure PowerShell со службой хранилища Azure](../storage/storage-powershell-guide-full.md)
- [Командлеты службы хранилища Azure](http://msdn.microsoft.com/library/dn806401.aspx)


- [Использование Azure PowerShell с хранилищем Azure](../storage/storage-powershell-guide-full.md) — статья содержит полную информацию о PowerShell и службе хранилища Azure.
- [Использование хранилища BLOB-объектов из .NET](../storage/storage-dotnet-how-to-use-blobs.md)


- [CREATE CREDENTIAL \(Transact-SQL\)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION \(Transact-SQL\)](http://msdn.microsoft.com/library/bb677289.aspx)


- [Публикации в блоге Джонтана Кехайаса \(Jonathan Kehayias\) о расширенных событий в Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


Другие статьи с примерами кода для работы с расширенными событиями доступны по приведенным ниже ссылкам. Обязательно проверяйте, предназначен ли пример для Microsoft SQL Server или для базы данных SQL Azure. После этого вы сможете решить, какие поправки нужно внести в пример кода.


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->

<!---HONumber=AcomDC_0413_2016-->