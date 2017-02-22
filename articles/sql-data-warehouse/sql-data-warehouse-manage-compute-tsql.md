---
title: "Приостановка, возобновление и масштабирование ресурсов в хранилище данных SQL Azure с помощью T-SQL | Документация Майкрософт"
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
ms.sourcegitcommit: f589111d3a4da061e1cc3313632dd0b5403dc278
ms.openlocfilehash: f93e5802141b16862f5e37126196069bd32c1f19


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
2. Используйте оператор TSQL [ALTER DATABASE][ALTER DATABASE]. В приведенном ниже примере для базы данных MySQLDW устанавливается цель уровня обслуживания DW1000. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других задачах управления см. в статье [Управление базами данных в хранилище данных SQL Azure][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/



<!--HONumber=Jan17_HO4-->


