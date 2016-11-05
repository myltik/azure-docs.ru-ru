---
title: Восстановление базы данных SQL Azure до момента времени в прошлом (на портале Azure) | Microsoft Docs
description: Восстановление базы данных SQL Azure до момента времени в прошлом
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/17/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Восстановление базы данных SQL Azure до момента времени в прошлом с помощью портала Azure
> [!div class="op_single_selector"]
> * [Обзор](sql-database-recovery-using-backups.md)
> * [Восстановление до точки во времени: PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

В этой статье объясняется, как восстановить базу данных до предшествующей точки во времени из [созданных автоматически резервных копий базы данных SQL](sql-database-automated-backups.md) с помощью портала Azure.

## Выбор базы данных для восстановления до момента времени в прошлом
Чтобы восстановить базу данных на портале Azure, выполните следующие действия.

1. Откройте [портал Azure](https://portal.azure.com).
2. В левой части экрана выберите **ОБЗОР** > **Базы данных SQL**.
3. Перейдите к базе данных, которую требуется восстановить, и выберите ее.
4. В верхней части колонки базы данных выберите **Восстановить**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. Укажите имя базы данных, выберите момент времени и нажмите кнопку "ОК".
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## Дальнейшие действия
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md).
* Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).
* Чтобы узнать об использовании автоматически создаваемых резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md).
* Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md).
* Чтобы узнать об использовании автоматически создаваемых резервных копий для архивации, ознакомьтесь с [копированием базы данных](sql-database-copy.md).

<!---HONumber=AcomDC_0727_2016-->