---
title: 'Azure Active Directory B2C: настройка Facebook | Microsoft Docs'
description: Обеспечьте регистрацию и вход для пользователей с учетными записями Facebook в приложениях, защищенных с помощью Azure Active Directory B2C.
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
# Azure Active Directory B2C: регистрация и вход пользователей с учетными записями Facebook
## Создание приложения Facebook
Чтобы использовать Facebook в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение Facebook и задать в нем правильные параметры. Для этого потребуется учетная запись Facebook. Если у вас ее нет, создайте ее на сайте [https://www.facebook.com/](https://www.facebook.com/).

1. Перейдите на [веб-сайт разработчиков Facebook](https://developers.facebook.com/) и войдите с помощью учетной записи Facebook.
2. Если вы этого еще не сделали, необходимо зарегистрироваться в качестве разработчика Facebook. Для этого щелкните **Register** (Зарегистрироваться) в правом верхнем углу страницы, примите политики Facebook и завершите регистрацию.
3. Щелкните **My Apps** (Мои приложения), а затем — **Add a new App** (Добавить новое приложение). В качестве платформы выберите **Website** (Веб-сайт), а затем щелкните **Skip and Create App ID** (Пропустить и создать идентификатор приложения).
   
    ![Facebook — добавление нового приложения](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)
   
    ![Facebook — добавление нового приложения — веб-сайт](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)
   
    ![Facebook — создание идентификатора приложения](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)
4. В форме введите **отображаемое имя**, укажите действительный **контактный адрес электронной почты** и выберите требуемую **категорию**, а затем щелкните **Create App ID** (Создать идентификатор приложения). Для этого необходимо принять условия политики платформы Facebook и пройти проверку безопасности в сети.
   
    ![Facebook — создание идентификатора нового приложения](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)
5. В области навигации слева щелкните **Settings** (Параметры).
6. Щелкните **+Add Platform** (+Добавить платформу) и выберите **Website** (Веб-сайт).
   
    ![Facebook — настройки](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook — настройки — веб-сайт](./media/active-directory-b2c-setup-fb-app/fb-website.png)
7. Введите адрес [https://login.microsoftonline.com/](https://login.microsoftonline.com/) в поле **Site URL** (URL-адрес сайта) и нажмите кнопку **Save Changes** (Сохранить изменения).
   
    ![Facebook — URL-адрес сайта](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)
8. Скопируйте значение **App ID** (Идентификатор приложения). Нажмите кнопку **Show** (Показать) и скопируйте значение **App Secret** (Секрет приложения). Оба значения необходимы для настройки Facebook в качестве поставщика удостоверений для вашего клиента. **Секрет приложения** — это важные учетные данные безопасности.
   
    ![Facebook — идентификатор приложения и секрет приложения](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
9. В области навигации слева щелкните **+Add Product** (+Добавить продукт), а затем нажмите кнопку **Get Started** (Начать) рядом с разделом **Facebook Login** (Вход в Facebook).
   
    ![Facebook — вход в Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
10. В разделе **Client OAuth Settings** (Параметры OAuth клиента) в поле **Valid OAuth redirect URIs** (Допустимые URI перенаправления OAuth) введите `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com). Нажмите кнопку **Save Changes** (Сохранить изменения) в нижней части страницы.
    
    ![Facebook — универсальный код ресурса (URI) перенаправления OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
11. Чтобы подготовить приложение Facebook к использованию Azure AD B2C, необходимо сделать его общедоступным. Для этого можно щелкнуть **App Review** (Просмотр приложения) в области навигации слева и выбрать для переключателя в верхней части страницы значение **YES** (Да). Затем следует щелкнуть **Confirm** (Подтвердить).
    
    ![Facebook — публикация приложения](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Настройка Facebook в качестве поставщика удостоверений для клиента
1. Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) на портале Azure.
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите FB.
5. Щелкните **Identity provider type** (Тип поставщика удостоверений), выберите **Facebook** и нажмите кнопку **ОК**.
6. Щелкните **Set up this identity provider** (Настроить этот поставщик удостоверений) и введите идентификатор и секрет приложения (ранее созданного приложения Facebook) в поля **Идентификатор клиента** и **Секрет клиента** соответственно.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Facebook.

<!---HONumber=AcomDC_0727_2016-->