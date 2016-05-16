<properties
pageTitle="Задачи управления масштабируемостью для хранилища данных SQL Azure (TSQL) | Microsoft Azure"
   description="Задачи TSQL, относящиеся к масштабированию производительности для хранилища данных SQL Azure. Используйте TSQL для изменения вычислительных ресурсов путем настройки единиц DWU."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Задачи управления масштабируемостью для хранилища данных SQL Azure (TSQL)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-overview-scalability.md)
- [Портал](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


Гибкое развертывание вычислительных ресурсов и памяти позволяет удовлетворять меняющиеся потребности рабочих нагрузок и сокращать затраты благодаря свертыванию ресурсов в периоды низкой нагрузки.

Этот набор задач использует TSQL:

- для просмотра текущих параметров DWU;
- для изменения вычислительных ресурсов путем настройки единиц DWU.

Чтобы приостановить или возобновить базу данных, выберите один из других параметров платформы в верхней части этой статьи.

Дополнительные сведения см. в [обзоре масштабируемости производительности][].

<a name="current-dwu-bk"></a>

## Просмотр текущих параметров DWU

Для просмотра текущих параметров DWU для своих баз данных:

1. Откройте обозреватель объектов SQL Server в Visual Studio 2015.
2. Подключитесь к базе данных master, связанной с логическим сервером базы данных SQL.
2. Выберите в sys.database\_service\_objectives динамическое административное представление. Пример: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>

## DWU масштабирования

[AZURE.INCLUDE [Описание единиц DWU масштабирования хранилища данных SQL](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Изменение DWU:


1. Подключитесь к базе данных master, связанной с логическим сервером базы данных SQL.
2. Используйте оператор TSQL [ALTER DATABASE][]. В приведенном ниже примере для базы данных MySQLDW устанавливается цель уровня обслуживания DW1000. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## Дальнейшие действия

Сведения о других задачах управления см. в статье [Средства управления][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Средства управления]: ./sql-data-warehouse-overview-manage.md
[обзоре масштабируемости производительности]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->