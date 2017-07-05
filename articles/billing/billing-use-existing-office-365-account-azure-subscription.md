---
title: "Регистрация в Azure с помощью учетной записи Office 365 | Документация Майкрософт"
description: "Узнайте, как создать подписку Azure с помощью учетной записи Office 365"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: cjiang
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d62ec023605573e4b0804da00c75f65c8dea5173
ms.contentlocale: ru-ru
ms.lasthandoff: 04/12/2017


---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Регистрация для получения подписки Azure с помощью учетной записи Office 365
Если у вас есть подписка Office 365, используйте учетную запись Office 365 для создания подписки Azure. Вы можете войти на [портал Azure](https://portal.azure.com/) без подписки Azure, используя имя пользователя и пароль Office 365. Но если вы хотите настроить виртуальные машины или использование других служб Azure, необходимо создать подписку Azure для учетной записи. Если вы работаете в организации, в которой планируется использовать службы Azure, добавьте других пользователей в организации в подписку после ее создания.  

При наличии учетной записи Office 365 и подписки Azure см. статью [Связывание клиента Office 365 с подпиской Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-by-using-your-office-365-account"></a>Получение подписки Azure с помощью учетной записи Office 365

Чтобы зарегистрироваться для работы с Azure, перейдите по ссылке на бесплатную версию Azure и зарегистрируйтесь в Azure, используя имя пользователя и пароль Office 365. 

1. Перейдите на сайт [Azure.com](https://azure.microsoft.com/). 
2. Щелкните **Начать бесплатно**.
3. Войдите, используя имя пользователя и пароль Office 365. Используемой учетной записи не нужны права администратора. Если у вас есть несколько учетных записей Office 365, убедитесь, что используются учетные данные учетной записи Office 365, которую требуется связать с подпиской Azure. 

   ![Снимок экрана, на котором показана страница входа.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

4. Введите необходимые сведения и завершите процесс регистрации.

    ![Снимок экрана, на котором показана форма регистрации.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Если необходимо добавить других пользователей организации в подписку Azure, см. статью [Начало работы с управлением доступом на портале Azure](../active-directory/role-based-access-control-what-is.md). 
- Если вы хотите добавить администратора для управления подписками, см. статью [Добавление или изменение ролей администратора Azure, управляющих подписками и службами](billing-add-change-azure-subscription-administrator.md).

## <a id="more-about-subs">Справочная информация о подписках Azure и Office 365</a>
Office 365 и Azure используют службу Azure AD для управления пользователями и подписками. Считайте, что каталог Azure — это контейнер, в котором можно группировать пользователей и подписки. Чтобы использовать одну учетную запись пользователя для подписок Microsoft Azure и Office 365, эти подписки должны быть созданы в одном и том же каталоге. Помните на следующие моменты.

* Подписка создается в каталоге.
* Пользователи принадлежат к каталогам.
* Подписка сохраняется в каталоге пользователя, который ее создал. Подписка Office 365 и Azure привязаны к одной учетной записи.
* Подписки Azure принадлежат отдельным пользователям в каталоге.
* Подписки Office 365 принадлежат самому каталогу. Пользователи с соответствующими разрешениями в каталоге могут управлять этими подписками.

![Снимок экрана, на котором показана связь между каталогом, пользователями и подписками.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Дополнительные сведения см. в статье [Связь между подписками Azure и службой Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему. 
