---
title: "Просмотр резервных копий в хранилище служб восстановления Azure | Документация Майкрософт"
description: "Краткий справочник по просмотру резервных копий, расположенных в хранилище служб восстановления Azure, и информация о пространстве, используемом этими резервными копиями."
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
ms.openlocfilehash: 723be587128c131670c9905572d808c176362fb8


---
# <a name="view-information-about-your-database-backups-in-long-term-backup-retention"></a>Просмотр информации о резервных копиях базы данных при долгосрочном хранении резервных копий

Изучив это практическое руководство, вы узнаете, как получить сведения о резервных копиях базы данных с долгосрочным хранением.

## <a name="view-long-term-backup-retention-information-using-the-azure-portal"></a>Просмотр сведений о долгосрочном хранении резервных копий с помощью портала Azure 

1. Откройте колонку хранилища службы восстановления Azure (щелкните **Все ресурсы** и выберите хранилище службы восстановления Azure из списка ресурсов для своей подписки), чтобы просмотреть сведения об объеме хранилища, используемого для хранения резервных копий базы данных.

   ![Просмотр хранилища служб восстановления с резервными копиями](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Откройте колонку **База данных SQL** для своей базы данных.

    ![Колонка нового примера базы данных](./media/sql-database-get-started/new-sample-db-blade.png)

3. На панели инструментов щелкните **Восстановить**.

    ![Элемент "Восстановить" на панели инструментов](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. В колонке "Восстановление" щелкните **Долгосрочные**.

5. В разделе Azure vault backups (Резервные копии хранилища Azure) щелкните **Выберите архив**, чтобы просмотреть список доступных резервных копий с долгосрочным хранением.

    ![Резервные копии в хранилище](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

> [!TIP]
> Руководство приведено в статье [Начало работы с резервным копированием и восстановлением для защиты и восстановления данных с помощью PowerShell](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы настроить долгосрочное хранение создаваемых автоматически резервных копий в хранилище служб восстановления Azure, ознакомьтесь с разделом [](sql-database-configure-long-term-retention.md).
- Чтобы восстановить базу данных из резервной копии с долгосрочным хранением, ознакомьтесь с разделом [Восстановление базы данных из резервной копии с долгосрочным хранением](sql-database-restore-from-long-term-retention.md).
- Чтобы узнать больше о резервных копиях базы данных, создаваемых автоматически службой, ознакомьтесь с разделом [Подробнее о резервном копировании базы данных SQL](sql-database-automated-backups.md).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до&10; лет](sql-database-long-term-retention.md).
- Дополнительные сведения о восстановлении из резервных копий см. в статье [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


