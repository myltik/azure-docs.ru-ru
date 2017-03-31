---
title: "Копирование базы данных SQL Azure с помощью портала Azure | Документация Майкрософт"
description: "Создание копии Базы данных SQL Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 752dcb97101f4a8288125e3e41167095620d130f
ms.lasthandoff: 03/28/2017


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Копирование базы данных SQL Azure с помощью портала Azure

Ниже показано, как скопировать базу данных SQL с помощью [портала Azure](https://portal.azure.com) на тот же сервер или на другой сервер. 

> [!NOTE]
> Кроме того, это можно сделать с помощью [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) или [Transact-SQL](sql-database-copy-transact-sql.md).
>

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
6. После выбора параметра **Целевой сервер** станут доступны параметры **Пул эластичных БД** и **Ценовая категория**. Если на сервере организован пул, то базу данных можно скопировать в него.
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
* Дополнительные сведения об управлении пользователями и именами для входа при копировании базы данных на другой логический сервер см. в статье [Настройка безопасности Базы данных SQL Azure и управление ею для геовосстановления или отработки отказа](sql-database-geo-replication-security-config.md).
* Рекомендации по экспорту базы данных в BACPAC-файл с помощью портала Azure см. в [этой статье](sql-database-export-portal.md).
* [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
* [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->

[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png


