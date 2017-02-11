---
title: "Просмотр самой старой точки восстановления из резервных копий базы данных SQL Azure, создаваемых службой | Документация Майкрософт"
description: "Краткий справочник по просмотру самой старой точки восстановления из резервных копий базы данных, создаваемых автоматически службой."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: ae35dd15c386bbbf4e77a143d245efcd1f44f238


---
# <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-an-azure-sql-database"></a>Просмотр самой старой точки восстановления из резервных копий базы данных SQL Azure, создаваемых службой

В этом практическом руководстве вы узнаете, как просмотреть самую старую точку восстановления из резервных копий базы данных SQL Azure, создаваемых службой.

## <a name="view-the-oldest-restore-point-using-the-azure-portal"></a>Просмотр самой старой точки восстановления с помощью портала Azure

1. Откройте колонку **База данных SQL** для своей базы данных.

    ![Колонка нового примера базы данных](./media/sql-database-get-started/new-sample-db-blade.png)

2. На панели инструментов щелкните **Восстановить**.

    ![Элемент "Восстановить" на панели инструментов](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. В колонке "Восстановление" просмотрите самую старую точку восстановления.

    ![Самая старая точка восстановления](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

> [!TIP]
> Руководство приведено в статье [Начало работы с резервным копированием и восстановлением для защиты и восстановления данных с помощью PowerShell](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до&10; лет](sql-database-long-term-retention.md).
- Дополнительные сведения о восстановлении из резервных копий см. в статье [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


