---
title: "Управление вычислительными ресурсами в хранилище данных SQL Azure (общие сведения) | Документация Майкрософт"
description: "Возможности масштабирования производительности в хранилище данных SQL Azure. Вы можете увеличивать масштаб, изменяя объем DWU, а также приостанавливать и возобновлять работу вычислительных ресурсов для сокращения затрат."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 6871ab3bc25ab3ec7b3c60852aa06bee047d8e9a


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

Архитектура хранилища данных SQL разделяет хранилище и вычислительные ресурсы, что позволяет масштабировать их независимо друг от друга. Это позволяет масштабировать производительность и в то же время сокращать расходы, оплачивая производительность только тогда, когда она вам нужна.

В этом обзоре описываются указанные ниже возможности масштабирования производительности для хранилища данных SQL и приводятся рекомендации о том, как и когда их следует использовать:

* Масштабирование вычислительных ресурсов путем изменения числа [единиц использования хранилища данных (DWU)][data warehouse units (DWUs)]
* Приостановка или возобновление работы вычислительных ресурсов

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>Масштабирование производительности
В хранилище данных SQL можно быстро увеличить или уменьшить производительность, настроив объем вычислительных ресурсов ЦП, памяти и пропускной способности ввода-вывода. Чтобы повысить или снизить производительность, достаточно настроить число [единиц использования хранилища данных (DWU)][data warehouse units (DWUs)], выделяемых хранилищем данных SQL для базы данных. Хранилище данных SQL быстро вносит изменения и применяет все базовые изменения в оборудовании или программном обеспечении.

Прошло то время, когда для обеспечения высокой производительности хранилища данных необходимо было анализировать, какие типы процессоров, какой объем памяти и какой тип хранилища вам нужен. Разместив хранилище данных в облаке, вы больше не будете сталкиваться с проблемами, вызванными использованием оборудования, которое не является мощным. Вместо этого хранилище данных SQL задает вам вопрос о том, с какой скоростью необходимо анализировать данные.

### <a name="how-do-i-scale-performance"></a>Как масштабировать производительность
Чтобы добиться гибкого увеличения или уменьшения вычислительной мощности, просто измените число [единиц использования хранилища данных (DWU)][data warehouse units (DWUs)] для своей базы данных. При добавлении дополнительных DWU производительность будет повышаться линейно.  На более высоких уровнях DWU для заметного повышения производительности требуется добавление более чем 100 DWU. Чтобы помочь вам выбрать значимые шаги увеличения DWU, предлагаем уровни DWU, которые дадут вам наилучший результат.

Для настройки DWU можно использовать любые из следующих отдельных методов:

* [Масштабирование вычислительных ресурсов с помощью портала Azure][Scale compute power with Azure portal]
* [Масштабирование вычислительных ресурсов с помощью PowerShell][Scale compute power with PowerShell]
* [Масштабирование вычислительных ресурсов с помощью REST API][Scale compute power with REST APIs]
* [Масштабирование вычислительных ресурсов с помощью TSQL][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>Сколько DWU нужно использовать
Производительность в хранилище данных SQL масштабируется линейно, и переход с одного уровня производительности к другому (например, с 100 DWU к 2000) происходит в считанные секунды. Это позволяет вам гибко экспериментировать с различными параметрами DWU, пока вы не подберете наиболее оптимальный сценарий.

Чтобы определить оптимальное значение DWU, попробуйте увеличить и уменьшить масштаб, а также выполнить несколько запросов после загрузки данных. Так как масштабирование выполняется достаточно быстро, попробуйте использовать разные уровни производительности не дольше одного часа. Помните о том, что хранилище данных SQL предназначено для обработки больших объемов данных. Чтобы определить реальные возможности масштабирования (для больших объемов данных, как мы и предлагаем), вы можете использовать большой набор данных, размер которого достигает или превышает 1 ТБ.

Рекомендации по выборе оптимального объема DWU для рабочей нагрузки:

1. Если хранилище данных только создается, начните с небольшого количества DWU.  В качестве отправной точки можно использовать DW400 или DW200.
2. Отслеживайте производительность приложения, сравнивая ее с количеством выбранных DWU.
3. Определите, насколько выше или ниже должна быть производительность, которая оптимально соответствует требуемому вам уровню (учитывайте линейное масштабирование).
4. Увеличьте или уменьшите число DWU в соответствии с тем, насколько вы хотите увеличить или уменьшить скорость выполнения вашей рабочей нагрузки. Служба отреагирует незамедлительно и изменит количество вычислительных ресурсов в соответствии с новыми требованиями к объему DWU.
5. Вносите изменения, пока не достигнете уровня производительности, который оптимально отвечает вашим бизнес-требованиям.

### <a name="when-should-i-scale-dwus"></a>Когда следует масштабировать DWU
Если вы хотите получить результаты быстрее, увеличьте количество DWU за отдельную оплату.  Если вам не нужна большая вычислительная мощность, уменьшите количество DWU и оплачивайте только необходимые ресурсы.

Рекомендации по выборе времени для масштабирования DWU

1. Если рабочая нагрузка вашего приложения постоянно меняется, вы можете увеличивать и уменьшать объем DWU в соответствии с пиковыми и самыми низкими нагрузками. Например, если пиковый уровень нагрузки приходится на конец месяца, вы можете запланировать добавление DWU в пиковые дни и уменьшение производительности в конце этого периода.
2. Прежде чем выполнять ресурсоемкую загрузку данных или их преобразование, увеличьте объем DWU, чтобы быстрее получать доступ к данным.

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

## <a name="permissions"></a>Разрешения
Для масштабирования базы данных потребуются разрешения, описанные в статье о синтаксисе [ALTER DATABASE][ALTER DATABASE].  Чтобы приостановить и возобновить работу, нужны разрешения [Участник баз данных SQL][SQL DB Contributor], в частности Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Дальнейшие действия
Приведенные ниже статьи помогут вам разобраться с некоторыми дополнительными ключевыми понятиями, связанными с производительностью:

* [Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL][Workload and concurrency managment]
* [Общие сведения о таблицах в хранилище данных SQL][Table design overview]
* [Распределение таблиц в хранилище данных SQL][Table distribution]
* [Indexing tables in SQL Data Warehouse (Индексирование таблиц в хранилище данных SQL)][Table indexing]
* [Секционирование таблиц в хранилище данных SQL][Table partitioning]
* [Управление статистикой таблиц в хранилище данных SQL][Table statistics]
* [Рекомендации по использованию хранилища данных SQL Azure][Best practices]

<!--Image reference-->

<!--Article references-->
[data warehouse units (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Workload and concurrency managment]: ./sql-data-warehouse-develop-concurrency.md
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



<!--HONumber=Dec16_HO1-->


