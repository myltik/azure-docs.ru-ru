---
title: "Использование клиента Office 365 с подпиской Azure | Документация Майкрософт"
description: "Узнайте, как добавить каталог (клиент) Office 365 в подписку Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: cjiang
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 42db903244e5642dbf273e71994d402f8aeda1e9
ms.contentlocale: ru-ru
ms.lasthandoff: 02/22/2017


---
<a id="associate-an-office-365-tenant-to-an-azure-subscription" class="xliff"></a>
# Связывание клиента Office 365 с подпиской Azure
Свяжите отдельные подписки Azure и Office 365, чтобы иметь доступ к клиенту Office 365 из подписки Azure. Чтобы связать подписки, войдите в Azure, используя учетную запись администратора служб Azure, добавьте каталог, а затем добавьте учетные записи организации Office 365 в клиент Azure Active Directory.

Если вам необходима подписка Office 365 для пользователей в экземпляре Azure Active Directory, или у вас есть учетная запись Office 365, но нет учетной записи Azure, то см. статью [Регистрация в Azure с помощью учетной записи Office 365](billing-use-existing-office-365-account-azure-subscription.md). 

<a id="before-you-begin" class="xliff"></a>
## Перед началом работы
* Необходимо иметь учетные данные администратора служб подписки Azure. Учетная запись соадминистратора не позволяет выполнить некоторые действия, описанные в этой статье. Чтобы изменить администратора службы, см. статью [Добавление или изменение ролей администратора Azure](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription).
* Необходимо иметь учетные данные глобального администратора клиента Office 365.
* Адрес электронной почты администратора служб не должен содержаться на клиенте Office 365.
* Адрес электронной почты администратора служб не должен совпадать с адресом электронной почты любого из глобальных администраторов клиента Office 365.
* Если вы используете электронный адрес, который одновременно представляет учетную запись Майкрософт и учетную запись организации, то временно измените роль администратора служб своей подписки Azure, чтобы использовать другую учетную запись Майкрософт. Учетную запись Майкрософт можно создать на [странице регистрации учетных записей Майкрософт](https://signup.live.com/).

<a id="link-office-365-tenant-to-azure-subscription" class="xliff"></a>
## Связывание клиента Office 365 с подпиской Azure
Чтобы связать клиент Office 365 с подпиской Azure, выполните описанные ниже действия.

<a id="step-1-add-office-365-tenant-to-your-azure-subscription" class="xliff"></a>
### Шаг 1. Добавление клиента Office 365 в подписку Azure

1. Войдите на [классический портал Azure](https://manage.windowsazure.com/), используя учетные данные администратора службы.

    ![Снимок экрана входа в Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

2. В области слева выберите **Active Directory**. Клиент Office 365 отображаться не должен. Если он отображается, то перейдите к разделу [Шаг 2. Изменение каталога, связанного с подпиской Azure](#Step2).
   
   ![Снимок экрана записи Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

3. Выберите **Создать** > **Каталог** > **Настраиваемое создание**.
   
    ![Снимок экрана создания настраиваемого каталога Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
4. На странице **Добавить каталог** в разделе **Каталог** выберите **Использовать существующий каталог**. Выберите **Я готов к выходу из системы** и щелкните **Завершить** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Снимок экрана с параметром "Использовать существующий каталог"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
5. После выхода из системы войдите в нее с помощью учетных данных глобального администратора клиента Office 365.
   
    ![Снимок экрана входа глобального администратора Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
6. Выберите **Продолжить**.
   
    ![Снимок экрана проверки](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
7. Щелкните **Выйти сейчас**.
   
    ![Снимок экрана выхода](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
8. Войдите на [классический портал Azure](https://manage.windowsazure.com/), используя учетные данные администратора службы.
   
    ![Снимок экрана входа в Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
9. Клиент Office 365 должен отображаться на панели мониторинга.
   
    ![Снимок экрана панели мониторинга](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>Шаг 2. Изменение каталога, связанного с подпиской Azure
   
1. Выберите элемент **Параметры**.
   
    ![Снимок экрана со значком настройки на классическом портале Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
2. Выберите свою подписку Azure и щелкните **Изменить каталог**.

    ![Снимок экрана изменения каталога подписки Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
3. Щелкните **Далее** ![значок "Далее"](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Снимок экрана диалогового окна "Изменить связанный каталог"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
4. Проверьте учетные записи, затронутые этим изменением. Все соадминистраторы и пользователи [управления доступом на основе ролей (RBAC)](../active-directory/role-based-access-control-configure.md), имеющие назначенные права доступа в существующих группах ресурсов, также удаляются. В предупреждении, которое вы получите, упоминается только об удалении соадминистраторов.
      
    ![Снимок экрана, показывающий удаляемые учетные записи администратора.](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![Снимок экрана, показывающий пример удаляемой учетной записи.](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
5. Щелкните **Завершить** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

<a id="step-3-add-your-office-365-organizational-accounts-as-co-administrators-to-the-azure-active-directory-tenant" class="xliff"></a>
### Шаг 3. Добавление учетных записей Office 365 организации в качестве соадминистраторов в клиент Azure Active Directory
   
1. Откройте вкладку **Администраторы**, а затем щелкните **Добавить**.
   
    ![Снимок экрана вкладки настройки администраторов на классическом портале Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
2. Укажите в клиенте Office 365 корпоративную учетную запись, выберите подписку Azure и щелкните **Завершить** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Снимок экрана диалогового окна добавления соадминистратора Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
3. Вернитесь на вкладку **Администраторы**. На ней должна отображаться учетная запись организации как соадминистратор.
   
    ![Снимок экрана вкладки "Администраторы"](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
4.  Проверьте возможность доступа к Azure с помощью учетной записи соадминистратора.
   
    а. Выйдите с классического портала Azure.
   
    b. Откройте [портал Azure](https://portal.azure.com/).
   
    c. Введите учетные данные соадминистратора и щелкните **Вход**.
   
    ![Снимок экрана страницы входа в Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

<a id="need-help-contact-support" class="xliff"></a>
## Требуется помощь? Обратитесь в службу поддержки.
Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.



