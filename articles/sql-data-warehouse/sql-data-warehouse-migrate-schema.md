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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Перенос схемы в хранилище данных SQL#

Следующие сводные данные помогут вам понять различия между SQL Server и хранилищем данных SQL и перенести базу данных.

## Перенос таблиц

При переносе таблиц следует ознакомиться с возможностями таблиц хранилища данных SQL. Вы можете начать с [обзора таблиц][]. В этой статье рассмотрены наиболее важные вопросы, касающиеся создания таблицы, например статистика таблиц, распределения, секционирование и индексирование. В ней также рассматриваются некоторые [неподдерживаемые функции таблиц][] и способы их обойти.

Хранилище данных SQL поддерживает распространенные типы бизнес-данных. Список поддерживаемых и [неподдерживаемых типов данных][] см. в разделе [Типы данных][]. Статья [Типы данных][] также содержит запрос для выявления [неподдерживаемых типов данных][]. При преобразовании типов данных обязательно ознакомьтесь с [рекомендациями по типам данных][].

## Дальнейшие действия
После успешного переноса схемы базы данных в хранилище данных SQL вы можете перейти к одной из приведенных ниже статей.

- [Перенос данных][]
- [Перенос кода][]

Дополнительные рекомендации по работе с хранилищем данных SQL см. в статье [Рекомендации по использованию хранилища данных SQL Azure][].

<!--Image references-->

<!--Article references-->
[Перенос кода]: ./sql-data-warehouse-migrate-code.md
[Перенос данных]: ./sql-data-warehouse-migrate-data.md
[Рекомендации по использованию хранилища данных SQL Azure]: ./sql-data-warehouse-best-practices.md
[обзора таблиц]: ./sql-data-warehouse-tables-overview.md
[неподдерживаемые функции таблиц]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Типы данных]: ./sql-data-warehouse-tables-data-types.md
[неподдерживаемых типов данных]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[рекомендациями по типам данных]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0831_2016-->