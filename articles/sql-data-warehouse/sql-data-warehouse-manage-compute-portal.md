<properties
   pageTitle="Управление вычислительными ресурсами в хранилище данных SQL Azure (портал Azure) | Microsoft Azure"
   description="Задачи портала Azure для управления вычислительными ресурсами. Масштабирование вычислительных ресурсов путем изменения числа единиц DWU. Кроме того, можно приостанавливать и возобновлять работу вычислительных ресурсов для сокращения затрат."
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
   ms.date="08/22/2016"
   ms.author="barbkess;sonyama"/>

# Управление вычислительными ресурсами в хранилище данных SQL Azure (портал Azure)

> [AZURE.SELECTOR]
- [Обзор](sql-data-warehouse-manage-compute-overview.md)
- [Портал](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Масштабирование производительности путем развертывания вычислительных ресурсов и памяти для удовлетворения меняющихся потребностей рабочих нагрузок. Снижение затрат путем свертывания ресурсов в периоды низкой загрузки или полной приостановки вычислений.

Этот набор задач использует портал Azure:

- Масштабирование вычислительных ресурсов
- Приостановка работы вычислительных ресурсов
- Возобновление работы вычислительных ресурсов

Чтобы узнать больше, ознакомьтесь с [управлением вычислительной мощностью][].

<a name="scale-performance-bk"></a> <a name="scale-compute-bk"></a>

## Масштабирование вычислительных ресурсов

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

1. Чтобы приостановить работу вычислительных ресурсов и ресурсов памяти, щелкните **Приостановить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Подтверждение приостановки][7]

1. Во время запуска базы данных хранилищем данных SQL для нее будет отображаться состояние **Приостановка**.
2. Когда состояние изменится на **Приостановлено**, это будет означать, что операция приостановки выполнена и плата за DWU больше не начисляется.

    ![Состояние "Приостановка"][4]

<a name="resume-compute-bk"></a>

## Возобновление работы вычислительных ресурсов

[AZURE.INCLUDE [Описание возобновления работы хранилища данных SQL](../../includes/sql-data-warehouse-resume-description.md)] Возобновление работы базы данных

1. Откройте [портал Azure][], а затем нужную базу данных. Обратите внимание, что она находится в состоянии **Приостановлено**.

    ![Приостановка базы данных][4]

1. Чтобы возобновить работу базы данных, щелкните **Запустить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Подтверждение возобновления][5]

1. Во время запуска базы данных хранилищем данных SQL для нее будет отображаться состояние "Возобновление".
2. Когда состояние изменится на **В сети**, база данных будет готова к работе.

    ![Состояние "В сети"][6]

<a name="next-steps-bk"></a>

## Дальнейшие действия
Дополнительные сведения см. в [обзоре средств управления][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

.<!--Article references-->
[обзоре средств управления]: ./sql-data-warehouse-overview-manage.md
[управлением вычислительной мощностью]: ./sql-data-warehouse-manage-compute-overview.md

.<!--MSDN references-->


<!--Other Web references-->

[портал Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0824_2016-->