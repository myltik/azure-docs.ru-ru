---
title: "Восстановление базы данных SQL Azure до предыдущей точки во времени (портал Azure) | Документация Майкрософт"
description: "Восстановление базы данных SQL Azure до точки во времени."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: d1822905-a11f-4c42-8940-98c6b81aed20
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feaea525ae1fceff5acdc95fefa115939d5b1da


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Восстановление базы данных SQL Azure до предшествующей точки во времени с помощью портала Azure
> [!div class="op_single_selector"]
> * [Обзор](sql-database-recovery-using-backups.md)
> * [Восстановление до точки во времени: PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

В этой статье объясняется, как восстановить базу данных до предшествующей точки во времени из [автоматически создаваемых резервных копий базы данных SQL](sql-database-automated-backups.md) с помощью портала Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Восстановление состояния базы данных SQL на момент времени в прошлом
Выберите базу данных для восстановления на портале Azure.

1. Откройте [портал Azure](https://portal.azure.com).
2. В левой части экрана выберите **Больше служб** > **Базы данных SQL**.
3. Щелкните по базе данных, которую хотите восстановить.
4. В верхней части страницы базы данных выберите **Восстановить**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. На странице **Восстановление** выберите дату и время (в формате UTC) для восстановления базы данных. Нажмите кнопку **ОК**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Мониторинг операции восстановления
1. Нажав кнопку **ОК** на предыдущем шаге, щелкните значок уведомления в правом верхнем углу страницы. Щелкните уведомление **Идет восстановление базы данных SQL**, чтобы получить дополнительные сведения.
   
    ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
2. Откроется страница "Идет восстановление базы данных SQL", содержащая сведения о состоянии восстановления. Для получения более подробных сведений можно щелкнуть интересующую вас строку.
   
    ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>Дальнейшие действия
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md)
* Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md)
* Чтобы узнать об использовании автоматически создаваемых резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md)
* Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md)  
* Чтобы узнать об использовании автоматически создаваемых резервных копий для архивации, ознакомьтесь с [копированием базы данных](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


