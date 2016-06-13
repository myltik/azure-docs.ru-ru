<properties
   pageTitle="Переименование в хранилище данных SQL | Microsoft Azure"
   description="Советы по переименованию таблиц в хранилище данных SQL Azure для разработки решений."
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
   ms.date="05/28/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Переименование в хранилище данных SQL
Хранилище данных SQL использует инструкцию [RENAME][] для переименования таблиц. Это отличается от SQL Server, в котором используется `sp_rename`. В настоящее время переименовывать можно только таблицы пользователя. Невозможно переименовать базы данных и внешние таблицы.

## Переименование таблицы

При переименовании таблицы все связанные с ней объекты и свойства обновляются в соответствии с новым именем. Обновляются, например, таблица определений, индексы, ограничения и разрешения. Представления не обновляются.

Для переименования таблицы используется следующий синтаксис:

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

## Изменение схемы таблицы

Чтобы изменить схему, к которой относится объект, используйте ALTER SCHEMA.

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Для переименования требуется монопольная блокировка таблицы

Нельзя переименовать таблицу, пока она используется. Для переименования таблицы требуется ее монопольная блокировка. Если таблица используется, сеанс с использованием этой таблицы необходимо завершить. Для завершения сеанса используйте команду [KILL][]. Пример: `KILL 'SID1234'`. Соблюдайте осторожность при использовании команды KILL, так как после завершения сеанса будет выполнен откат результатов всех незафиксированных транзакций. Ознакомьтесь со статьей о подключениях, чтобы больше узнать о [сеансах и запросах][]. Дополнительные сведения о влиянии команды KILL на транзакционные запросы и результатах отката см. в разделе [Оптимизация транзакций для хранилища данных SQL][].


## Дальнейшие действия
Дополнительные сведения о T-SQL представлены в [справочниках по T-SQL][].

<!--Image references-->

<!--Article references-->
[development overview]: ./sql-data-warehouse-overview-develop.md
[сеансах и запросах]: ./sql-data-warehouse-develop-connections.md#sessions-and-requests
[справочниках по T-SQL]: ./sql-data-warehouse-reference-tsql-statements.md
[Оптимизация транзакций для хранилища данных SQL]: ./sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!---HONumber=AcomDC_0601_2016-->