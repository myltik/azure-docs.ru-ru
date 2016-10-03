<properties
	pageTitle="Использование клиента Office 365 с подпиской Azure | Microsoft Azure"
	description="Узнайте, как добавить каталог (клиент) Office 365 в подписку Azure для создания взаимосвязи."
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
	ms.date="09/16/2016"
	ms.author="cjiang"/>

# Связывание клиента Office 365 с подпиской Azure
Сценарий. В прошлом вы приобрели по отдельности подписку Azure и подписку Office 365 и сейчас хотите иметь доступ к клиенту Office 365 из подписки Azure. В этой статье рассказывается, насколько просто это сделать.

> [AZURE.NOTE] Эта статья не относится к клиентам с соглашением Enterprise (EA).

## Краткое руководство
Если вы хотите связать клиент Office 365 с подпиской Azure, добавьте клиент Office 365 с помощью учетной записи Azure и свяжите подписку Azure с клиентом Office 365. См. подробные инструкции.

## Подробные инструкции
В этом сценарии у пользователя Келли (Kelley Wall) есть учетная запись kelly.wall@outlook.com с подпиской Azure. У Келли также есть подписка Office 365 в рамках учетной записи kelley.wall@contoso.onmicrosoft.com. Сейчас Келли хочет получить доступ к клиенту Office 365 с помощью подписки Azure.

![msa-aad-status](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![office-365-user](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

**Предварительные требования:**

- Необходимо иметь учетные данные администратора служб подписки Azure. Соадминистраторы не могут выполнять некоторые действия.
- Необходимо иметь учетные данные глобального администратора клиента Office 365.
- Адрес электронной почты администратора служб не должен содержаться на клиенте Office 365.
- Адрес электронной почты администратора служб не должен совпадать с адресом электронной почты любого из глобальных администраторов клиента Office 365.
- Если сейчас вы используете адрес электронной почты, который одновременно представляет учетную запись Майкрософт и корпоративную учетную запись, рекомендуется временно изменить роль администратора служб подписки Azure, чтобы использовать другую учетную запись Майкрософт. Это уменьшит известные ограничения для процедуры. Новую учетную запись Майкрософт можно создать на [странице регистрации учетных записей Майкрософт](https://signup.live.com/).

	Для изменения администратора служб выполните следующие действия.

	1. Войдите на [портал управления учетными записями](https://account.windowsazure.com/subscriptions).
	2. Выберите подписку, которую требуется изменить.
	3. В правой части щелкните **Изменить сведения о подписке**.

		![azure-edit-subscription-details](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

	4. В поле **Администратор служб** введите адрес электронной почты нового администратора служб.

		![change-subscription-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

Чтобы связать клиент Office 365 с подпиской Azure, выполните следующие действия.

1. 	Войдите на [портал управления учетными записями](https://account.windowsazure.com/subscriptions), используя учетные данные администратора служб.
2.	На левой панели щелкните **Active Directory**.

	![classic-portal-active-directory-entry](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

	> [AZURE.NOTE] Клиент Office 365 отображаться не должен. Если он отображается, пропустите следующий шаг.

	![aad-tenant-default](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Добавьте клиент Office 365 к своей подписке Azure.
	1. Щелкните **Создать** > **Каталог** > **Настраиваемое создание**.

		![aad-custom-create](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

	2. На странице **добавления каталога** в разделе **Каталог** выберите **Использовать существующий каталог**, установите флажок **Я готов к выходу из системы** и нажмите кнопку **Завершить** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![add-directory-use-existing](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

	3. После выхода из системы войдите в нее с помощью учетных данных глобального администратора клиента Office 365.

		![sign-in-global-admin-office-365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

	4. Нажмите кнопку **Продолжить**.

		![use-contoso-directory-azure-verify](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

	5. Щелкните **Выйти сейчас**.

		![use-contoso-directory-azure-confirm-and-sign-out](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

	6. Войдите на [портал управления учетными записями](https://account.windowsazure.com/subscriptions), используя учетные данные администратора служб.

		![azure-sign-in-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

	7. Клиент Office 365 должен отображаться на панели мониторинга.

		![office-365-tenant-appear-in-azure](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Перейдите в каталог, связанный с подпиской Azure.

	1. Щелкните **Параметры**.

		![azure-classic-portal-settings-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

	2. Выберите свою подписку Azure и щелкните **Изменить каталог**.

		![azure-subscription-edit-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

	3. Нажмите кнопку **Далее**![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

		![azure-change-associated-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

		> [AZURE.WARNING] Вы получите предупреждение о том, что все соадминистраторы будут удалены.

		![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

		>[AZURE.WARNING] Кроме того, все пользователи [управления доступом на основе ролей (RBAC)](./active-directory/role-based-access-control-configure.md), имеющие назначенные права доступа в существующих группах ресурсов, также будут удалены. Однако в предупреждении, которое вы получите, упоминается только об удалении соадминистраторов.

		![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

	4. Щелкните **Завершить**![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Теперь вы можете добавить учетные записи Office 365 своей организации в качестве соадминистраторов для клиента AAD.

	1. Откройте вкладку **Администраторы**, а затем щелкните **Добавить**.

		![azure-classic-portal-settings-administrators](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

	2. Укажите в клиенте Office 365 корпоративную учетную запись, выберите подписку Azure и нажмите кнопку **Завершить**![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

		![azure-add-co-administrator](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

	3. Вернитесь на вкладку **Администраторы**: учетная запись организации должна отображаться как соадминистратор.

		![azure-co-administrator-added](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. Затем можно проверить возможность доступа с учетными данными соадминистратора.

	1. Выйдите с портала управления учетными записями.
	2. Снова откройте [портал управления учетными записями](https://account.windowsazure.com/subscriptions) или [портал Azure](https://portal.azure.com/).
	3. Если на странице входа Azure содержится ссылка **Войдите с помощью учетной записи организации**, щелкните ее. В противном случае пропустите этот шаг.

		![Azure-sign-in-with-orgid-link](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

	4. Введите учетные данные соадминистратора и нажмите кнопку **Вход**.

		![Azure-sign-in-with-co-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## Дальнейшие действия
Есть похожие сценарии, когда у вас уже есть подписка Office 365 и вы готовы оформить подписку Azure, но хотите при этом использовать существующие учетные записи Office 365 для подписки Azure. Или же наоборот: вы являетесь подписчиком Azure и хотите получить подписку Office 365 для пользователей, внесенных в ваш каталог Azure Active Directory. Сведения о способах выполнения этих задач см. в статье [Использование существующей учетной записи Office 365 с подпиской Azure или наоборот](billing-use-existing-office-365-account-azure-subscription.md).

<!---HONumber=AcomDC_0921_2016-->