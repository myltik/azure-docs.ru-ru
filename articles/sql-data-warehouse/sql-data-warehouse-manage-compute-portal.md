---
title: "Управление вычислительными ресурсами в хранилище данных SQL Azure (портал Azure) | Документация Майкрософт"
description: "Задачи портала Azure для управления вычислительными ресурсами. Масштабирование вычислительных ресурсов путем изменения числа единиц DWU. Кроме того, можно приостанавливать и возобновлять работу вычислительных ресурсов для сокращения затрат."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: c1c23ab46d9b4e43154a62080cb8865b246489f9
ms.openlocfilehash: 2e11486203f04d671548b6132e61f804acb7f90a


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Управление вычислительными ресурсами в хранилище данных SQL Azure (портал Azure)
> [!div class="op_single_selector"]
> * [Обзор](sql-data-warehouse-manage-compute-overview.md)
> * [Портал](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

Масштабирование производительности путем развертывания вычислительных ресурсов и памяти для удовлетворения меняющихся потребностей рабочих нагрузок. Снижение затрат путем свертывания ресурсов в периоды низкой загрузки или полной приостановки вычислений.

Этот набор задач использует портал Azure:

* Масштабирование вычислительных ресурсов
* Приостановка работы вычислительных ресурсов
* Возобновление работы вычислительных ресурсов

Дополнительные сведения см. в статье [управлением вычислительной мощностью][управлением вычислительной мощностью].

## <a name="scale-compute-power"></a>Масштабирование вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Изменение вычислительных ресурсов

1. Откройте [портал Azure][портал Azure], откройте нужную базу данных и щелкните **Масштаб**.

    ![Щелкните пункт Масштаб][1]
2. В колонке "Масштаб" передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU.

    ![Переместите ползунок][2]
3. Щелкните **Сохранить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Щелкните Сохранить][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Приостановка работы вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Приостановка работы базы данных

1. Откройте [портал Azure][портал Azure] и выберите нужную базу данных. Обратите внимание, что она находится в состоянии **В сети**.

    ![Состояние "В сети"][6]
2. Чтобы приостановить работу вычислительных ресурсов и ресурсов памяти, щелкните **Приостановить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Подтверждение приостановки][7]
3. Во время запуска базы данных хранилищем данных SQL для нее будет отображаться состояние **Приостановка**.
4. Когда состояние изменится на **Приостановлено**, это будет означать, что операция приостановки выполнена и плата за DWU больше не начисляется.

    ![Состояние "Приостановка"][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Возобновление работы вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Возобновление работы базы данных

1. Откройте [портал Azure][портал Azure] и выберите нужную базу данных. Обратите внимание, что она находится в состоянии **Приостановлено**.

    ![Приостановка базы данных][4]
2. Чтобы возобновить работу базы данных, щелкните **Запустить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Подтверждение возобновления][5]
3. Во время запуска базы данных хранилищем данных SQL для нее будет отображаться состояние "Возобновление".
4. Когда состояние изменится на **В сети**, база данных будет готова к работе.

    ![Состояние "В сети"][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в статье [Управление базами данных в хранилище данных SQL Azure][Управление базами данных в хранилище данных SQL Azure].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Управление базами данных в хранилище данных SQL Azure]: ./sql-data-warehouse-overview-manage.md
[управлением вычислительной мощностью]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[портал Azure]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


