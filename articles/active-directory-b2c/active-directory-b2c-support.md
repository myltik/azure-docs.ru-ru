<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: поддержка | Microsoft Azure"
	description="Как регистрировать запросы на поддержку для Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/24/2015"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory B2C: регистрация запросов на поддержку для Azure Active Directory B2C

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Вы можете регистрировать запросы на поддержку для Azure Active Directory (AD) B2C на портале предварительной версии Azure, выполнив следующее:

1. Откройте колонку функций B2C на [портале предварительной версии Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Переключитесь с вашего клиента B2C на другой клиент, с которым сопоставлена подписка Azure. Это может быть клиент вашего сотрудника или клиент по умолчанию, который был создан, когда вы зарегистрировались для получения подписки Azure. Прочитайте [эту статью](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad), чтобы больше узнать о связи между подписками Azure и каталогами Azure AD.

    > [AZURE.IMPORTANT]Этот шаг является обязательным. Процесс завершится ошибкой, если не выполнить этот шаг.

    ![Поддержка — переключение каталогов](./media/active-directory-b2c-support/support-switch-dir.png)

3. После переключения каталогов щелкните **Справка и поддержка**.

    ![Поддержка — поддержка](./media/active-directory-b2c-support/support-support.png)

4. Выполните шаги, описанные в [этой статье](http://blogs.msdn.com/b/mast/archive/2013/10/24/windows-azure-technical-support-for-msdn-technet-or-mpn-users-and-partners.aspx), чтобы регистрировать запросы на поддержку для Azure AD B2C. Используйте эти данные для выполнения действий:

    - **Тип запроса** — **Технический**.
	- **Ресурс** — **Active Directory**.
	- В колонке **Категория проблемы** выберите **Предварительная версия B2C** для параметра **Тип проблемы** и соответствующее значение **Категория**.
	- В колонке **Описание** подробно опишите неполадку. В текстовом поле **Ресурс** укажите имя клиента B2C, например contosob2c.onmicrosoft.com.

5. После отправки запроса на поддержку вы можете отслеживать его, щелкнув **Справка и поддержка** на начальной панели, а затем — **Управление запросами на поддержку**.

## Известная проблема: регистрация запроса на поддержку в контексте клиента B2C

Если вы пропустили описанный выше шаг 2 и пытаетесь создать запрос на поддержку в контексте вашего клиента B2C, появится указанная ниже ошибка.

> [AZURE.IMPORTANT]Не пытайтесь зарегистрироваться для получения новой подписки Azure в вашем клиенте B2C.

![Поддержка — нет подписки](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=Oct15_HO2-->