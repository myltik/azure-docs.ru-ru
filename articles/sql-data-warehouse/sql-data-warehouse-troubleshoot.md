<properties
   pageTitle="Устранение неполадок | Microsoft Azure"
   description="Устранение неполадок хранилища данных SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="twounder"/>

# Устранение неполадок
В этой статье описываются некоторые наиболее распространенные проблемы, с которыми пользователи сталкиваются при работе хранилищем данных SQL Azure.

## Соединение
Подключение к хранилищу данных SQL Azure может завершиться неудачей по двум распространенным причинам:

- не заданы правила брандмауэра;
- используются неподдерживаемые инструменты и протоколы.

### Правила брандмауэра
Базы данных SQL Azure брандмауэрами на уровне сервера и базы данных. Это гарантирует, что доступ к базам данных возможен только с известных IP-адресов. Брандмауэры являются безопасными по умолчанию. Это означает, что перед подключением необходимо разрешить доступ для вашего IP-адреса.

Чтобы настроить брандмауэр для предоставления доступа, выполните указания в разделе [Настройка доступа брандмауэра сервера к IP-адресу клиента](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip) на странице [Подготовка](sql-data-warehouse-get-started-provision.md).

### Использование неподдерживаемых инструментов и протоколов
Хранилище данных SQL поддерживает [Visual Studio 2013 и 2015](sql-data-warehouse-get-started-connect.md) как среды разработки и [SQL Server Native Client 10 и 11 (ODBC)](https://msdn.microsoft.com/library/ms131415.aspx) для клиентских подключений.

Чтобы узнать больше, ознакомьтесь с нашими страницами [Подключение](sql-data-warehouse-get-started-connect.md).

## Производительность запросов
Хранилище данных SQL использует распространенные конструкции SQL Server для выполнения запросов, включая статистику. [Статистика](sql-data-warehouse-develop-statistics.md) — это объекты, содержащие информацию о диапазоне и частоте значений в одном столбце базы данных. Обработчик запросов использует эту статистику для оптимизации выполнения запросов и повышения их производительности. Можно использовать следующий запрос, чтобы определить время последнего обновления статистики.

```
SELECT
	sm.[name]								    AS [schema_name],
	tb.[name]								    AS [table_name],
	co.[name]									AS [stats_column_name],
	st.[name]									AS [stats_name],
	STATS_DATE(st.[object_id],st.[stats_id])	AS [stats_last_updated_date]
FROM
	sys.objects				AS ob
	JOIN sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
	JOIN sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND	st.[object_id]		= sc.[object_id]
	JOIN sys.columns		AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
	JOIN sys.types           AS ty	ON	co.[user_type_id]	= ty.[user_type_id]
	JOIN sys.tables          AS tb	ON	co.[object_id]		= tb.[object_id]
	JOIN sys.schemas         AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE
	1=1 
	AND st.[user_created] = 1;
```

Чтобы узнать больше, ознакомьтесь с нашей страницей [Статистика](sql-data-warehouse-develop-statistics.md).

## Ключевые концепции производительности

Приведенные ниже статьи помогут вам понять такие дополнительные ключевые концепции производительности и масштабируемости:

- [производительность и масштабируемость;][]
- [модель параллелизма;][]
- [создание таблиц;][]
- [выбор ключа распределения хэша для таблицы;][]
- [статистика для повышения производительности.][]

## Дальнейшие действия
Чтобы ознакомиться с рекомендациями по созданию собственного решения на основе хранилища данных SQL, обратитесь к статье, содержащей [общие сведения о разработке][].

<!--Image references-->

<!--Article references-->

[производительность и масштабируемость;]: sql-data-warehouse-performance-scale.md
[модель параллелизма;]: sql-data-warehouse-develop-concurrency.md
[создание таблиц;]: sql-data-warehouse-develop-table-design.md
[выбор ключа распределения хэша для таблицы;]: sql-data-warehouse-develop-hash-distribution-key
[статистика для повышения производительности.]: sql-data-warehouse-develop-statistics.md
[общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0107_2016-->