---
title: "Создание и администрирование отдельных баз данных Azure SQL с помощью портала Azure | Документация Майкрософт"
description: "Краткий справочник по созданию и администрированию отдельных баз данных SQL Azure с помощью портала Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 319e22e2fb423e0fe1fabf95d0018d4fa3232e0b
ms.openlocfilehash: 5d7f11b9b027f86adf3d2382e82f2d382d71ee51


---
# <a name="create-and-manage-single-azure-sql-databases-with-the-azure-portal"></a>Создание и администрирование отдельных баз данных Azure SQL с помощью портала Azure

Вы можете создавать и администрировать отдельные базы данных SQL Azure с помощью [портала Azure](https://portal.azure.com/), PowerShell, Transact-SQL, REST API или языка C#. Этот раздел посвящен использованию портала Azure. PowerShell: см. дополнительные сведения о [создании и управлении отдельных баз данных с помощью PowerShell](sql-database-manage-single-databases-powershell.md). Transact-SQL: см. дополнительные сведения о [создании и администрировании отдельных баз данных с помощью Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-a-single-azure-sql-database-with-the-azure-portal"></a>Создание отдельных баз данных Azure SQL с помощью портала Azure

1. На [портале Azure](https://portal.azure.com/) откройте колонку **Базы данных SQL**. 

    ![базы данных sql](./media/sql-database-get-started/sql-databases.png)
2. В колонке баз данных SQL щелкните **Добавить**.

    ![Добавление базы данных SQL](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Портал Azure: см. дополнительные сведения о [создании баз данных с помощью портала Azure](sql-database-get-started.md).
>    

## <a name="view-and-update-sql-database-settings-using-the-azure-portal"></a>Просмотр и обновление параметров базы данных SQL с помощью портала Azure

1. На [портале Azure](https://portal.azure.com/) откройте вкладку **Базы данных SQL**. 

    ![базы данных sql](./media/sql-database-get-started/sql-databases.png)

2. Щелкните базу данных, с которой собираетесь работать, а затем выберите нужный параметр в колонке базы данных SQL.

    ![Колонка нового примера базы данных](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Изменение уровня служб и уровня производительности отдельной базы данных
Откройте колонку «База данных SQL» для базы данных, которую необходимо масштабировать.

1. На [портале Azure](https://portal.azure.com) щелкните **Больше служб** > **Базы данных SQL**.
2. Щелкните базу данных, которую хотите изменить.
3. В колонке **База данных SQL** щелкните **Ценовая категория (единицы DTU масштабирования)**.
   
   ![Ценовая категория](./media/sql-database-manage-single-database-portal/new-tier.png)

4. Выберите новый уровень обслуживания и нажмите кнопку **Выбрать**.
   
   После нажатия кнопки **Выбрать** будет отправлен запрос на изменение ценовой категории. В зависимости от размера базы данных операция может занять некоторое время (см. сведения в начале этой статьи).
   
   > [!NOTE]
   > Изменение ценовой категории базы данных не приводит к изменению максимального размера баз данных. Чтобы изменить максимальный размер базы данных, используйте [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) или [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
   > 
   > 
   
   ![выберите ценовую категорию](./media/sql-database-manage-single-database-portal/choose-tier.png)
5. Справа вверху щелкните значок уведомления (колокольчик).
   
   ![Уведомления](./media/sql-database-manage-single-database-portal/scale-notification.png)
   
6. Щелкните текст уведомления, чтобы открыть область сведений, где можно просмотреть состояние запроса.

## <a name="next-steps"></a>Дальнейшие действия
* Обзор средств управления см. в статье [Обзор: средства управления для Базы данных SQL](sql-database-manage-overview.md).
* Чтобы узнать, как выполнять задачи управления с использованием портала Azure, ознакомьтесь со статьей [Управление базами данных SQL Azure с помощью портала Azure](sql-database-manage-portal.md).
* Чтобы узнать, как выполнять задачи управления с использованием PowerShell, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью PowerShell](sql-database-manage-powershell.md).
* Чтобы узнать, как выполнять задачи управления с использованием SQL Server Management Studio, ознакомьтесь со статьей [Управление базой данных SQL Azure с помощью SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Сведения о службе Базы данных SQL см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md). 
* Сведения о серверах и функциях базы данных Azure см. в статье [Возможности базы данных SQL Azure](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


