---
title: Совместное использование одного клиента Azure AD с подписками Office 365 и Azure | Microsoft Docs
description: Узнайте, как передать клиент Azure AD Office 365 и его пользователей в подписку Azure, или наоборот.
services: ''
documentationcenter: ''
author: JiangChen79
manager: mbaldwin
editor: ''
tags: billing,top-support-issue

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cjiang

---
# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Использование существующей учетной записи Office 365 с подпиской Azure или наоборот
Сценарий: у вас уже есть подписка Office 365 и вы готовы оформить подписку Azure, но хотите использовать для подписки Azure существующие учетные записи пользователей Office 365. Или же наоборот: вы являетесь подписчиком Azure и хотите получить подписку Office 365 для пользователей, внесенных в ваш каталог Azure Active Directory. В этой статье рассказывается, насколько легко это можно сделать.

> [!NOTE]
> Эта статья не относится к клиентам с соглашением Enterprise (EA). Если в ходе работы с этой статьей вам потребуется дополнительная помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , которая поможет вам быстро устранить проблему.
> 
> 

## <a name="quick-guidance"></a>Краткое руководство
* Если у вас уже есть подписка Office 365 и вы хотите подписаться на Azure, используйте действие **Войдите, используя учетную запись организации**. Затем оформите подписку Azure, указав существующую учетную запись Office 365. [Подробные инструкции приведены далее в этой статье](#s1).
* Если у вас уже есть подписка Azure и вы хотите подписаться на Office 365, войдите в Office 365 с помощью учетной записи Azure. Затем выполните процесс регистрации. После завершения регистрации подписка Office 365 будет добавлена в тот же экземпляр Azure Active Directory, в который входит ваша подписка Azure. Чтобы узнать больше, ознакомьтесь с [дополнительными сведениями далее в этой статье](#s2).

> [!NOTE]
> Для оформления подписки Office 365 учетная запись, используемая для регистрации, должна быть участником роли каталога глобального администратора или администратора выставления счетов в клиенте Azure Active Directory. [Узнайте, как определить роль в Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).
> 
> 

Чтобы понять, что происходит при добавлении подписки в учетную запись, ознакомьтесь со [справочной информацией далее в этой статье](#background-information).

## <a name="detailed-steps"></a>Подробные инструкции
<a id="s1"></a>

### <a name="scenario-1:-office-365-users-who-plan-to-buy-azure"></a>Сценарий 1: пользователи Office 365 планируют приобрести подписку Azure
В этом сценарии предполагается, что пользователь Келли (Kelley Wall) имеет подписку Office 365 и планирует подписаться на Azure. Существует еще два дополнительных активных пользователя — Джейн (Jane) и Триша (Tricia). Учетная запись Келли — admin@contoso.onmicrosoft.com.

![Центр администрирования Office 365 для пользователя.](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Чтобы подписаться на Azure, выполните следующие действия.

1. Подпишитесь на Azure на сайте [Azure.com](https://azure.microsoft.com/). Щелкните **Бесплатное пробное использование**. На следующей странице нажмите кнопку **Начать прямо сейчас**.
   
    ![Воспользуйтесь бесплатной пробной версией Azure.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)
2. Щелкните **Войдите, используя учетную запись организации**.
   
    ![Войдите в Azure.](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)
3. Войдите, используя свою учетную запись Office 365. В данном случае это учетная запись Келли для входа в Office 365.
   
    ![Войдите, используя свою учетную запись Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)
4. Введите необходимые сведения и завершите процесс регистрации.
   
    ![Введите необходимые сведения и завершите регистрацию.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)
   
    ![Щелкните "Начать управлять моей службой".](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Теперь все готово. На портале Azure должны появиться те же пользователи. Чтобы это проверить, выполните следующие действия.

1. Щелкните **Начать управлять моей службой**, как показано на предыдущем снимке экрана.
2. Щелкните **Обзор**, а затем — **Active Directory**.
   
    ![Щелкните "Обзор", а затем — "Active Directory".](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. Выберите **Пользователи**.
   
    ![Вкладка "Пользователи"](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)
4. Как и ожидалось, отобразились все пользователи, включая Келли.
   
    ![Список пользователей](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>

### <a name="scenario-2:-azure-users-who-plan-to-buy-office-365"></a>Сценарий 2: пользователи Azure планируют приобрести подписку Office 365
В этом сценарии у пользователя Келли (Kelley Wall) есть учетная запись admin@contoso.onmicrosoft.com. Келли хочет подписаться на Office 365 и использовать тот же каталог, который у нее уже есть в Azure.

> [!NOTE]
> Для оформления подписки Office 365 учетная запись, используемая для входа, должна быть участником роли каталога глобального администратора или администратора выставления счетов в клиенте Azure Active Directory. [Узнайте, как определить роль в Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).
> 
> 

![Параметры подписки на портале Azure](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Пользователи Active Directory на портале Azure](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Чтобы подписаться на Office 365, выполните следующие действия.

1. Перейдите на [страницу Office 365](https://products.office.com/business)и выберите интересующий вас план.
2. После выбора плана отобразится следующая страница. Не заполняйте эту форму. Щелкните **Войти** в правом верхнем углу страницы.
   
    ![Страница пробной версии Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)
3. Войдите, используя свои учетные данные. В данном примере это учетная запись Келли.
   
    ![Вход в Office 365](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)
4. Щелкните **Попробовать**.
   
    ![Подтвердите свой заказ для Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)
5. На странице подтверждения заказа щелкните **Продолжить**.
   
    ![Получение заказа Office 365](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Теперь все готово. В Центре администрирования Office 365 вы увидите тех же пользователей, что и в каталоге Contoso, отображающихся как активные пользователи. Чтобы это проверить, выполните следующие действия.

1. Откройте центр администрирования Office 365.
2. Разверните раздел **Пользователи** и выберите **Активные пользователи**.
   
    ![Пользователи в Центре администрирования Office 365](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Как узнать свою роль в каталоге Azure Active Directory
1. Войдите на [портал Azure](https://portal.azure.com/).
2. Щелкните **Обзор**, а затем — **Active Directory**.
   
    ![Active Directory на портале Azure](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. Выберите **Пользователи**.
   
    ![Пользователи Active Directory по умолчанию на портале Azure](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)
4. Выберите одного пользователя. В этом примере пользователем является Келли.
   
    Обратите внимание на поле **РОЛЬ В ОРГАНИЗАЦИИ**.
   
    ![Удостоверение пользователя портала Azure](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Справочная информация о подписках Azure и Office 365
Office 365 и Azure используют службу Azure Active Directory для управления пользователями и подписками. Каталог Azure можно считать контейнером, в котором можно группировать пользователей и подписки. Чтобы использовать одну учетную запись пользователя для подписок Microsoft Azure и Office 365, эти подписки должны быть созданы в одном и том же каталоге. Помните на следующие моменты.

* Подписка создается в каталоге, но не наоборот.
* Пользователи принадлежат к каталогам, и никак иначе.
* Подписка сохраняется в каталоге пользователя, который ее создал. Поэтому, если для создания подписки Office 365 использовать указывает ту же учетную запись, для которой оформлена подписка Azure, то обе подписки будут привязаны к одной учетной записи.

![Справочная информация](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Дополнительные сведения см. в статье [Связь между подписками Azure и службой Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

> [!NOTE]
> Подписки Azure принадлежат отдельным пользователям в каталоге.
> 
> [!NOTE]
> Подписки Office 365 принадлежат самому каталогу. Если у пользователей в каталоге есть соответствующие разрешения, то они могут выполнять различные действия с этими подписками.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Если в прошлом вы приобрели по отдельности подписку Azure и подписку Office 365 и сейчас хотите иметь доступ к клиенту Office 365 из подписки Azure, ознакомьтесь с разделом [Связывание клиента Office 365 с подпиской Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [!NOTE]
> Если у вас есть дополнительные вопросы, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.
> 
> 

<!--HONumber=Oct16_HO2-->


