---
title: 'Azure Active Directory B2C: настройка Google+ | Microsoft Docs'
description: Обеспечение регистрации и входа для пользователей с учетными записями Google+ в приложениях, защищенных с помощью Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2016
ms.author: swkrish

---
# Azure Active Directory B2C: организация регистрации и входа для потребителей с учетными записями Google+
## Создание приложения Google+
Чтобы использовать Google+ в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение Google+ и задать в нем правильные параметры. Для этого потребуется учетная запись Google+. Если у вас ее нет, вы можете создать такую учетную запись на странице [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Перейдите на сайт [Google Developers Console](https://console.developers.google.com/) и выполните вход с учетной записью Google+.
2. Щелкните **Create project** (Создать проект), введите значение в поле **Project name** (Имя проекта) и щелкните **Create** (Создать).
   
    ![Google+: приступая к работе](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google+: создание проекта](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. В левой области навигации щелкните **API Manager** (Менеджер API), а затем **Credentials** (Учетные данные).
4. Щелкните вкладку **OAuth consent screen** (Экран согласия OAuth) вверху.
   
    ![Google+: учетные данные](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Выберите или укажите допустимый **электронный адрес**, заполните поле **Product name** (Название продукта) и нажмите кнопку **Save** (Сохранить).
   
    ![Google+: экран согласия OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Щелкните **New credentials** (Создать учетные данные) и выберите **OAuth client ID** (Идентификатор клиента OAuth).
   
    ![Google+: экран согласия OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. Из списка **Application type** (Тип приложения) выберите **Web application** (Веб-приложение).
   
    ![Google+: экран согласия OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. В поле **Name** (Имя) введите имя приложения, затем введите `https://login.microsoftonline.com` в поле **Authorized JavaScript origins** (Авторизованные источники JavaScript) и `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` — в поле **Authorized redirect URIs** (Авторизованные URI перенаправления). Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com). В значении **{клиент}** необходимо учитывать регистр. Щелкните **Создать**.
   
    ![Google+: создание идентификатора клиента](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Скопируйте значения **Client ID** (Идентификатор клиента) и **Client secret** (Секрет клиента). Оба значения необходимы для настройки Google+ в качестве поставщика удостоверений в вашем клиенте. **Секрет клиента** — это важные учетные данные безопасности.
   
    ![Google+: секрет клиента](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Настройка Google+ в качестве поставщика удостоверений в клиенте
1. Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) на портале Azure.
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите G+.
5. Щелкните **Тип поставщика удостоверений**, выберите **Google** и нажмите кнопку **ОК**.
6. Щелкните **Настроить этот поставщик удостоверений** и введите идентификатор клиента и секрет клиента ранее созданного приложения Google+.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Google+.

<!---HONumber=AcomDC_0727_2016-->