---
title: "Портал Azure: восстановление базы данных SQL Azure | Документация Майкрософт"
description: "Восстановление базы данных SQL Azure (портал Azure)."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 49800be1a61e45f64a77f8d2662ca9edeae29de9
ms.lasthandoff: 03/10/2017


---
# <a name="restore-an-azure-sql-database-using-the-azure-portal"></a>Восстановление базы данных SQL Azure с помощью портала Azure

Ниже показано, как восстановить базу данных SQL Azure до точки во времени, если она была удалена, и как это сделать с помощью геоизбыточной резервной копии.

## <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Восстановление базы данных SQL Azure до точки во времени 

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


## <a name="restore-a-deleted-azure-sql-database-from-backups"></a>Восстановление удаленной базы данных SQL Azure из резервных копий
Чтобы восстановить удаленную базу данных на портале Azure, сделайте следующее:

1. На [портале Azure](https://portal.azure.com) щелкните **Больше служб** > **Серверы SQL Server**.
2. Выберите сервер с базой данных, которую требуется восстановить.
3. Прокрутите вниз до раздела **Операции** колонки сервера и выберите **Удаленные базы данных**. ![Восстановление базы данных SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. Выберите базу данных, которую требуется восстановить.
5. Укажите имя базы данных и нажмите кнопку **ОК**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>Восстановление базы данных SQL Azure из геоизбыточной резервной копии

Чтобы выполнить геовосстановление базы данных на портале Azure, выполните следующие действия.

1. Перейдите на [портал Azure](https://portal.azure.com).
2. В левой части экрана выберите **+Создать** > **Базы данных** > **База данных SQL**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Выберите **Резервная копия** в качестве источника, затем выберите резервную копию, из которой вы хотите восстановить базу данных. Укажите имя базы данных, сервер, на котором вы хотите восстановить базу данных, и нажмите кнопку **Создать**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Чтобы отслеживать состояние операции восстановления, можно щелкнуть значок уведомления в правом верхнем углу страницы.

## <a name="next-steps"></a>Дальнейшие действия
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md)
* Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md)
* Чтобы узнать об использовании автоматически создаваемых резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md)
* Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md)  
* Чтобы узнать об использовании автоматически создаваемых резервных копий для архивации, ознакомьтесь с [копированием базы данных](sql-database-copy.md)


