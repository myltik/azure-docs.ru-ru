---
title: "Использование клиента Office 365 с подпиской Azure | Документация Майкрософт"
description: "Узнайте, как добавить каталог (клиент) Office 365 в подписку Azure для создания взаимосвязи."
services: 
documentationcenter: 
author: JiangChen79
manager: mbaldwin
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 36cd9eac7be2d99971d8d2e227cd4b761df82d08


---
# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Связывание клиента Office 365 с подпиской Azure
Если в прошлом вы приобрели по отдельности подписку Azure и подписку Office 365 и сейчас хотите иметь доступ к клиенту Office 365 из подписки Azure, то это просто. В этой статье показано, как это сделать.

> [!NOTE]
> Эта статья не относится к клиентам с соглашением Enterprise (EA).
> 
> 

## <a name="quick-guidance"></a>Краткое руководство
Чтобы связать клиент Office 365 с подпиской Azure, добавьте клиент Office 365 с помощью учетной записи Azure и свяжите подписку Azure с клиентом Office 365.

## <a name="detailed-steps"></a>Подробные инструкции
В этом сценарии у пользователя Келли (Kelley Wall) есть учетная запись kelley.wall@outlook.com.. У Келли также есть подписка Office 365 в рамках учетной записи kelley.wall@contoso.onmicrosoft.com.. Сейчас Келли хочет получить доступ к клиенту Office 365 с помощью подписки Azure.

![Снимок экрана состояния Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Снимок экрана активных пользователей в Центре администрирования Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Предварительные требования
Для правильной работы связи необходимы приведенные ниже компоненты.

* Необходимо иметь учетные данные администратора служб подписки Azure. Соадминистраторы не могут выполнять некоторые действия.
* Необходимо иметь учетные данные глобального администратора клиента Office 365.
* Адрес электронной почты администратора служб не должен содержаться на клиенте Office 365.
* Адрес электронной почты администратора служб не должен совпадать с адресом электронной почты любого из глобальных администраторов клиента Office 365.
* Если сейчас вы используете электронный адрес, который одновременно представляет учетную запись Майкрософт и корпоративную учетную запись, то временно измените роль администратора служб своей подписки Azure, чтобы использовать другую учетную запись Майкрософт. Новую учетную запись Майкрософт можно создать на [странице регистрации учетных записей Майкрософт](https://signup.live.com/).

Для изменения администратора служб выполните следующие действия.

1. Войдите на [портал управления учетными записями](https://account.windowsazure.com/subscriptions).
2. Выберите подписку, которую требуется изменить.
3. Выберите **Изменить сведения о подписке**.
   
    ![Снимок экрана сведений о подписке Azure, на котором выделен элемент "Изменить сведения о подписке"](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)
4. В поле **Администратор служб** введите адрес электронной почты нового администратора служб.
   
    ![Снимок экрана диалогового окна "Изменение подписки"](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Связывание клиента Office 365 с подпиской Azure
Чтобы связать клиент Office 365 с подпиской Azure, выполните следующие действия.

1. Войдите на [портал управления учетными записями](https://account.windowsazure.com/subscriptions) , используя учетные данные администратора служб.
2. В области слева выберите **Active Directory**.
   
   ![Снимок экрана записи Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
   
   > [!NOTE]
   > Клиент Office 365 отображаться не должен. Если он отображается, пропустите следующий шаг.
   > 
   > 
   
   ![Снимок экрана каталога по умолчанию Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)
3. Добавьте клиент Office 365 к своей подписке Azure.
   
    а. Выберите **Создать** > **Каталог** > **Настраиваемое создание**.
   
    ![Снимок экрана создания настраиваемого каталога Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
    b. На странице **Добавить каталог** в разделе **Каталог** выберите **Использовать существующий каталог**. Выберите **Я готов к выходу из системы** и щелкните **Завершить** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Снимок экрана с параметром "Использовать существующий каталог"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
    c. После выхода из системы войдите в нее с помощью учетных данных глобального администратора клиента Office 365.
   
    ![Снимок экрана входа глобального администратора Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
    d. Выберите **Продолжить**.
   
    ![Снимок экрана проверки](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
    д. Щелкните **Выйти сейчас**.
   
    ![Снимок экрана выхода](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
    Е. Войдите на [портал управления учетными записями](https://account.windowsazure.com/subscriptions) , используя учетные данные администратора служб.
   
    ![Снимок экрана входа в Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
    ж. Клиент Office 365 должен отображаться на панели мониторинга.
   
    ![Снимок экрана панели мониторинга](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)
4. Перейдите в каталог, связанный с подпиской Azure.
   
    а. Выберите элемент **Параметры**.
   
    ![Снимок экрана со значком настройки на классическом портале Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
    b. Выберите свою подписку Azure и щелкните **Изменить каталог**.
    ![Снимок экрана изменения каталога подписи Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
    c. Щелкните **Далее** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).
   
    ![Снимок экрана диалогового окна "Изменить связанный каталог"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
   > [!WARNING]
   > Вы получите предупреждение о том, что все соадминистраторы будут удалены.
   > 
   > 
   
    ![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
   > [!WARNING]
   > Кроме того, все пользователи [управления доступом на основе ролей (RBAC)](active-directory/role-based-access-control-configure.md) , имеющие назначенные права доступа в существующих группах ресурсов, также будут удалены. Однако в предупреждении, которое вы получите, упоминается только об удалении соадминистраторов.
   > 
   > 
   
    ![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
    d. Щелкните **Завершить** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
5. Теперь вы можете добавить учетные записи Office 365 своей организации в качестве соадминистраторов для клиента Azure Active Directory.
   
    а. Откройте вкладку **Администраторы**, а затем щелкните **Добавить**.
   
    ![Снимок экрана вкладки настройки администраторов на классическом портале Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
    b. Укажите в клиенте Office 365 корпоративную учетную запись, выберите подписку Azure и щелкните **Завершить** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).
   
    ![Снимок экрана диалогового окна добавления соадминистратора Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
    c. Вернитесь на вкладку **Администраторы**. На ней должна отображаться учетная запись организации как соадминистратор.
   
    ![Снимок экрана вкладки "Администраторы"](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
6. Затем можно проверить возможность доступа с учетными данными соадминистратора.
   
    а. Выйдите с портала управления учетными записями.
   
    b. Откройте [портал управления учетными записями](https://account.windowsazure.com/subscriptions) или [портал Azure](https://portal.azure.com/).
   
    c. Если на странице входа Azure содержится ссылка **Войдите с помощью учетной записи организации**, щелкните ее. В противном случае пропустите этот шаг.
   
    ![Снимок экрана страницы входа в Azure](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)
   
    d. Введите учетные данные соадминистратора и щелкните **Вход**.
   
    ![Снимок экрана страницы входа в Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Дальнейшие действия
Ниже приведены похожие сценарии:

* У вас уже есть подписка Office 365 и вы готовы оформить подписку Azure, но хотите использовать для подписки Azure существующие учетные записи пользователей Office 365.
* Вы являетесь подписчиком Azure и хотите получить подписку Office 365 для пользователей, внесенных в ваш каталог Azure Active Directory.

Сведения о способах выполнения этих задач см. в статье [Использование существующей учетной записи Office 365 с подпиской Azure или наоборот](billing-use-existing-office-365-account-azure-subscription.md).




<!--HONumber=Nov16_HO3-->


