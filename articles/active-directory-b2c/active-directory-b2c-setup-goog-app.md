<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: настройка Google+ | Microsoft Azure"
	description="Обеспечьте регистрацию и вход для пользователей с учетными записями Google+ в приложениях, защищенных с помощью Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory (AD) B2C: организация регистрации и входа для потребителей с учетными записями Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание приложения Google+

Чтобы использовать Google+ в качестве поставщика удостоверений в Azure Active Directory (AD) B2C, необходимо сначала создать приложение Google+ и задать в нем правильные параметры. Для этого потребуется учетная запись Google+. Если у вас ее нет, вы можете получить ее, перейдя по адресу [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Перейдите на сайт [Google Developers Console](https://console.developers.google.com/) и выполните вход с учетной записью Google+.
2. Щелкните **Создать проект**, введите **Имя проекта** и щелкните **Создать**.

    ![G+ — приступая к работе](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ — новый проект](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. В левой области навигации щелкните **Менеджер API**, а затем **Учетные данные**.
4. Щелкните вкладку **OAuth consent screen** (Экран согласия OAuth) вверху.

    ![G+ — учетные данные](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Выберите или укажите допустимый **электронный адрес**, заполните поле **Product name** (Название продукта) и нажмите кнопку **Save** (Сохранить).

    ![G+ — экран согласия OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Щелкните **Add credentials** (Добавить учетные данные) и выберите **OAuth 2.0 client ID** (Идентификатор клиента OAuth 2.0).

    ![G+ — экран согласия OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. Из списка **Application type** (Тип приложения) выберите **Web application** (Веб-приложение).

    ![G+ — экран согласия OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. В поле **Имя** введите имя вашего приложения, в поле **Авторизованные источники JavaScript** — адрес [https://login.microsoftonline.com](https://login.microsoftonline.com), а в поле **Авторизованные URI перенаправления** — адрес `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`, заменив **{клиент}** именем своего клиента (например contosob2c.onmicrosoft.com). Щелкните **Создать**. Примечание. В значении **{клиент}** необходимо учитывать регистр.

    ![G+ — создание идентификатора клиента](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Скопируйте значения **Client ID** (Идентификатор клиента) и **Client secret** (Секрет клиента). Оба значения необходимы для настройки Google+ в качестве поставщика удостоверений в вашем клиенте. Примечание. **Секрет клиента** относится к важным учетным данным безопасности.

    ![G+ — секрет клиента](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Настройка Google+ в качестве поставщика удостоверений в клиенте

1. [Выполните следующие действия, чтобы перейти к колонке функций B2C на портале Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите G+.
5. Щелкните **Тип поставщика удостоверений**, выберите **Google** и нажмите кнопку **ОК**.
6. Щелкните **Настроить этот поставщик удостоверений** и введите **идентификатор клиента** и **секрет клиента** ранее созданного приложения Google+.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Google+.

<!---HONumber=AcomDC_0114_2016-->