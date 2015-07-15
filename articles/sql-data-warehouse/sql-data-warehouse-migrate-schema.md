<properties
   pageTitle="Перенос схемы в хранилище данных SQL | Microsoft Azure"
   description="Советы по переносу схемы в хранилище данных SQL Azure для разработки решений."
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
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Перенос схемы в хранилище данных SQL#

Следующие сводные данные помогут вам понять различия между SQL Server и хранилищем данных SQL и перенести базу данных.

### Компоненты таблицы
Хранилище данных SQL не использует и не поддерживает такие компоненты:

- первичные ключи;
- внешние ключи;
- проверочные ограничения;
- уникальные ограничения;
- уникальные индексы;
- вычисляемые столбцы;
- разреженные столбцы;
- пользовательские типы;
- индексированные представления;
- удостоверения;
- последовательности;
- триггеры;
- синонимы;

### различия типов данных.
Хранилище данных SQL поддерживает общие типы бизнес-данных:

- bigint;
- binary;
- bit;
- char;
- дата;
- datetime;
- datetime2;
- datetimeoffset;
- decimal;
- float;
- int;
- money;
- nchar;
- nvarchar;
- real;
- smalldatetime;
- smallint;
- smallmoney;
- Twitter в режиме реального
- tinyint;
- varbinary;
- varchar.

С помощью этого запроса можно в хранилище данных определить столбцы, которые содержат несовместимые типы.

```
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'numeric'
                ,   'sql_variant'
                ,   'sysname'
                ,   'text'
                ,   'timestamp'
                ,   'uniqueidentifier'
                ,   'xml'
                )

OR  (   y.[name] IN (  'nvarchar','varchar','varbinary')
    AND c.[max_length] = -1
    )
OR  y.[is_user_defined] = 1
;

```

Запрос включает любые пользовательские типы данных, которые также не поддерживаются.

Если в базе данных есть неподдерживаемые типы, это не проблема. Некоторые альтернативные варианты для использования предложены ниже.

Используйте такие варианты.

- Вместо **geometry** используйте varbinary.
- Вместо **geography** используйте varbinary.
- **hierarchyid** — этот тип CLR не поддерживается.
- Вместо **image**, **text**, **ntext** используйте varchar/nvarchar (чем меньше, тем лучше).
- Вместо **nvarchar(max)** используйте varchar(4000) или меньшую версию для лучшей производительности.
- Вместо **numeric** используйте decimal.
- Не используйте **sql_variant**. Вместо этого разделите столбец на несколько строго типизированных столбцов.
- Вместо **sysname** используйте nvarchar(128).
- **table** преобразуйте во временные таблицы.
- Не используйте **timestamp**, но переработайте код, чтобы использовать datetime2 и функцию `CURRENT_TIMESTAMP`. Примечание. Вы не можете использовать current_timestamp как ограничение по умолчанию. Значение автоматически не обновляется. Если нужно перенести значения rowversion из типизированного столбца timestamp, используйте binary(8) или varbinary(8) для значений версии строки NOT NULL или NULL.
- Вместо **nvarchar(max)** используйте varchar(8000) или меньшую версию для лучшей производительности.
- Вместо **uniqueidentifier** используйте varbinary(8).
- Преобразуйте **user defined types** обратно в их исходный тип, если это возможно.
- Вместо **xml** используйте varchar(8000) или меньшую версию для лучшей производительности. Разделите на столбцы при необходимости.

Частичная поддержка.

- Ограничения по умолчанию поддерживает только литералы и константы. Недетерминированные выражения или функции, такие как `GETDATE()` или `CURRENT_TIMESTAMP`, не поддерживаются.

> [AZURE.NOTE]Настройте таблицы, чтобы максимальный размер строки, включая полную длину столбцов переменной длины, не превышал 32 767 байт. Вы можете задать строку с данными переменной длины, размер которой превышает это значение, но вы не сможете вставить эти данные в таблицу. Кроме того, попробуйте ограничить размер столбцов переменной длины, чтобы еще больше повысить пропускную способность, нужную для выполнения запросов.

## Дальнейшие действия
После успешного переноса схемы базы данных в хранилище данных SQL вы можете переходить к одной из приведенных ниже статей.

- [Перенос данных][]
- [Перенос кода][]

Дополнительные советы по разработке см. в статье [Общие сведения о разработке][].

<!--Image references-->

<!--Article references-->
[Перенос кода]: sql-data-warehouse-migrate-code.md
[Перенос данных]: sql-data-warehouse-migrate-data.md
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=July15_HO1-->