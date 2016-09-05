<properties
	pageTitle="Совместное использование одного клиента Azure AD с подписками Office 365 и Azure | Microsoft Azure"
	description="Узнайте, как передать клиент Azure AD Office 365 и его пользователей в подписку Azure, или наоборот."
	services=""
	documentationCenter=""
	authors="jiangchen79"
	manager="mbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="cjiang"/>

# Использование существующей учетной записи Office 365 с подпиской Azure или наоборот
Сценарий: у вас уже есть подписка Office 365 и вы готовы оформить подписку Azure, но хотите использовать для подписки Azure существующие учетные записи пользователей Office 365. Или же наоборот: вы являетесь подписчиком Azure и хотите получить подписку Office 365 для пользователей, внесенных в ваш каталог Azure Active Directory. В этой статье рассказывается, насколько легко это можно сделать.

> [AZURE.NOTE] Эта статья не относится к клиентам с соглашением Enterprise (EA).

> [AZURE.NOTE] Если в ходе работы с этой статьей вам потребуется дополнительная помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет вам быстро устранить проблему.

## Краткое руководство

- Если у вас уже есть подписка Office 365 и вы хотите подписаться на Azure, используйте действие **Войдите, используя учетную запись организации**, а затем оформите подписку Azure, указав существующую учетную запись Office 365. См. [подробные инструкции](#s1).
- Если у вас уже есть подписка Azure и вы хотите подписаться на Office 365, войдите в Office 365 с помощью учетной записи Azure и выполните процесс регистрации. После завершения регистрации подписка Office 365 будет добавлена в тот же каталог Azure Active Directory, в который входит подписка Azure. См. [подробные инструкции](#s2).

>[AZURE.NOTE] Для оформления подписки Office 365 учетная запись, используемая для входа, должна быть участником роли каталога глобального администратора или администратора выставления счетов в клиенте Azure AD. [Узнайте, как определить роль каталога Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory)

Чтобы понять механизм добавления подписки в учетную запись, см. раздел [Справочная информация](#background-information) далее в этой статье.

## Подробные инструкции
<a id="s1"></a>
### Сценарий 1: пользователи Office 365 планируют приобрести Azure
В этом сценарии предполагается, что пользователь Келли (Kelley Wall) имеет подписку Office 365 и планирует подписаться на Azure. Существует еще два дополнительных активных пользователя — Джейн (Jane) и Триша (Tricia). Учетная запись Келли — admin@contoso.onmicrosoft.com.

![office365-users-admin-center.png](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Чтобы подписаться на Azure, выполните следующие действия.

1. Подпишитесь на Azure на сайте [Azure.com](https://azure.microsoft.com/). Нажмите кнопку **Начните использовать бесплатно**. На следующей странице нажмите кнопку **Начать прямо сейчас**.

	![azure-signup-try-free](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Щелкните **Войдите, используя учетную запись организации**.

	![sign-in-to-azure](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Войдите, используя свою учетную запись Office 365. В данном случае это учетная запись Келли для входа в Office 365.

	![sign-in-with-org-account](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Введите необходимые сведения и завершите процесс регистрации.

	![azure-sign-up-fill-information](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

5. Нажмите кнопку **Начать управлять моей службой**. Теперь все готово.

	![azure-start-managing-my-service](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Теперь все готово. На портале Azure отобразятся те же пользователи и в том же каталоге. Чтобы это проверить, выполните следующие действия.

1. Нажмите кнопку **Начать управлять моей службой**, которая показана выше на снимке экрана.
2. Щелкните **Обзор**, а затем — **Active Directory**.

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Выберите **Пользователи**.

	![azure-portal-ad-users-tab](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Как и ожидалось, отобразились все пользователи, включая Келли.

	![azure-portal-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### Сценарий 2: пользователи Azure планируют приобрести Office 365

В этом сценарии у пользователя Келли (Kelley Wall) есть учетная запись admin@contoso.onmicrosoft.com с подпиской Azure. Келли хочет подписаться на Office 365 и использовать тот же каталог, который у нее уже есть в Azure.

>[AZURE.NOTE] Для оформления подписки Office 365 учетная запись, используемая для входа, должна быть участником роли каталога глобального администратора или администратора выставления счетов в клиенте Azure AD. [Узнайте, как определить роль каталога Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory)

![azure-portal-settings-subscription](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![azure-portal-ads-users](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Чтобы подписаться на Office 365, выполните следующие действия.

1. Перейдите на [страницу Office 365](https://products.office.com/business) и выберите интересующий вас план.
2. После выбора плана отобразится следующая страница. Не заполняйте эту форму. В верхнем правом углу страницы щелкните **Вход**.

	![office-365-trial-page](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Войдите, используя учетные данные своей учетной записи. В данном случае это учетная запись Келли.

	![office-365-sign-in](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Щелкните **Попробовать**.

	![office-365-confirm-your-order](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. На странице подтверждения заказа щелкните **Продолжить**.

	![office-365-order-receipt](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Теперь все готово. В центре администрирования Office 365 вы увидите тех же пользователей, что и в каталоге Contoso, отображающихся как активные пользователи. Чтобы это проверить, выполните следующие действия.

1. Откройте центр администрирования Office 365.
2. Разверните раздел **ПОЛЬЗОВАТЕЛИ** и выберите **Активные пользователи**.

	![office-365-admin-center-users](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### Как узнать свою роль в каталоге Azure Active Directory

1. Выполните вход на [портал Azure](https://portal.azure.com/).
2. Щелкните **Обзор**, а затем — **Active Directory**.

	![azure-portal-browse-ad](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Выберите **Пользователи**.

	![azure-portal-default-ad-users](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Выберите одного пользователя. В данном примере это Келли.
5. Обратите внимание на поле **РОЛЬ В ОРГАНИЗАЦИИ**.

	![azure-portal-user-identity](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## Справочная информация
Office 365 и Azure используют службу Azure Active Directory (AAD) для управления пользователями и подписками. Каталог Azure можно считать контейнером для группирования пользователей и подписок. Чтобы использовать одну учетную запись пользователя для подписок Microsoft Azure и Office 365, эти подписки должны быть созданы в одном и том же каталоге.

- Подписка создается в каталоге, но не наоборот.
- Пользователи принадлежат к каталогам, и никак иначе.
- Подписка сохраняется в каталоге пользователя, который создал эту подписку. Поэтому, если использовать для создания подписки Office 365 ту же учетную запись, для которой оформлена подписка Azure, обе подписки будут привязаны к одной учетной записи.

![background-information](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Дополнительные сведения см. в статье [Связь между подписками Azure и службой Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

**Примечания.**

- Подписки Azure принадлежат отдельным пользователям в каталоге.
- Подписки Office 365 принадлежат самому каталогу. Пользователи в каталоге могут выполнять с подписками различные действия при наличии необходимых разрешений.

##Дальнейшие действия
Возможен и такой сценарий: вы ранее приобрели по отдельности подписку Azure и подписку Office 365, а теперь хотите получить доступ к клиенту Office 365 из подписки Azure. Сведения о том, как это сделать, вы найдете в статье [Связывание клиента Office 365 с подпиской Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Если у вас есть дополнительные вопросы, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.

<!---HONumber=AcomDC_0824_2016-->