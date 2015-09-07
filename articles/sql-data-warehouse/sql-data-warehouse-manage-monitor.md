<properties
   pageTitle="Мониторинг рабочей нагрузки с помощью динамических административных представлений | Microsoft Azure"
	description="Узнайте о том, как организовать отслеживание рабочей нагрузки с помощью динамических административных представлений."
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="sahaj08"
	manager="barbkess"
	editor=""/>

<tags
   ms.service="sql-data-warehouse"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-services"
	ms.date="08/06/2015"
	ms.author="sahajs"/>

# Мониторинг рабочей нагрузки с помощью динамических административных представлений

В этой статье рассказывается, как использовать динамические административные представления (DMV) для наблюдения за рабочей нагрузкой и проверки выполнения запросов в хранилище данных SQL Azure.




## Разрешения

В хранилище данных SQL для запроса динамического административного представления требуется наличие разрешений **VIEW DATABASE STATE**. Разрешение **VIEW DATABASE STATE** возвращает сведения обо всех объектах в текущей базе данных. Чтобы предоставить разрешение **VIEW DATABASE STATE** определенному пользователю базы данных, выполните следующий запрос:

```

GRANT VIEW DATABASE STATE TO database_user;

```




## Мониторинг подключений

Для получения сведений о подключениях, установленных к базе данных в хранилище данных SQL Azure,можно использовать представление *sys.dm\_pdw\_nodes\_exec\_connections*. Кроме того, представление *sys.dm\_exec\_sessions* может оказаться полезным при получении сведений обо всех активных подключениях пользователя.

```

SELECT * FROM sys.dm_pdw_nodes_exec_connections;
SELECT * FROM sys.dm_pdw_nodes_exec_sessions;

```


Используйте следующий запрос для получения сведений о текущем подключении.

```

SELECT * 
FROM sys.dm_pdw_nodes_exec_connections AS c 
   JOIN sys.dm_pdw_nodes_exec_sessions AS s 
   ON c.session_id = s.session_id 
WHERE c.session_id = @@SPID;

```





## Проверка выполнения запроса
Могут возникнуть ситуации, когда запрос не завершен или выполняется дольше, чем ожидалось. В таких случаях можно использовать следующие шаги для сбора данных и упростить работу с ними.



### Шаг 1. Поиск запроса для анализа

```

-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the longest running queries
SELECT * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;

```

Сохраните идентификатор запроса.


  
### Шаг 2. Проверка того, ожидает ли запрос ресурсы

```

-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status, 
      requests.start_time,  
      waits.type,  
      waits.object_type, 
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits 
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id 
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;

```


В результатах представленного выше запроса содержатся сведения о состоянии ожидания запроса.

- Если запрос ожидает ресурсы из другого запроса, то состояние будет **AcquireResources**.
- Если запрос содержит все необходимые ресурсы и не находится в состоянии ожидания, то состояние будет **Granted**. В этом случае следует просмотреть этапы запроса.




### Шаг 3. Поиск этапа запроса, который выполняется дольше всех

Используйте идентификатор запроса для получения списка всех этапов распределенного запроса. Найдите самый долго выполняющийся этап, обратившись к сведениям об общем затраченном времени.

```

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.
 
SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;

```

Сохраните индекс самого длительного этапа.

Перейдите к столбцу *operation\_type* самого долго выполняющегося этапа запроса:

- Перейдите к шагу 4a для **операций SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Перейдите к шагу 4b для **операций перемещения данных**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.




### Шаг 4а. Поиск сведений о ходе выполнения этапа SQL

Используйте идентификатор запроса и индекса этапа для получения сведений о распределении запросов SQL Server в рамках этапа SQL в запросе. Сохраните идентификатор узла и SPID.

```

-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;

```


Используйте следующий запрос, чтобы получить план выполнения SQL Server для этапа SQL на определенном узле.

```

-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node. 
-- Replace node_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```



### Шаг 4b. Поиск сведений о ходе выполнения этапа DMS

Используйте идентификатор запроса и индекса шага для получения сведений об этапе перемещения данных, выполняющемся при каждом распределении.

```

-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.
 
SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Перейдите к столбцу *total\_elapsed\_time*, чтобы просмотреть, имеется ли определенная операция распространения, выполнение которой занимает значительно больше времени, чем другие, для перемещения данных. 
- Обратитесь к столбцу *rows\_processed* для длительно выполняющейся операции распространения и проверьте, является ли количество перемещаемых этой операцией строк значительно большим по сравнению с другими. Это указывает на неравномерное смещение данных в запросе.





## Проверка неравномерного смещения данных

```

-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");

```


Результат этого запроса содержит сведения о количестве строк таблицы, которое хранится в каждом из 60 распределений базы данных. Для достижения оптимальной производительности строки в распределенной таблице должны быть равномерно распределены по всем распределениям. Дополнительные сведения можно найти в разделе, посвященном [конструктору таблиц][].



## Дальнейшие действия
Дополнительные советы по управлению хранилищем данных SQL можно найти в разделе [сведений о средствах управления][].

<!--Image references-->

<!--Article references-->
[сведений о средствах управления]: sql-data-warehouse-overview-manage.md
[конструктору таблиц]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!---HONumber=August15_HO9-->