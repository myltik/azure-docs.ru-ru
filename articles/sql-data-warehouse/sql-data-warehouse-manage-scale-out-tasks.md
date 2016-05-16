<properties
   pageTitle="Задачи управления масштабируемостью для хранилища данных SQL Azure (портал Azure) | Microsoft Azure"
   description="Задачи портала Azure, относящиеся к масштабированию производительности для хранилища данных SQL Azure. Вы можете изменить вычислительные ресурсы путем настройки единиц DWU. Кроме того, можно приостанавливать и возобновлять работу вычислительных ресурсов для сокращения затрат."
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

# Задачи управления масштабируемостью для хранилища данных SQL Azure (портал Azure)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-overview-scalability.md)
- [Портал](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


Гибкое развертывание вычислительных ресурсов и памяти позволяет удовлетворять меняющиеся потребности рабочих нагрузок и сокращать затраты благодаря свертыванию ресурсов в периоды низкой нагрузки.

Этот набор задач использует портал Azure:

- для масштабирования производительности путем корректировки числа единиц DWU;
- для приостановки работы вычислительных ресурсов;
- для возобновления работы вычислительных ресурсов;

Дополнительные сведения см. в [обзоре масштабируемости производительности][].

<a name="scale-performance-bk"></a>

## Масштабирование производительности

[AZURE.INCLUDE [Описание единиц DWU масштабирования хранилища данных SQL](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Изменение вычислительных ресурсов

1. Откройте [портал Azure][], откройте нужную базу данных и щелкните **Масштаб**.

    ![Щелкните пункт Масштаб][1]

1. В колонке "Масштаб" передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU.

    ![Переместите ползунок][2]

1. Щелкните **Сохранить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Щелкните Сохранить][3]

<a name="pause-compute-bk"></a>

## Приостановка работы вычислительных ресурсов

[AZURE.INCLUDE [Описание приостановки работы хранилища данных SQL](../../includes/sql-data-warehouse-pause-description.md)]

Приостановка работы базы данных

1. Откройте [портал Azure][], а затем нужную базу данных. Обратите внимание, что она находится в состоянии **В сети**. 

    ![Состояние "В сети"][6]

1. Чтобы приостановить работу вычислительных ресурсов и ресурсов памяти, щелкните **Приостановить**. Появится подтверждение. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Подтверждение приостановки][7]

1. Во время остановки базы данных хранилищем данных SQL для нее будет отображаться состояние "Приостановка".
2. Когда состояние изменится на **Приостановлено**, это будет означать, что операция приостановки выполнена и плата за единицы DWU больше не начисляется.

    ![Состояние "Приостановка"][4]

<a name="resume-compute-bk"></a>

## Возобновление работы вычислительных ресурсов

[AZURE.INCLUDE [Описание возобновления работы хранилища данных SQL](../../includes/sql-data-warehouse-resume-description.md)]Возобновление работы базы данных

1. Откройте [портал Azure][], а затем нужную базу данных. Обратите внимание, что она находится в состоянии **Приостановлено**. 

    ![Приостановка базы данных][4]

1. Чтобы возобновить работу базы данных, щелкните **Запустить**. Появится подтверждение. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Подтверждение возобновления][5]

1. Во время запуска базы данных хранилищем данных SQL для нее будет отображаться состояние "Возобновление".
2. Когда состояние изменится на **В сети**, база данных будет готова к работе.

    ![Состояние "В сети"][6]

<a name="next-steps-bk"></a>

## Дальнейшие действия
Дополнительные сведения см. в разделе [Средства управления][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-scale.png
[2]: ./media/sql-data-warehouse-manage-scale-out-tasks/move-slider.png
[3]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-save.png
[4]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-database.png
[5]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-database.png
[7]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-confirm.png

<!--Article references-->
[Средства управления]: ./sql-data-warehouse-overview-manage.md
[обзоре масштабируемости производительности]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->


<!--Other Web references-->

[портал Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->