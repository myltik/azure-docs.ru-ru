---
title: "Настройка долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure | Документация Майкрософт"
description: "Краткий справочник по настройке долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure."
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
ms.openlocfilehash: b395fc5c1deaf04205dcfccacd13d30ec90dd1e7


---
# <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Настройка долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure 

В этом практическом руководстве вы узнаете, как настроить долгосрочное хранение создаваемых автоматически резервных копий в хранилище служб восстановления Azure.

## <a name="configure-long-term-retention-using-the-azure-portal"></a>Настройка долгосрочного хранения с помощью портала Azure

1. Откройте колонку **SQL Server** для своего сервера.

    ![Колонка сервера SQL Server](./media/sql-database-get-started/sql-server-blade.png)

2. Щелкните **Long-term backup retention** (Долгосрочное хранение резервных копий).

   ![Ссылка Long-term backup retention (Долгосрочное хранение резервных копий)](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. В колонке **Long-term backup retention** (Долгосрочное хранение резервных копий) просмотрите и примите условия использования предварительной версии (если вы этого еще не сделали или эта функция по-прежнему находится на этапе предварительной версии).

   ![Принятие условий использования предварительной версии](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Чтобы настроить долгосрочное хранение резервных копий для базы данных, выберите эту базу данных в таблице и щелкните **Настроить** на панели инструментов.

   ![Выбор базы данных для настройки долгосрочного хранения резервных копий](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. В колонке **Настройка** в разделе **Хранилище служб восстановления** щелкните **Настройка обязательных параметров**.

   ![Ссылка для настройки хранилища](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. В колонке **Хранилище служб восстановления** выберите имеющееся хранилище (при наличии). Если хранилище служб восстановления отсутствует, щелкните, чтобы выйти, и создайте хранилище служб восстановления.

   ![Ссылка для создания хранилища](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. В колонке **Хранилища служб восстановления** щелкните **Добавить**.

   ![Ссылка для добавления хранилища](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. В колонке **Хранилище служб восстановления** предоставьте допустимое имя для нового хранилища служб восстановления.

   ![Имя нового хранилища](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Выберите подписку и группу ресурсов, а затем выберите расположение хранилища. По завершении нажмите кнопку **Создать**.

   ![Создание хранилища](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Хранилище должно находиться в том же регионе, что и логический сервер Azure SQL Server, и использовать ту же группу ресурсов.
   >

10. После создания хранилища вернитесь в колонку **Хранилище служб восстановления**.

11. В колонке **Хранилище служб восстановления** щелкните хранилище, а затем нажмите кнопку **Выбрать**.

   ![Выбор имеющегося хранилища](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. В колонке **Настройка** предоставьте допустимое имя для новой политики хранения, измените ее должным образом и нажмите кнопку **ОК**.

   ![Определение политики хранения](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. В колонке **Long-term backup retention** (Долгосрочное хранение резервных копий) щелкните **Сохранить**, а затем нажмите кнопку **ОК**, чтобы применить политику долгосрочного хранения резервных копий ко всем выбранным базам данных.

   ![Определение политики хранения](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Щелкните **Сохранить**, чтобы включить новую политику долгосрочного хранения резервных копий в настроенном хранилище служб восстановления Azure.

   ![Определение политики хранения](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

14. После включения долгосрочного хранения резервных копий откройте колонку **Хранилище служб восстановления** (щелкните **Все ресурсы** и выберите хранилище служб восстановления из списка ресурсов для своей подписки).

   ![Просмотр хранилища служб восстановления](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)


> [!TIP]
> Руководство приведено в статье [Начало работы с резервным копированием и восстановлением для защиты и восстановления данных с помощью PowerShell](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Дальнейшие действия

- Восстановление базы данных из резервной копии при долгосрочном хранении описывается в разделе [Восстановление базы данных из резервной копии с долгосрочным хранением](sql-database-restore-from-long-term-retention.md).
- Просмотр резервных копий в хранилище служб восстановления Azure описывается в разделе [Просмотр информации о резервных копиях базы данных при долгосрочном хранении резервных копий](sql-database-view-backups-in-vault.md).
- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до&10; лет](sql-database-long-term-retention.md).
- Дополнительные сведения о восстановлении из резервных копий см. в статье [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


