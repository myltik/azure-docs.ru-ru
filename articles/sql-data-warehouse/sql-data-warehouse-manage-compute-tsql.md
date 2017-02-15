---
title: "Управление вычислительными ресурсами в хранилище данных SQL Azure (REST) | Документация Майкрософт"
description: "Задачи Transact-SQL (T-SQL) для масштабирования производительности путем изменения числа единиц DWU. Сокращение затрат путем свертывания ресурсов в периоды низкой загрузки."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 41ab1c4b2709c2ea6890ca526db1dea177b7da1b


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Управление вычислительными ресурсами в хранилище данных SQL (T-SQL)
> [!div class="op_single_selector"]
> * [Обзор](sql-data-warehouse-manage-compute-overview.md)
> * [Портал](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

Масштабирование производительности путем развертывания вычислительных ресурсов и памяти для удовлетворения меняющихся потребностей рабочих нагрузок. Снижение затрат путем свертывания ресурсов в периоды низкой загрузки или полной приостановки вычислений. 

В этом наборе задач T-SQL используется

* Просмотр текущих параметров DWU
* для изменения вычислительных ресурсов путем настройки единиц DWU.

Чтобы приостановить или возобновить базу данных, выберите один из других параметров платформы в верхней части этой статьи.

Дополнительные сведения см. в статье [Управление вычислительными ресурсами в хранилище данных SQL Azure (обзор)][Управление вычислительными ресурсами в хранилище данных SQL Azure (обзор)].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>для просмотра текущих параметров DWU;
Для просмотра текущих параметров DWU для своих баз данных:

1. Откройте обозреватель объектов SQL Server в Visual Studio 2015.
2. Подключитесь к базе данных master, связанной с логическим сервером базы данных SQL.
3. Выберите в sys.database_service_objectives динамическое административное представление. Пример: 

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
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Масштабирование вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Изменение DWU:

1. Подключитесь к базе данных master, связанной с логическим сервером базы данных SQL.
2. Используйте инструкцию TSQL [ALTER DATABASE][ALTER DATABASE]. В приведенном ниже примере для базы данных MySQLDW устанавливается цель уровня обслуживания DW1000. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других задачах управления см. в статье [Управление базами данных в хранилище данных SQL Azure][Управление базами данных в хранилище данных SQL Azure].

<!--Image references-->

<!--Article references-->
[Ограничения емкости службы]: ./sql-data-warehouse-service-capacity-limits.md
[Управление базами данных в хранилище данных SQL Azure]: ./sql-data-warehouse-overview-manage.md
[Управление вычислительными ресурсами в хранилище данных SQL Azure (обзор)]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Портал Azure]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


