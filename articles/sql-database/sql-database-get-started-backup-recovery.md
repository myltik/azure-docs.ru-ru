---
title: "Начало работы с резервным копированием и восстановлением баз данных SQL Azure для защиты и восстановления данных | Документация Майкрософт"
description: "Из этого руководства вы узнаете, как восстанавливать базы данных до точки во времени с помощью создаваемых автоматически резервных копий, сохранять создаваемые автоматически резервные копии в хранилище служб восстановления Azure, а также как восстанавливать из хранилища служб восстановления Azure."
keywords: "руководство по базе данных sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: business continuity
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 7f26cd0f6c5f9c7a2fe692bfcdc6ef60d1b2200f
ms.openlocfilehash: d4ea089ed4b5d29c261b25e95f4d304611f9a857


---
<!------------------
This topic is annotated with TEMPLATE guidelines for TUTORIAL TOPICS.


Metadata guidelines

title
    60 characters or less. Tells users clearly what they will do (deploy an ASP.NET web app to App Service). Not the same as H1. It's 60 characters or fewer including all characters between the quotes and the Microsoft Docs site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "This tutorial shows how to deploy an ASP.NET web application to a web app in Azure App Service by using Visual Studio 2015."
------------------>

<!----------------

TEMPLATE GUIDELINES for tutorial topics

The tutorial topic shows users how to solve a problem using a product or service. It includes the prerequisites and steps users need to be successful.  

It is a "solve a problem" topic, not a "learn concepts" topic.

DO include this:
    • What users will do
    • What they will create or accomplish by the end of the tutorial
    • Time estimate
    • Optional but useful: Include a diagram or video. Diagrams help users see the big picture of what they are doing. A video of the steps can be used by customers as an alternative to following the steps in the topic.
    • Prerequisites: Technical expertise and software requirements
    • End-to-end steps. At the end, include next steps to deeper or related tutorials so users can learn more about the service

DON'T include this:
    • Conceptual info about the service. This info is in overview topics that you can link to in the prerequisites section if necessary

------------------->

<!------------------
GUIDELINES for the H1 
    
    The H1 should answer the question "What will I do in this topic?" Write the H1 heading in conversational language and use search keywords as much as possible. Since this is a "solve a problem" topic, make sure the title indicates that. Use a strong, specific verb like "Deploy."  
        
    Heading must use an industry standard term. If your feature is a proprietary name like "elastic pools", use a synonym. For example: "Learn about elastic pools for multi-tenant databases." In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

-------------------->

# <a name="get-started-with-backup-and-restore-for-data-protection-and-recovery"></a>Начало работы с резервным копированием и восстановлением для защиты и восстановления данных

<!------------------
    GUIDELINES for introduction
    
    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top keywords that you are using throughout the article.The introduction should be brief and to the point of what users will do and what they will accomplish. 

    In this example:
     

Sentence #1 Explains what the user will do. This is also the metadata description. 
    This tutorial shows how to deploy an ASP.NET web application to a web app in Azure App Service by using Visual Studio 2015. 

Sentence #2 Explains what users will learn and the benefit.  
    When you’re finished, you’ll have a simple web application up and running in the cloud.

-------------------->


В этом руководстве мы с помощью портала Azure сделаем следующее:

- просмотрим имеющиеся резервные копии базы данных;
- восстановим базу данных до предыдущей точки во времени;
- настроим долгосрочное хранение файла резервной копии базы данных в хранилище служб восстановления Azure;
- восстановим базу данных из хранилища служб восстановления Azure.

**Оценка времени.** Для работы с этим руководством требуется около 30 минут (при условии, что предварительные требования уже выполнены).


## <a name="prerequisites"></a>Предварительные требования

