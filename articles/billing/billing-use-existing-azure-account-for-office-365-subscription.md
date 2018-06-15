---
title: Оформление подписки на Office 365 с помощью учетной записи Azure | Документация Майкрософт
description: Узнайте, как создать подписку Office 365 с помощью учетной записи Azure
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: bce4b58d4197eee34be43a4dc852c6f43e0e18b4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203380"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Регистрация для подписки Office 365 с помощью учетной записи Azure
Если вы являетесь подписчиком Azure, то вы можете использовать учетную запись Azure для регистрации подписки Office 365. Если вы работаете в организации, которая имеет подписку Azure, то вы можете создать подписку Office 365 для пользователей в существующей службе Azure Active Directory (Azure AD). Подпишитесь на Office 365, используя учетную запись, которая имеет разрешения глобального администратора или администратора выставления счетов в клиенте Azure Active Directory. Дополнительные сведения см. в разделе [Проверка разрешений учетной записи в Azure AD](#RoleInAzureAD) и в статье [Назначение ролей администратора в Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

Если у вас уже есть учетная запись Office 365 и подписка Azure, то вы можете [связать клиент Office 365 с подпиской Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Оформление подписки Office 365 с помощью учетной записи Azure

1. Перейдите на [страницу продуктов Office 365](https://products.office.com/business) и выберите план.
2. Щелкните **Войти** в правом верхнем углу страницы.

    ![снимок экрана со страницы пробной версии Office 365](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Войдите, используя свои учетные данные Azure. При создании подписки для организации используйте учетную запись Azure, которая является участником роли каталога глобального администратора или администратора выставления счетов каталога в клиенте Azure Active Directory.

    ![Снимок экрана с входом в Office 365](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Щелкните **Попробовать**.

    ![Снимок экрана, на котором показано уведомление о подтверждении заказа Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. На странице подтверждения заказа щелкните **Продолжить**.

    ![Снимок экрана, на котором показано сообщение о получении заказа Office 365](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Теперь все готово. При создании подписки Office 365 для организации проверьте, находятся ли пользователи Azure AD в Office 365, выполнив следующие шаги.

1. Откройте центр администрирования Office 365.
2. Разверните раздел **Пользователи** и выберите **Активные пользователи**.

    ![Снимок экрана с пользователями в Центре администрирования Office 365](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

После регистрации подписка Office 365 будет добавлена в тот же экземпляр Azure Active Directory, в который входит ваша подписка Azure. Дополнительные сведения см. в разделе [Справочная информация о подписках Azure и Office 365](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) и в статье [Связь между подписками Azure и службой Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Проверка разрешений учетной записи в Azure AD
1. Войдите на [портале Azure](https://portal.azure.com/).
2. Щелкните **Все службы** и найдите **Active Directory**.

    ![Снимок экрана с Active Directory на портале Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Щелкните **Пользователи и группы** > **Все пользователи**.
4. Выберите пользователя. 

    ![Снимок экрана, на котором показаны пользователи Azure Active Directory](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Щелкните **Роль каталога**.
  
    ![Снимок экрана, на котором показана роль каталога на портале Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  Роли **Глобальный администратор** или **Администратор с ограниченными правами** > **Администратор выставления счетов** требуются для создания подписки Office 365 для пользователей в имеющемся экземпляре Azure Active Directory.

    ![Снимок экрана, на котором показана роль каталога "Администратор выставления счетов" на портале Azure](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему. 
