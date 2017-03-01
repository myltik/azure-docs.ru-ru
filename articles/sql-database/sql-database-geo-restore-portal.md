---
title: "Портал Azure: восстановление базы данных SQL из геоизбыточной резервной копии | Документация Майкрософт"
description: "Восстановление базы данных SQL Azure на новый сервер из геоизбыточной резервной копии с помощью портала Azure."
services: sql-database
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
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 7d9314444c39cda3f9d893e4f97b4afce4c75777
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-the-azure-portal"></a>Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure

В этой статье показано, как восстановить базу данных на новый сервер с помощью геовосстановления и портала Azure. Эту задачу можно также выполнить [с помощью PowerShell](sql-database-geo-restore-powershell.md).

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure

Чтобы выполнить геовосстановление базы данных на портале Azure, выполните следующие действия.

1. Перейдите на [портал Azure](https://portal.azure.com).
2. В левой части экрана выберите **+Создать** > **Базы данных** > **База данных SQL**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Выберите **Резервная копия** в качестве источника, затем выберите резервную копию, из которой вы хотите восстановить базу данных. Укажите имя базы данных, сервер, на котором вы хотите восстановить базу данных, и нажмите кнопку **Создать**.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Чтобы отслеживать состояние операции восстановления, можно щелкнуть значок уведомления в правом верхнем углу страницы.


## <a name="next-steps"></a>Дальнейшие действия
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md).
* Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь со статьей [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).
* Чтобы узнать об использовании автоматически создаваемых резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md).
* Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md).  
* Чтобы узнать об использовании автоматически создаваемых резервных копий для архивации, ознакомьтесь с [копированием базы данных](sql-database-copy.md).