* Вам понадобится учетная запись Azure. Вы можете [создать бесплатную учетную запись Azure](/pricing/free-trial/?WT.mc_id=A261C142F) или [активировать преимущества для подписчиков Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* У вас должна быть возможность подключиться к порталу Azure с помощью учетной записи, которой назначена роль владельца или участника подписки. Дополнительные сведения об управлении доступом на основе ролей (RBAC) см. в статье [Начало работы с управлением доступом на портале Azure](../active-directory/role-based-access-control-what-is.md).

* Вы изучили руководство [по началу работы с серверами баз данных SQL Azure, базами данных и правилами брандмауэра с использованием портала Azure, SQL Server Management Studio](sql-database-get-started.md) или [PowerShell](sql-database-get-started-powershell.md). Если это не так, прежде чем продолжить, изучите предыдущее руководство или выполните сценарий PowerShell, приведенный в конце руководства по началу работы с использованием [PowerShell](sql-database-get-started-powershell.md).


> [!TIP]
> Во время работы с руководством по началу работы эти же задачи можно выполнять с помощью [PowerShell](sql-database-get-started-backup-recovery-powershell.md).


## <a name="sign-in-by-using-your-existing-account"></a>Вход с помощью существующей учетной записи
Используя [существующую подписку](https://account.windowsazure.com/Home/Index), выполните следующие действия, чтобы подключиться к порталу Azure.

1. Откройте любой браузер и подключитесь к [порталу Azure](https://portal.azure.com/).
2. Выполните вход на [портал Azure](https://portal.azure.com/).
3. На странице **входа** введите учетные данные своей подписки.
   
   ![Вход](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>Просмотр самой старой точки восстановления из резервных копий базы данных, создаваемых автоматически службой

При работе с этим разделом руководства вы просмотрите самую старую точку восстановления из [резервных копий базы данных, создаваемых автоматически службой](sql-database-automated-backups.md). 

1. Откройте колонку **База данных SQL** для базы данных **sqldbtutorialdb**.

    ![Колонка нового примера базы данных](./media/sql-database-get-started/new-sample-db-blade.png)

2. На панели инструментов щелкните **Восстановить**.

    ![Элемент "Восстановить" на панели инструментов](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. В колонке "Восстановление" просмотрите самую старую точку восстановления.

    ![Самая старая точка восстановления](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Восстановление базы данных до предыдущей точки во времени

При работе с этим разделом руководства вы восстановите имеющуюся базу данных в новую базу данных до определенной точки во времени.

1. В колонке **Восстановление** базы данных просмотрите имя по умолчанию новой базы данных, в которую вы хотите восстановить свою базу данных до более ранней точки во времени (это имя имеющейся базы данных с меткой времени). Это имя изменяется с учетом времени, который вы будете указывать на следующих этапах.

    ![Имя восстановленной базы данных](./media/sql-database-get-started-backup-recovery/restored-database-name.png)

2. Щелкните значок **календаря** в поле ввода **Точка восстановления (в формате UTС)**.

    ![Точка восстановления](./media/sql-database-get-started-backup-recovery/restore-point.png)

2. В календаре выберите дату с учетом срока хранения.

    ![Выбор даты в поле "Точка восстановления (в формате UTС)"](./media/sql-database-get-started-backup-recovery/restore-point-date.png)

3. В поле ввода **Точка восстановления (в формате UTС)** укажите время восстановления данных из создаваемых автоматически резервных копий базы данных для выбранной даты.

    ![Выбор времени в поле "Точка восстановления (в формате UTС)"](./media/sql-database-get-started-backup-recovery/restore-point-time.png)

    >[!NOTE]
    >Обратите внимание, что имя базы данных изменилось в соответствии с выбранной датой и временем. Обратите также внимание, что вы не можете изменить сервер, на который выполняется восстановление до определенной точки во времени. Чтобы выполнить восстановление на другой сервер, используйте [геовосстановление](sql-database-disaster-recovery.md#recover-using-geo-restore). Наконец, обратите внимание, что базу данных можно восстановить в [эластичный пул](sql-database-elastic-jobs-overview.md) или до другой ценовой категории. 
    >

4. Щелкните **ОК**, чтобы восстановить базу данных в новую базу данных до более ранней точки во времени.

5. На панели инструментов щелкните значок уведомления, чтобы просмотреть состояние задания восстановления.

    ![Ход выполнения задания восстановления](./media/sql-database-get-started-backup-recovery/restore-job-progress.png)

6. После завершения задания откройте колонку **Базы данных SQL**, чтобы просмотреть восстановленную базу данных.

    ![Восстановленная база данных](./media/sql-database-get-started-backup-recovery/restored-database.png)

   > [!NOTE]
   > Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например [извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных, или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных](sql-database-recovery-using-backups.md#point-in-time-restore).
   >

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Настройка долгосрочного хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure 

При работе с этим разделом руководства вы [настроите более длительный срок хранения создаваемых автоматически резервных копий в хранилище служб восстановления Azure](sql-database-long-term-retention.md), чем для вашего уровня служб. 


> [!TIP]
> Сведения об удалении резервных копий с длительным периодом удержания см. в [этой статье](sql-database-long-term-retention-delete.md).


1. Откройте колонку **SQL Server** для сервера **sqldbtutorialserver**.

    ![Колонка сервера SQL Server](./media/sql-database-get-started/sql-server-blade.png)

2. Щелкните **Long-term backup retention** (Долгосрочное хранение резервных копий).

   ![Ссылка Long-term backup retention (Долгосрочное хранение резервных копий)](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. В колонке **sqldbtutorial - Long-term backup retention** (sqldbtutorial — долгосрочное хранение резервных копий) просмотрите и примите условия использования предварительной версии (если вы этого еще не сделали и эта функция по-прежнему находится на этапе предварительной версии).

   ![Принятие условий использования предварительной версии](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Чтобы настроить долгосрочное хранение резервных копий для базы данных sqldbtutorialdb, выберите эту базу данных в таблице и щелкните **Настроить** на панели инструментов.

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

13. В колонке **sqldbtutorial - Long-term backup retention** (sqldbtutorial — долгосрочное хранение резервных копий) щелкните **Сохранить**, а затем нажмите кнопку **ОК**, чтобы применить политику долгосрочного хранения резервных копий ко всем выбранным базам данных.

   ![Определение политики хранения](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Щелкните **Сохранить**, чтобы включить новую политику долгосрочного хранения резервных копий в настроенном хранилище служб восстановления Azure.

   ![Определение политики хранения](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

15. После включения долгосрочного хранения резервных копий откройте колонку **sqldbtutorialvault** (щелкните **Все ресурсы** и выберите эту базу данных в списке ресурсов для своей подписки).

   ![Просмотр хранилища служб восстановления](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)

   > [!IMPORTANT]
   > После настройки резервные копии появятся в хранилище в течение следующих семи дней. Не выполняйте следующие этапы до появления резервных копий в хранилище.
   >

## <a name="view-backups-in-long-term-retention"></a>Просмотр резервных копий в хранилище с включенной функцией долгосрочного хранения

При работе с этим разделом руководства вы просмотрите сведения о резервных копиях базы данных в хранилище с включенной функцией [долгосрочного хранения резервных копий](sql-database-long-term-retention.md). 

1. Откройте колонку **sqldbtutorialvault** (щелкните **Все ресурсы** и выберите эту базу данных в списке ресурсов для своей подписки), чтобы просмотреть сведения об объеме хранилища, используемого для хранения резервных копий базы данных.

   ![Просмотр хранилища служб восстановления с резервными копиями](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Откройте колонку **База данных SQL** для базы данных **sqldbtutorialdb**.

    ![Колонка нового примера базы данных](./media/sql-database-get-started/new-sample-db-blade.png)

3. На панели инструментов щелкните **Восстановить**.

    ![Элемент "Восстановить" на панели инструментов](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. В колонке "Восстановление" щелкните **Долгосрочные**.

5. В разделе Azure vault backups (Резервные копии хранилища Azure) щелкните **Выберите архив**, чтобы просмотреть список доступных резервных копий с долгосрочным хранением.

    ![Резервные копии в хранилище](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Восстановление базы данных из резервной копии с долгосрочным хранением

При работе с этим разделом руководства вы восстановите имеющуюся базу данных из резервной копии, расположенной в хранилище служб восстановления Azure, в новую базу данных.

1. В колонке **Azure vault backups** (Резервные копии хранилища Azure) щелкните резервную копию, которую нужно восстановить, а затем нажмите кнопку **Выбрать**.

    ![Выбор резервной копии в хранилище](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. В текстовом поле **Имя базы данных** введите имя для восстановленной базы данных.

    ![Имя новой базы данных](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Нажмите кнопку **ОК**, чтобы восстановить базу данных из резервной копии, расположенной в хранилище, в новую базу данных.

4. На панели инструментов щелкните значок уведомления, чтобы просмотреть состояние задания восстановления.

    ![Ход выполнения задания восстановления из хранилища](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. После завершения задания откройте колонку **Базы данных SQL**, чтобы просмотреть восстановленную базу данных.

    ![Имя базы данных, восстановленной из хранилища](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

   > [!NOTE]
   > Здесь вы можете подключиться к восстановленной базе данных с помощью SQL Server Management Studio и выполнить необходимые задания, например [извлечь часть данных из восстановленной базы данных, чтобы скопировать их в имеющуюся базу данных, или удалить имеющуюся базу данных и присвоить ее имя восстановленной базе данных](sql-database-recovery-using-backups.md#point-in-time-restore).
   >


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о резервных копиях базы данных, создаваемых автоматически службой, см. в [этой статье](sql-database-automated-backups.md).
- Дополнительные сведения о долгосрочном хранении резервных копий см. в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](sql-database-long-term-retention.md).
- Дополнительные сведения о восстановлении из резервных копий см. в статье [Восстановление базы данных Azure SQL с помощью создаваемых автоматически резервных копий](sql-database-recovery-using-backups.md).



<!--HONumber=Dec16_HO4-->


