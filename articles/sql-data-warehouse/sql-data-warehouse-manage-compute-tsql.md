<properties
   pageTitle="Управление вычислительными ресурсами в хранилище данных SQL Azure (REST) | Microsoft Azure"
   description="Задачи Transact-SQL (T-SQL) для масштабирования производительности путем изменения числа единиц DWU. Сокращение затрат путем свертывания ресурсов в периоды низкой загрузки."
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
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# Управление вычислительными ресурсами в хранилище данных SQL (T-SQL)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-manage-compute-overview.md)
- [Портал](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Масштабирование производительности путем развертывания вычислительных ресурсов и памяти для удовлетворения меняющихся потребностей рабочих нагрузок. Снижение затрат путем свертывания ресурсов в периоды низкой загрузки или полной приостановки вычислений.

В этом наборе задач T-SQL используется

- Просмотр текущих параметров DWU
- для изменения вычислительных ресурсов путем настройки единиц DWU.

Чтобы приостановить или возобновить базу данных, выберите один из других параметров платформы в верхней части этой статьи.

Дополнительные сведения об этом см. в разделе [Manage compute power in Azure SQL Data Warehouse (Overview)][] \(Обзор управления вычислительной мощностью в хранилище данных SQL Azure).

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

<a name="scale-dwu-bk"></a> <a name="scale-compute-bk"></a>

## Масштабирование вычислительных ресурсов

[AZURE.INCLUDE [Описание единиц DWU масштабирования хранилища данных SQL](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Изменение DWU:


1. Подключитесь к базе данных master, связанной с логическим сервером базы данных SQL.
2. Используйте инструкцию T-SQL [ALTER DATABASE][]. В приведенном ниже примере для базы данных MySQLDW устанавливается цель уровня обслуживания DW1000.

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
[Manage compute power in Azure SQL Data Warehouse (Overview)]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0810_2016-->
