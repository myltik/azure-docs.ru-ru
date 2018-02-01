---
title: "Управление вычислительными ресурсами в хранилище данных SQL Azure (общие сведения) | Документация Майкрософт"
description: "Возможности масштабирования производительности в хранилище данных SQL Azure. Вы можете увеличивать масштаб, изменяя объем DWU, а также приостанавливать и возобновлять работу вычислительных ресурсов для сокращения затрат."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 3/23/2017
ms.author: elbutter
ms.openlocfilehash: d795abe5254d47a72a468b0989e46829a5c5142a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2018
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Управление вычислительными ресурсами в хранилище данных SQL Azure (обзор)
> [!div class="op_single_selector"]
> * [Обзор](sql-data-warehouse-manage-compute-overview.md)
> * [Портал](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

Архитектура хранилища данных SQL разделяет хранилище и вычислительные ресурсы, что позволяет масштабировать их независимо друг от друга. В результате можно масштабировать вычислительные ресурсы в соответствии с требованиями к производительности независимо от объема данных. При такой архитектуре [выставление счетов][billed] за ресурсы вычисления и хранения осуществляется отдельно. 

В этом обзоре рассматриваются возможности масштабирования для хранилища данных SQL и сведения о том, как использовать возможности приостановления, возобновления и масштабирования хранилища данных SQL. 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>Как работают операции управления вычислениями в хранилище данных SQL
Архитектура хранилища данных SQL состоит из управляющего узла, вычислительных узлов и уровня хранилища, разделенного между 60 распределениями. 

Во время обычного активного сеанса в хранилище данных SQL головной узел системы управляет метаданными и содержит оптимизатор распределенных запросов. Вычислительные узлы и уровень хранилища размещены под головным узлом. При 400 DWU система включает в себя один головной узел, четыре вычислительных узла и уровень хранилища, состоящий из 60 распределений. 

Если выполняется операция масштабирования или приостановки, система сначала уничтожает все входящие запросы, а затем выполняет откат транзакций, чтобы обеспечить стабильное состояние. Операции масштабирования можно выполнить только после завершения отката транзакций. При операции увеличения масштаба система подготавливает нужное количество вычислительных узлов, после чего присоединяет их к уровню хранилища. При операции уменьшения масштаба ненужные узлы освобождаются, а оставшиеся вычислительные узлы заново присоединяются к нужному количеству распределений. При операции приостановки все вычислительные узлы освобождаются, а система выполняет различные операции метаданных, чтобы итоговая система находилась в стабильном состоянии.

| DWU  | \# вычислительных узлов | \# распределений на узел |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4.                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4.                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

Ниже перечислены три основные функции управления вычислениями.

1. Приостановить
2. Продолжить
3. Масштаб

Выполнение каждой из этих операций может занять несколько минут. Если операции масштабирования, приостановки и возобновления выполняются автоматически, можно реализовать логику, позволяющую приступать к выполнению другого действия только после завершения определенных операций. 

Проверка состояния базы данных с использованием различных конечных точек позволяет правильно реализовать автоматизацию этих операций. На портале отображается уведомление о завершении операции и текущее состояние баз данных, однако здесь нельзя проверять состояние программным способом. 

>  [!NOTE]
>
>  Функцию управления вычислениями можно выполнять не для всех конечных точек.
>
>  

|              | Приостановка и возобновление | Масштаб | Проверка состояния базы данных |
| ------------ | ------------ | ----- | -------------------- |
| Портал Azure | Yes          | Yes   | **Нет**               |
| PowerShell   | Yes          | Yes   | Yes                  |
| ИНТЕРФЕЙС REST API     | Yes          | Yes   | Yes                  |
| T-SQL        | **Нет**       | Yes   | Yes                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Масштабирование вычислительных ресурсов

Производительность в хранилище данных SQL измеряется в единицах использования хранилища данных (DWU), которые являются абстрактной мерой вычислительных ресурсов, таких как ЦП, память и пропускная способность ввода-вывода. Вы можете масштабировать производительность системы с помощью различных средств, таких как портал, T-SQL и REST API. 

### <a name="how-do-i-scale-compute"></a>Как масштабировать вычислительные ресурсы
Масштабирование вычислительных ресурсов в хранилище данных SQL осуществляется путем изменения параметра DWU. При добавлении дополнительных DWU для некоторых операций производительность будет повышаться линейно.  Мы разработали предложения DWU, благодаря которым ваша производительность заметно изменится при уменьшении или увеличении масштаба системы. 

Для настройки DWU можно использовать любые из следующих отдельных методов:

* [Масштабирование вычислительных ресурсов с помощью портала Azure][Scale compute power with Azure portal]
* [Масштабирование вычислительных ресурсов с помощью PowerShell][Scale compute power with PowerShell]
* [Масштабирование вычислительных ресурсов с помощью REST API][Scale compute power with REST APIs]
* [Масштабирование вычислительных ресурсов с помощью TSQL][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>Сколько DWU нужно использовать

Чтобы определить оптимальное значение DWU, попробуйте увеличить и уменьшить масштаб, а также выполнить несколько запросов после загрузки данных. Так как масштабирование выполняется достаточно быстро, попробуйте использовать разные уровни производительности не дольше одного часа. 

> [!Note] 
> Хранилище данных SQL предназначено для обработки больших объемов данных. Чтобы определить реальные возможности масштабирования (для больших значений DWU), вы можете использовать большой набор данных, размер которого достигает или превышает 1 ТБ.

Рекомендации по выборе оптимального объема DWU для рабочей нагрузки:

1. Если хранилище данных только создается, начните с небольшого уровня производительности, обеспечиваемого DWU.  В качестве отправной точки можно использовать DW400 или DW200.
2. Отслеживайте производительность приложения, сравнивая ее с количеством выбранных DWU.
3. Определите, насколько выше или ниже должна быть производительность, которая оптимально соответствует требуемому вам уровню (учитывайте линейное масштабирование).
4. Увеличьте или уменьшите число DWU в соответствии с тем, насколько вы хотите увеличить или уменьшить скорость выполнения вашей рабочей нагрузки. 
5. Вносите изменения, пока не достигнете уровня производительности, который оптимально отвечает вашим бизнес-требованиям.

> [!NOTE]
>
> Высокий уровень параллелизма только увеличивает производительность обработки запросов, если нагрузку можно разделить между вычислительными узлами. Если вы обнаружили, что масштабирование не влияет на производительность, ознакомьтесь со статьями о настройке производительности, чтобы проверить, распределены ли данные неравномерно или не перемещаете ли вы большой объем данных. 

### <a name="when-should-i-scale-dwus"></a>Когда следует масштабировать DWU
Масштабирование единиц DWU изменяет следующие важные сценарии:

1. Линейное изменение производительности системы для операций сканирования, агрегирования и инструкций CTAS.
2. Увеличение количества модулей чтения и записи при загрузке с помощью PolyBase.
3. Максимальное количество параллельных запросов и слотов выдачи.

Рекомендации по выборе времени для масштабирования DWU

1. Прежде чем выполнять ресурсоемкую загрузку данных или их преобразование, увеличьте объем DWU, чтобы быстрее получать доступ к данным.
2. Во время рабочих часов наибольшей нагрузки выполните масштабирование, чтобы обработать большое количество одновременных запросов. 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Приостановка работы вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Для приостановки базы данных можно использовать любые из следующих отдельных методов:

* [Приостановка работы вычислительных ресурсов с помощью портала Azure][Pause compute with Azure portal]
* [Приостановка работы вычислительных ресурсов с помощью PowerShell][Pause compute with PowerShell]
* [Приостановка работы вычислительных ресурсов с помощью REST API][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Возобновление работы вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Для возобновления базы данных можно использовать любые из следующих отдельных методов:

* [Возобновление работы вычислительных ресурсов с помощью портала Azure][Resume compute with Azure portal]
* [Возобновление работы вычислительных ресурсов с помощью PowerShell][Resume compute with PowerShell]
* [Возобновление работы вычислительных ресурсов с помощью REST API][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>Проверка состояния базы данных 

Для возобновления базы данных можно использовать любые из следующих отдельных методов:

- [проверка состояния базы данных с помощью T-SQL;][Check database state with T-SQL]
- [проверка состояния базы данных с помощью PowerShell;][Check database state with PowerShell]
- [проверка состояния базы данных с помощью REST API.][Check database state with REST APIs]

## <a name="permissions"></a>Разрешения

Для масштабирования базы данных потребуются разрешения, описанные в статье [Изменение базы данных (хранилище данных Azure SQL)][ALTER DATABASE].  Чтобы приостановить и возобновить работу, нужны разрешения [Участник баз данных SQL][SQL DB Contributor], в частности Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Дополнительная информация
Приведенные ниже статьи помогут вам разобраться с некоторыми дополнительными ключевыми понятиями, связанными с производительностью:

* [Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL][Workload and concurrency management]
* [Общие сведения о таблицах в хранилище данных SQL][Table design overview]
* [Распределение таблиц в хранилище данных SQL][Table distribution]
* [Indexing tables in SQL Data Warehouse (Индексирование таблиц в хранилище данных SQL)][Table indexing]
* [Секционирование таблиц в хранилище данных SQL][Table partitioning]
* [Управление статистикой таблиц в хранилище данных SQL][Table statistics]
* [Рекомендации по использованию хранилища данных SQL Azure][Best practices]

<!--Image reference-->

<!--Article references-->
[billed]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
