---
title: "Копирование базы данных SQL Azure с помощью портала Azure | Документация Майкрософт"
description: "Создание копии Базы данных SQL Azure"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
ms.service: sql-database
ms.devlang: NA
ms.date: 09/19/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8d70045b3ed20d387fa856614647409deb73c127


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Копирование базы данных SQL Azure с помощью портала Azure
> [!div class="op_single_selector"]
> * [Обзор](sql-database-copy.md)
> * [Портал Azure](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

Ниже показано, как скопировать базу данных SQL с помощью [портала Azure](https://portal.azure.com) на тот же сервер или на другой сервер.

Для копирования базы данных SQL необходимо следующее:

* Подписка Azure. Если вам нужна подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
* База данных SQL для копирования. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).

## <a name="copy-your-sql-database"></a>Копирование Базы данных SQL
Откройте страницу "База данных SQL" для базы данных, которую нужно скопировать.

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Больше служб** > **Базы данных SQL** и выберите необходимую базу данных.
3. На странице базы данных SQL щелкните **Копировать**.
   
   ![База данных SQL](./media/sql-database-copy-portal/sql-database-copy.png)
4. На странице **Копирование** указывается имя базы данных по умолчанию. Введите другое имя, если нужно (у всех баз данных на сервере должны иметь уникальные имена).
5. Выберите **целевой сервер**. Целевой сервер — это место, где будет создана копия базы данных. Можно скопировать базу данных на тот же сервер или на другой сервер. Можно выбрать существующий сервер из списка или создать новый. 
6. После выбора параметра **Целевой сервер** станут доступны параметры **Пул эластичных баз данных** и **Ценовая категория**. Если на сервере организован пул, то базу данных можно скопировать в него.
7. Нажмите кнопку **ОК** , чтобы запустить процесс копирования.
   
   ![База данных SQL](./media/sql-database-copy-portal/copy-page.png)

## <a name="monitor-the-progress-of-the-copy-operation"></a>Отслеживание хода выполнения операции копирования
* После запуска копирования щелкните уведомление портала, чтобы открыть подробные сведения.
  
    ![уведомление][3]
  
    ![monitor][4]

## <a name="verify-the-database-is-live-on-the-server"></a>Проверка активности базы данных на сервере
* Щелкните **Больше служб** > **Базы данных SQL** и убедитесь в том, что новая база данных находится в состоянии **В сети**.

## <a name="resolve-logins"></a>Разрешение имен для входа
Сведения о разрешении имен для входа после завершения операции копирования см. в разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes).

## <a name="next-steps"></a>Дальнейшие действия
* В разделе [Копирование Базы данных SQL Azure](sql-database-copy.md) доступны общие сведения о копировании базы данных SQL Azure.
* В разделе [Копирование базы данных SQL Azure с помощью PowerShell](sql-database-copy-powershell.md) рассматривается копирование базы данных с помощью PowerShell.
* В разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](sql-database-copy-transact-sql.md) рассматривается копирование базы данных с помощью Transact-SQL.
* В разделе [Как управлять безопасностью базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md) описывается управление пользователями и именами для входа при копировании базы данных на другой логический сервер.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Управление именами для входа](sql-database-manage-logins.md)
* [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)
* [Экспорт базы данных в BACPAC](sql-database-export.md)
* [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
* [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png




<!--HONumber=Nov16_HO3-->


