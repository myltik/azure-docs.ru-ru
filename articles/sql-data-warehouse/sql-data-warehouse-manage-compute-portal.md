---
title: "Управление вычислительными ресурсами в хранилище данных SQL Azure (портал Azure) | Документация Майкрософт"
description: "Задачи портала Azure для управления вычислительными ресурсами. Масштабирование вычислительных ресурсов путем изменения числа единиц DWU. Кроме того, можно приостанавливать и возобновлять работу вычислительных ресурсов для сокращения затрат."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 63888d5dd103b585cf18e4787d3e779810163e3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
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


## <a name="scale-compute-power"></a>Масштабирование вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Изменение вычислительных ресурсов

1. Откройте [портал Azure][Azure portal], откройте нужную базу данных и щелкните **Масштаб**.

    ![Щелкните пункт Масштаб][1]
2. В колонке "Масштаб" передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU.

    ![Переместите ползунок][2]
3. Щелкните **Сохранить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Щелкните Сохранить][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Приостановка работы вычислительных ресурсов
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Приостановка работы базы данных

1. Откройте [портал Azure][Azure portal], а затем нужную базу данных. Обратите внимание, что она находится в состоянии **В сети**.

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

1. Откройте [портал Azure][Azure portal], а затем нужную базу данных. Обратите внимание, что она находится в состоянии **Приостановлено**.

    ![Приостановка базы данных][4]
2. Чтобы возобновить работу базы данных, щелкните **Запустить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Подтверждение возобновления][5]
3. Во время запуска базы данных хранилищем данных SQL для нее будет отображаться состояние "Возобновление".
4. Когда состояние изменится на **В сети**, база данных будет готова к работе.

    ![Состояние "В сети"][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в [обзоре средств управления][Management overview].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
