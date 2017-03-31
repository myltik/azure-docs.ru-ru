---
title: "Портал Azure: управление долгосрочным хранением резервных копий баз данных | Документация Майкрософт"
description: "Краткий справочник по настройке и администрированию долгосрочного хранения создаваемых автоматически резервных копий баз данных SQL Azure в хранилище служб восстановления Azure и восстановлению посредством этих резервных копий с помощью портала Azure."
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
ms.date: 12/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: fa4cf56c10f885a2fd4b62e08a369a51d1280ab3
ms.lasthandoff: 03/25/2017


---
# <a name="configure-manage-and-restore-from-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-the-azure-portal"></a>Настройка и администрирование долгосрочного хранения резервных копий баз данных в хранилище служб восстановления Azure и восстановление посредством этих резервных копий с помощью портала Azure

В этом разделе вы узнаете о настройке и администрировании долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure и восстановлении посредством этих резервных копий с помощью портала Azure. Эту задачу можно также выполнить с помощью [PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).

Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](sql-database-long-term-retention.md).

> [!TIP]
> Руководство приведено в статье [Начало работы с резервным копированием и восстановлением для защиты и восстановления данных](sql-database-get-started-backup-recovery-portal.md).
>

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
> Руководство приведено в статье [Начало работы с резервным копированием и восстановлением для защиты и восстановления данных с помощью PowerShell](sql-database-get-started-backup-recovery-portal.md).

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об управлении резервными копиями при долгосрочном хранении с помощью PowerShell см. в разделе [Настройка долгосрочного хранения создаваемых автоматически резервных копий баз данных в хранилище служб восстановления Azure с помощью PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).
- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](sql-database-automated-backups.md).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](sql-database-long-term-retention.md).
- Дополнительные сведения о восстановлении из резервных копий см. в статье [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md).
