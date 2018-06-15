---
title: Восстановление хранилища данных SQL Azure (портал Azure) | Документация Майкрософт
description: Задачи портала Azure для восстановления хранилища данных SQL Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6268f500d44bf7ceaaccbcc8e2c2134b42032197
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526878"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Восстановление хранилища данных SQL Azure (портал)
> [!div class="op_single_selector"]
> * [Обзор][Overview]
> * [Портал][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
В этой статье вы узнаете, как восстановить хранилище данных SQL Azure с помощью портала Azure.

## <a name="before-you-begin"></a>Перед началом работы
**Проверьте ресурсы DTU.** Каждый экземпляр хранилища данных SQL размещается на сервере SQL Server (например, myserver.database.windows.net), которому выделена квота единиц пропускной способности данных (DTU) по умолчанию. Перед восстановлением хранилища данных SQL убедитесь, что у сервера SQL Server осталась достаточная квота DTU для восстанавливаемой базы данных. Чтобы узнать, как вычислить необходимую квоту DTU или запросить дополнительные единицы DTU, ознакомьтесь с разделом [Создание запроса в службу поддержки][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Восстановление активной или приостановленной базы данных
Процедура восстановления базы данных

1. Войдите на [портал Azure][Azure portal].
2. В левой области выберите **Обзор**, а затем — **Серверы SQL Server**.

    ![Выбор "Обзор" > "Серверы SQL Server"](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Найдите свой сервер и выберите его.

    ![Выбор своего сервера](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Найдите экземпляр хранилища данных SQL, из которого требуется выполнить восстановление, и выберите его.

    ![Выбор экземпляра хранилища данных SQL для восстановления](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. В верхней части колонки "Хранилище данных" щелкните **Восстановить**.

    ![Выбор "Восстановить"](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Укажите новое значение в поле **Имя базы данных**.
7. Выберите последнюю **точку восстановления**.

   Обязательно выберите последнюю точку восстановления. Так как точки восстановления отображаются в формате UTC, то по умолчанию может быть выбрана не самая последняя точка восстановления.

      ![Выбор точки восстановления](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Нажмите кнопку **ОК**.
9. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **уведомлений**.

> [!NOTE]
> Чтобы настроить базу данных после восстановления, ознакомьтесь с разделом [Настройка базы данных после восстановления][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Восстановление удаленной базы данных.
Восстановление удаленной базы данных.

1. Войдите на [портал Azure][Azure portal].
2. В левой области выберите **Обзор**, а затем — **Серверы SQL Server**.

    ![Выбор "Обзор" > "Серверы SQL Server"](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Найдите свой сервер и выберите его.

    ![Выбор своего сервера](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Прокрутите внизу колонку своего сервера до раздела **Операции**.
5. Щелкните элемент **Удаленные базы данных**.

    ![Выбор элемента "Удаленные базы данных"](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Выберите удаленную базу данных, которую хотите восстановить.

    ![Выбор базы данных для восстановления](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Укажите новое значение в поле **Имя базы данных**.

    ![Добавление имени для базы данных](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Нажмите кнопку **ОК**.
9. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **уведомлений**.

> [!NOTE]
> Чтобы настроить базу данных после восстановления, ознакомьтесь с разделом [Настройка базы данных после восстановления][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о функциях, обеспечивающих непрерывность бизнес-процессов в выпусках Базы данных SQL Azure, см. в статье [Обзор обеспечения непрерывности бизнес-процессов с помощью базы данных SQL Azure][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
