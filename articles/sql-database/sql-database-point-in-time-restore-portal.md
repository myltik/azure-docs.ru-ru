---
title: "Портал Azure: восстановление базы данных SQL Azure до точки во времени | Документация Майкрософт"
description: "Восстановление базы данных SQL Azure до предшествующей точки во времени с помощью портала Azure."
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 342df453a2e4cc573117fd59fd5c3aa899295439
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Восстановление базы данных SQL Azure до предшествующей точки во времени с помощью портала Azure

В этой статье объясняется, как восстановить базу данных до предшествующей точки во времени из [автоматически созданных резервных копий базы данных SQL](sql-database-automated-backups.md) с использованием PowerShell. Эту задачу можно также выполнить [с помощью портала Azure](sql-database-point-in-time-restore-powershell.md).

## <a name="restore-to-a-previous-point-in-time"></a>Восстановление до предшествующей точки во времени 

> [!TIP]
> Руководство приведено в статье [Начало работы с резервным копированием и восстановлением для защиты и восстановления данных](sql-database-get-started-backup-recovery-portal.md).
>

Выберите базу данных для восстановления на портале Azure.

1. Откройте [портал Azure](https://portal.azure.com).
2. В левой части экрана выберите **Больше служб** > **Базы данных SQL**.
3. Щелкните по базе данных, которую хотите восстановить.
4. В верхней части страницы базы данных выберите **Восстановить**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. На странице **Восстановление** выберите дату и время (в формате UTC) для восстановления базы данных. Нажмите кнопку **ОК**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. Нажав кнопку **ОК** на предыдущем шаге, щелкните значок уведомления в правом верхнем углу страницы. Щелкните уведомление **Идет восстановление базы данных SQL**, чтобы получить дополнительные сведения.
   
    ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. Откроется страница "Идет восстановление базы данных SQL", содержащая сведения о состоянии восстановления. Для получения более подробных сведений можно щелкнуть интересующую вас строку.
   
    ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [создаваемых автоматически резервных копий базы данных SQL](sql-database-automated-backups.md)
* Чтобы узнать об использовании создаваемых автоматически резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md)
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md)


