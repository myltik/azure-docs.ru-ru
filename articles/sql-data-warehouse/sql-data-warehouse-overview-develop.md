<properties
   pageTitle="Разработка решений для хранилища данных SQL | Microsoft Azure"
   description="Методы разработки и рекомендации для построения решений с помощью хранилища данных SQL Azure."
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

# Разработка решений для хранилища данных SQL

Ознакомьтесь со всеми статьями о разработке, чтобы лучше понять принципы разработки, рекомендации и приемы кодирования для хранилища данных SQL.

## Основные понятия
В приведенных ниже статьях описаны основные понятия и проектные решения, с которыми необходимо ознакомиться для разработки распределенного хранилища данных с помощью хранилища данных SQL:

- [подключения][];
- [параллелизм][];
- [транзакции][];
- [пользовательские схемы][];
- [проектирование таблиц][];
- [ключи хэш-распределения][];
- [разделы таблицы][];
- [CTAS][];
- [статистика][].

## Методы разработки
В следующих статьях описаны методы кодирования, советы и рекомендации по разработке решений с помощью хранилища данных SQL.

- [хранимые процедуры][];
- [метки][];
- [представления][];
- [временные таблицы][];
- [динамический SQL][];
- [циклы][];
- [переименование объектов][];
- [сведение данных][];
- [группировка по параметрам][];
- [присваивание значения переменной][].

## Дальнейшие действия
После ознакомления со статьями о разработке перейдите в [Справочник по Transact-SQL][], чтобы получить дополнительные сведения о поддерживаемом синтаксисе для хранилища данных SQL.

<!--Image references-->

<!--Article references-->
[параллелизм]: sql-data-warehouse-develop-concurrency.md
[подключения]: sql-data-warehouse-develop-connections.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[динамический SQL]: sql-data-warehouse-develop-dynamic-sql.md
[группировка по параметрам]: sql-data-warehouse-develop-group-by-options.md
[ключи хэш-распределения]: sql-data-warehouse-develop-hash-distribution-key.md
[метки]: sql-data-warehouse-develop-label.md
[циклы]: sql-data-warehouse-develop-loops.md
[сведение данных]: sql-data-warehouse-develop-pivot-unpivot.md
[переименование объектов]: sql-data-warehouse-develop-rename.md
[статистика]: sql-data-warehouse-develop-statistics.md
[хранимые процедуры]: sql-data-warehouse-develop-stored-procedures.md
[проектирование таблиц]: sql-data-warehouse-develop-table-design.md
[разделы таблицы]: sql-data-warehouse-develop-table-partitions.md
[временные таблицы]: sql-data-warehouse-develop-temporary-tables.md
[транзакции]: sql-data-warehouse-develop-transactions.md
[пользовательские схемы]: sql-data-warehouse-develop-user-defined-schemas.md
[присваивание значения переменной]: sql-data-warehouse-develop-variable-assignment.md
[представления]: sql-data-warehouse-develop-views.md

[Справочник по Transact-SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO4-->