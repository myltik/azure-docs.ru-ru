<properties 
	pageTitle="Код кольцевого буфера XEvent для Базы данных SQL | Microsoft Azure" 
	description="Содержит пример кода Transact-SQL, обеспечивающего простоту и удобство использования целевого объекта ";Кольцевой буфер"; в Базе данных SQL Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/30/2015" 
	ms.author="genemi"/>


# Код целевого объекта "Кольцевой буфер" для расширенных событий в базе данных SQL


Вам нужен полный образец кода для простой и быстрой регистрации и сообщения сведений о расширенных событиях в процессе тестирования. Самый простой целевой объект для данных расширенных событий — это [целевой объект "Кольцевой буфер"](http://msdn.microsoft.com/library/ff878182.aspx).


В этом разделе представлен пример кода Transact-SQL, который:


1. создает таблицу с данными для демонстрации;

2. создает сеанс для существующего расширенного события, а именно **sqlserver.sql\_statement\_starting**.
 - Событие ограничивается операторами SQL, содержащими определенную строку обновления: **оператор LIKE '%UPDATE tabEmployee%'**.
 - Выбирает отправку выходных данных события в целевой объект типа "Кольцевой буфер", а именно **package0.ring\_buffer**.

3. Запускает сеанс событий.

4. Выдает пару простых операторов SQL UPDATE.

5. Выдает оператор SQL SELECT для получения выходных данных события из кольцевого буфера.
 - **sys.dm\_xe\_database\_session\_targets** и другие динамические административные представления объединяются.

6. Останавливает сеанс событий.

7. Удаляет целевой объект "Кольцевой буфер" для освобождения ресурсов.

8. Удаляет сеанс событий и демонстрационную таблицу.


## Предварительные требования


- Учетная запись и подписка Azure. Вы можете зарегистрироваться, чтобы получить [бесплатную пробную версию](http://azure.microsoft.com/pricing/free-trial/).


- Любая база данных, позволяющая создать таблицу.
 - При необходимости вы можете быстро [создать демонстрационную базу данных **AdventureWorksLT**](sql-database-get-started.md).


- SQL Server Management Studio (ssms.exe): предварительная версия августа 2015 года или более поздняя. Ресурсы для загрузки последней версии файла ssms.exe:
 - [Ссылка в статье.](http://msdn.microsoft.com/library/mt238290.aspx)
 - [Прямая ссылка на загрузку.](http://go.microsoft.com/fwlink/?linkid=616025)
 - Корпорация Майкрософт рекомендует периодически обновлять файл ssms.exe, например ежемесячно.


## Пример кода


С небольшими изменениями приведенный ниже пример кода кольцевого буфера можно выполнять как в базе данных SQL Azure, так и на Microsoft SQL Server. Разница заключается в наличии узла '\_database' в имени некоторых динамических административных представлений в части FROM (шаг 5). Например:

- sys.dm\_xe**\_database**\_session\_targets
- sys.dm\_xe\_session\_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
	(SELECT * FROM sys.objects
		WHERE type = 'U' and name = 'tabEmployee')
BEGIN
	DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
	EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
	EmployeeId           int                not null  identity(1,1),
	EmployeeKudosCount   int                not null  default 0,
	EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
	VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
	(SELECT * from sys.database_event_sessions
		WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
	DROP EVENT SESSION eventsession_gm_azuresqldb51
		ON DATABASE;
END
GO


CREATE
	EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	ADD EVENT
		sqlserver.sql_statement_starting
			(
			ACTION (sqlserver.sql_text)
			WHERE statement LIKE '%UPDATE tabEmployee%'
			)
	ADD TARGET
		package0.ring_buffer
			(SET
				max_memory = 500   -- Units of KB.
			);
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## Содержимое кольцевого буфера


Для выполнения данного примера кода мы использовали файл ssms.exe.


Чтобы просмотреть результаты, мы щелкнули ячейку под заголовком столбца **target\_data\_XML**.

Затем в области результатов мы щелкнули ячейку под заголовком столбца **target\_data\_XML**. В результате в файле ssms.exe была создана дополнительная вкладка, в которой в виде XML-кода отображается содержимое итоговой ячейки.


Выходные данные показаны в приведенном ниже блоке. Он выглядит длинным, но содержит всего два элемента **<event>**.


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### Освобождение ресурсов, занятых кольцевым буфером


По завершении работы с кольцевым буфером его можно удалить и освободить таким образом ресурсы. Для этого используется оператор **ALTER**:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	DROP TARGET package0.ring_buffer;
GO
```


Определение сеанса событий обновляется, но не удаляется. Впоследствии в сеанс событий можно добавить еще один экземпляр кольцевого буфера.


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	ADD TARGET
		package0.ring_buffer
			(SET
				max_memory = 500   -- Units of KB.
			);
```


## Дополнительные сведения


Основная статья о расширенных событиях в Базе данных SQL Azure:


- [Рекомендации по работе с расширенными событиями в Базе данных SQL](sql-database-xevent-db-diff-from-svr.md), где сравниваются аспекты расширенных событий в Базе данных SQL Azure и в Microsoft SQL Server.


Другие статьи с примерами кода для работы с расширенными событиями доступны по приведенным ниже ссылкам. Обязательно проверяйте, предназначен ли пример для Microsoft SQL Server или для базы данных SQL Azure. После этого вы сможете решить, какие поправки нужно внести в пример кода.


- Пример кода для Базы данных SQL Azure: [Код целевого объекта "Файл событий" для расширенных событий в Базе данных SQL](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->

<!---HONumber=AcomDC_0107_2016-->