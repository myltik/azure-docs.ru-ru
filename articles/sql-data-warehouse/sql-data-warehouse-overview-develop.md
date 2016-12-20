---
title: "Проектные решения и методики программирования для разработки для хранилища данных SQL | Документация Майкрософт"
description: "Основные понятия разработки, проектные решения, рекомендации и методики программирования для хранилища данных SQL."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 996e3afc-c21c-4e21-b9df-997f953f6dfd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d1c2255264a4240eecc51199050f8406d141ace2


---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Проектные решения и методики программирования для хранилища данных SQL
Ознакомьтесь со статьями о разработке, чтобы лучше понять основные проектные решения, рекомендации и методики программирования для хранилища данных SQL.

## <a name="key-design-decisions"></a>Основные проектные решения
В приведенных ниже статьях описаны основные понятия и проектные решения, с которыми необходимо ознакомиться для разработки распределенного хранилища данных с помощью хранилища данных SQL:

* [подключения;][подключения;]
* [параллелизм;][параллелизм;]
* [транзакции;][транзакции;]
* [пользовательские схемы;][пользовательские схемы;]
* [распределение таблиц;][распределение таблиц;]
* [индексы таблицы;][индексы таблицы;]
* [разделы таблицы;][разделы таблицы;]
* [CTAS;][CTAS;]
* [статистика.][статистика.]

## <a name="development-recommendations-and-coding-techniques"></a>Рекомендации по разработке и методики программирования
В следующих статьях описаны методики программирования, советы и рекомендации по разработке для хранилища данных SQL:

* [хранимые процедуры;][хранимые процедуры;]
* [метки;][метки;]
* [представления;][представления;]
* [временные таблицы;][временные таблицы;]
* [динамический SQL;][динамический SQL;]
* [циклы;][циклы;]
* [группировка по параметрам;][группировка по параметрам;]
* [присваивание значения переменной.][присваивание значения переменной.]

## <a name="next-steps"></a>Дальнейшие действия
После ознакомления со статьями о разработке перейдите к [справочным материалам по Transact-SQL][Справочник по Transact-SQL], чтобы получить дополнительные сведения о поддерживаемом синтаксисе для хранилища данных SQL.

<!--Image references-->

<!--Article references-->
[параллелизм;]: ./sql-data-warehouse-develop-concurrency.md
[подключения;]: ./sql-data-warehouse-connect-overview.md
[CTAS;]: ./sql-data-warehouse-develop-ctas.md
[динамический SQL;]: ./sql-data-warehouse-develop-dynamic-sql.md
[группировка по параметрам;]: ./sql-data-warehouse-develop-group-by-options.md
[метки;]: ./sql-data-warehouse-develop-label.md
[циклы;]: ./sql-data-warehouse-develop-loops.md
[статистика.]: ./sql-data-warehouse-tables-statistics.md
[хранимые процедуры;]: ./sql-data-warehouse-develop-stored-procedures.md
[распределение таблиц;]: ./sql-data-warehouse-tables-distribute.md
[индексы таблицы;]: ./sql-data-warehouse-tables-index.md
[разделы таблицы;]: ./sql-data-warehouse-tables-partition.md
[временные таблицы;]: ./sql-data-warehouse-tables-temporary.md
[транзакции;]: ./sql-data-warehouse-develop-transactions.md
[пользовательские схемы;]: ./sql-data-warehouse-develop-user-defined-schemas.md
[присваивание значения переменной.]: ./sql-data-warehouse-develop-variable-assignment.md
[представления;]: ./sql-data-warehouse-develop-views.md
[Справочник по Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[переименование объектов]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


