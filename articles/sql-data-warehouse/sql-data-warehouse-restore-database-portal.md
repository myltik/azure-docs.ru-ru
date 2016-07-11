<properties
   pageTitle="Восстановление хранилища данных SQL Azure (портал) | Microsoft Azure"
   description="Задачи портала Azure для восстановления хранилища данных SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess;nicw"/>

# Восстановление хранилища данных SQL Azure (портал)

> [AZURE.SELECTOR]
- [Обзор][]
- [Портал][]
- [PowerShell][]
- [REST][]

Из этой статьи вы узнаете, как восстановить хранилище данных SQL Azure с помощью портала Azure.

## Перед началом работы

**Проверьте ресурсы DTU.** Каждое хранилище данных SQL размещается на логическом сервере SQL Server. Этот логический сервер имеет предельную емкость, измеряемую в DTU. Перед восстановлением хранилища данных SQL важно убедиться, что логический сервер SQL Server, на котором размещена база данных, имеет достаточное количество DTU для восстанавливаемой базы данных. В этой записи блога вы можете узнать больше о том, [как просмотреть и увеличить квоту DTU][].


## Восстановление активной или приостановленной базы данных

Процедура восстановления базы данных

1. Войдите на [портал Azure][].
2. В левой части экрана выберите **Обзор**, а затем — **Серверы SQL**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Перейдите к нужному серверу и выберите его.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Найдите хранилище данных SQL, из которого требуется выполнить восстановление, и выберите его.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. В верхней части колонки "Хранилище данных" щелкните **Восстановить**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Укажите новое значение в поле **Имя базы данных**.
7. Выберите последнюю **точку восстановления**.
    1. Обязательно выберите последнюю точку восстановления. Так как точки восстановления отображаются в формате UTC, иногда по умолчанию отображается не последняя точка восстановления.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Нажмите кнопку **ОК**.
9. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **уведомлений**.

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной Базы данных SQL Azure][].


## Восстановление удаленной базы данных.

Восстановление удаленной базы данных.

1. Войдите на [портал Azure][].
2. В левой части экрана выберите **Обзор**, а затем — **Серверы SQL**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Перейдите к нужному серверу и выберите его.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Прокрутите внизу колонку своего сервера до раздела "Операции".
5. Щелкните элемент **Удаленные базы данных**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Выберите удаленную базу данных, которую хотите восстановить.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Укажите новое значение в поле **Имя базы данных**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Нажмите кнопку **ОК**.
9. Начнется процесс восстановления базы данных, который можно отслеживать с помощью **уведомлений**.

>[AZURE.NOTE] Восстановленную базу данных можно настроить. Для этого следуйте инструкциям руководства [Финализация восстановленной Базы данных SQL Azure][].


## Дальнейшие действия
Дополнительные сведения о функциях обеспечения непрерывности бизнес-процессов в выпусках Базы данных SQL Azure см. в статье [Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL][].

<!--Image references-->

<!--Article references-->
[Обзор. Непрерывность облачных бизнес-процессов и аварийное восстановление баз данных с базой данных SQL]: ./sql-database-business-continuity.md
[Обзор]: ./sql-data-warehouse-restore-database-overview.md
[Портал]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Финализация восстановленной Базы данных SQL Azure]: ./sql-database-recovered-finalize.md

<!--MSDN references-->

<!--Blog references-->
[как просмотреть и увеличить квоту DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[портал Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0629_2016-->