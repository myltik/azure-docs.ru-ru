---
title: "Перенос схемы в хранилище данных SQL | Документация Майкрософт"
description: "Советы по переносу схемы в хранилище данных SQL Azure для разработки решений."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0630f43642a0be98c470032d32b74ca14ee144e5
ms.contentlocale: ru-ru
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Перенос схемы в хранилище данных SQL
Следующие сводные данные помогут вам понять различия между SQL Server и хранилищем данных SQL и перенести базу данных.

## <a name="table-migration"></a>Перенос таблиц
При переносе таблиц следует ознакомиться с возможностями таблиц хранилища данных SQL.  Вы можете начать с [обзора таблиц][table overview].  В этой статье рассмотрены наиболее важные вопросы, касающиеся создания таблицы, например статистика таблиц, распределения, секционирование и индексирование.  В ней также рассматриваются некоторые [неподдерживаемые функции таблиц][unsupported table features] и способы их обойти.

Хранилище данных SQL поддерживает распространенные типы бизнес-данных.  Список поддерживаемых и [неподдерживаемых типов данных][unsupported data types] см. в статье [Типы данных таблиц в хранилище данных SQL][data types].  В [этой статье][data types] также содержится запрос для определения [неподдерживаемых типов данных][unsupported data types].  При преобразовании типов данных обязательно ознакомьтесь с [рекомендациями по типам данных][data type best practices].

## <a name="next-steps"></a>Дальнейшие действия
После успешного переноса схемы базы данных в хранилище данных SQL вы можете перейти к одной из приведенных ниже статей.

* [Перенос данных][Migrate your data]
* [Перенос кода][Migrate your code]

Дополнительные рекомендации по работе с хранилищем данных SQL см. в статье [Рекомендации по использованию хранилища данных SQL Azure][best practices].

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[data type best practices]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

