<properties
   pageTitle="Переименование в хранилище данных SQL | Microsoft Azure"
   description="Советы для переименованию объектов и баз данных в хранилище данных SQL Azure для разработки решений."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Переименование в хранилище данных SQL
SQL Server позволяет переименовывать объекты и базы данных с помощью хранимых процедур sp_rename и sp_renamedb соответственно.

В хранилище данных SQL для достижения той же цели используется синтаксис DDL. Команды DDL: RENAME OBJECT и RENAME DATABASE.

## Переименование объекта

Важно понимать, что меняется только имя объекта; в остальной части системы автоматическая замена имени переименованного объекта не происходит. Это значит, что все представления, в которых используется старое имя объекта, станут недействительны, пока не будет создан объект с таким старым именем. В связи с этим команда `RENAME OBJECT` часто используется в парах.

```
RENAME OBJECT product.item to item_old;
RENAME OBJECT product.item_new to item;
```

## Переименование базы данных

Базы данных переименовываются практически так же, как и объекты.

```
RENAME DATABASE AdventureWorks TO Contoso;
```

Важно помнить, что переименовать объект или базу данных нельзя, если с ними работают другие пользователи. Для начала необходимо завершить открытые сеансы. Для завершения сеанса используйте команду [KILL][]. Команду KILL следует использовать с осторожностью. После ее выполнения целевой сеанс будет завершен, а все несохраненные изменения — отменены.

> [AZURE.NOTE]Сеансы в хранилище данных SQL обозначаются префиксом SID — укажите его и номер сеанса при вызове команды KILL. Например, команда KILL ’SID1234’ приведет к завершению сеанса 1234 при условии, что у вас есть разрешения, необходимые для ее выполнения.

## Завершение сеансов
Для переименования базы данных необходимо завершить сеансы подключения к хранилищу данных SQL. Следующий запрос создает отдельный список команд KILL для отмены подключений (кроме текущего сеанса):

```
SELECT 'KILL '''+session_id+''''
FROM	sys.dm_pdw_exec_requests r
WHERE r.session_id <> SESSION_ID()
AND EXISTS
(	SELECT 	*
	FROM 	sys.dm_pdw_lock_waits w
	WHERE 	r.session_id = w.session_id
)
GROUP BY session_id
;
```

## Переключение схемы
Изменить схему, к которой относится объект, можно с помощью команды `ALTER SCHEMA`:

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```


## Дальнейшие действия
Дополнительные советы по разработке см. в статье [Общие сведения о разработке][].

<!--Image references-->

<!--Article references-->
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/ru-ru/library/ms173730.aspx

<!--Other Web references-->
[Azure management portal]: http://portal.azure.com/

<!---HONumber=July15_HO4-->