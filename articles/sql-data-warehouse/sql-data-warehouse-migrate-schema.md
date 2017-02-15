---
title: "Перенос схемы в хранилище данных SQL | Документация Майкрософт"
description: "Советы по переносу схемы в хранилище данных SQL Azure для разработки решений."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1ac4db6d9404567be0d3d12f91a96fa4e31ef3bf


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Перенос схемы в хранилище данных SQL
Следующие сводные данные помогут вам понять различия между SQL Server и хранилищем данных SQL и перенести базу данных.

## <a name="table-migration"></a>Перенос таблиц
При переносе таблиц следует ознакомиться с возможностями таблиц хранилища данных SQL.  Вы можете начать с [общих сведений о таблицах][обзора таблиц].  В этой статье рассмотрены наиболее важные вопросы, касающиеся создания таблицы, например статистика таблиц, распределения, секционирование и индексирование.  В ней также рассматриваются некоторые [неподдерживаемые функции таблиц][неподдерживаемые функции таблиц] и способы их обойти.

Хранилище данных SQL поддерживает распространенные типы бизнес-данных.  Список поддерживаемых и [неподдерживаемых типов данных][Неподдерживаемые типы данных] см. в статье [Типы данных][Типы данных].  В [этой статье][Типы данных] также содержится запрос для определения [неподдерживаемых типов данных][Неподдерживаемые типы данных].  При преобразовании типов данных обязательно ознакомьтесь с [рекомендациями по типам данных][Рекомендации относительно типов данных].

## <a name="next-steps"></a>Дальнейшие действия
После успешного переноса схемы базы данных в хранилище данных SQL вы можете перейти к одной из приведенных ниже статей.

* [Перенос данных][Перенос данных]
* [Перенос кода][Перенос кода]

Дополнительные рекомендации по работе с хранилищем данных SQL см. в статье [Рекомендации по использованию хранилища данных SQL Azure][Рекомендации по использованию хранилища данных SQL Azure].

<!--Image references-->

<!--Article references-->
[Перенос кода]: ./sql-data-warehouse-migrate-code.md
[Перенос данных]: ./sql-data-warehouse-migrate-data.md
[Рекомендации по использованию хранилища данных SQL Azure]: ./sql-data-warehouse-best-practices.md
[обзора таблиц]: ./sql-data-warehouse-tables-overview.md
[неподдерживаемые функции таблиц]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Типы данных]: ./sql-data-warehouse-tables-data-types.md
[Неподдерживаемые типы данных]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Рекомендации относительно типов данных]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


