---
title: "Восстановление базы данных из резервной копии, расположенной в хранилище служб восстановления Azure | Документация Майкрософт"
description: "Краткий справочник по восстановлению базы данных из резервной копии, расположенной в хранилище служб восстановления Azure, и информация о пространстве, используемом этими резервными копиями."
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
ms.openlocfilehash: cdb898ef86517b6eb6eed4b2ae38c4e327e78181


---
# <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Восстановление базы данных из резервной копии с долгосрочным хранением

В этом практическом руководстве вы узнаете, как восстановить базу данных из резервной копии с долгосрочным хранением.

## <a name="restore-from-long-term-backup-retention-using-the-azure-portal"></a>Восстановление после долгосрочного хранения резервных копий с помощью портала Azure

1. В колонке **Azure vault backups** (Резервные копии хранилища Azure) щелкните резервную копию, которую нужно восстановить, а затем нажмите кнопку **Выбрать**.

    ![Выбор резервной копии в хранилище](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. В текстовом поле **Имя базы данных** введите имя для восстановленной базы данных.

    ![Имя новой базы данных](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Нажмите кнопку **ОК**, чтобы восстановить базу данных из резервной копии, расположенной в хранилище, в новую базу данных.

4. На панели инструментов щелкните значок уведомления, чтобы просмотреть состояние задания восстановления.

    ![Ход выполнения задания восстановления из хранилища](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. После завершения задания откройте колонку **Базы данных SQL**, чтобы просмотреть восстановленную базу данных.

    ![Имя базы данных, восстановленной из хранилища](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!TIP]
> Руководство приведено в статье [Начало работы с резервным копированием и восстановлением для защиты и восстановления данных с помощью PowerShell](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы настроить долгосрочное хранение создаваемых автоматически резервных копий в хранилище служб восстановления Azure, ознакомьтесь со статьей [Настройка долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure](sql-database-configure-long-term-retention.md).
- Просмотр резервных копий в хранилище служб восстановления Azure описывается в разделе [Просмотр информации о резервных копиях базы данных при долгосрочном хранении резервных копий](sql-database-view-backups-in-vault.md).
- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до&10; лет](sql-database-long-term-retention.md).
- Дополнительные сведения о восстановлении из резервных копий см. в статье [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


