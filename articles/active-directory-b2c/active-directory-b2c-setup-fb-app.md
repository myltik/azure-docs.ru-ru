<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: настройка Facebook | Microsoft Azure"
	description="Обеспечьте регистрацию и вход для пользователей с учетными записями Facebook в приложениях, защищенных с помощью Azure Active Directory B2C."
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
	ms.date="06/06/2016"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory B2C: организация регистрации и входа для пользователей с учетными записями Facebook

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание приложения Facebook

Чтобы использовать Facebook в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение Facebook и задать в нем правильные параметры. Для этого потребуется учетная запись Facebook. Если у вас нет, зарегистрируйте ее на сайте [https://www.facebook.com/](https://www.facebook.com/).

1. Перейдите на [веб-сайт разработчиков Facebook](https://developers.facebook.com/) и войдите с помощью учетной записи Facebook.
2. Если вы этого еще не сделали, необходимо зарегистрироваться в качестве разработчика Facebook. Для этого щелкните **Зарегистрироваться** (в правом верхнем углу страницы), примите политики Facebook и выполните необходимые действия для регистрации.
3. Щелкните **Мои приложения**, затем — **Добавить новое приложение**. Выберите **Веб-сайт** в качестве платформы и затем щелкните **Пропустить и создать идентификатор приложения**.

    ![Facebook — добавление нового приложения](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook — добавление нового приложения — веб-сайт](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook — создание идентификатора приложения](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. В форме введите **отображаемое имя**, действительный адрес **контактной электронной почты**, выберите соответствующую **категорию** и нажмите кнопку **Create App ID** (Создать идентификатор приложения). Для этого необходимо принять условия политики платформы Facebook и пройти проверку безопасности в сети.

    ![Facebook — создание идентификатора нового приложения](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. В левой области навигации щелкните **Параметры**.
6. Щелкните **+Добавить платформу** и выберите **Веб-сайт**.

    ![Facebook — настройки](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Facebook — настройки — веб-сайт](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Введите адрес [https://login.microsoftonline.com/](https://login.microsoftonline.com/) в поле **Site URL** (URL-адрес сайта) и нажмите кнопку **Save Changes** (Сохранить изменения).

    ![Facebook — URL-адрес сайта](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. Скопируйте значение **App ID** (Идентификатор приложения). Нажмите кнопку **Show** (Показать) и скопируйте значение **App Secret** (Секрет приложения). Оба значения необходимы для настройки Facebook в качестве поставщика удостоверений для вашего клиента. **Секрет приложения** — это важные учетные данные безопасности.

    ![Facebook — идентификатор приложения и секрет приложения](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. В левой области навигации щелкните **+ Add Product** (+ Добавить продукт), а затем нажмите кнопку **Get Started** (Начать) рядом с разделом **Facebook Login** (Вход в Facebook).

    ![Facebook — вход в Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. В разделе **Client OAuth Settings** (Параметры OAuth клиента) в поле **Valid OAuth redirect URIs** (Допустимые URI перенаправления OAuth) введите `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com). Нажмите кнопку **Save Changes** (Сохранить изменения) в нижней части страницы.

    ![Facebook — универсальный код ресурса (URI) перенаправления OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. Чтобы подготовить приложение Facebook к использованию Azure AD B2C, необходимо сделать его общедоступным. Для этого можно щелкнуть **App Review** (Обзор приложения) в левой области навигации и переключить параметр в верхней части страницы в значение **YES** (ДА). Затем следует щелкнуть **Confirm** (Подтвердить).

    ![Facebook — публикация приложения](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Настройка Facebook в качестве поставщика удостоверений для клиента

1. Выполните действия по [переходу к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) на портале Azure.
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите FB.
5. Щелкните **Identity provider type** (Тип поставщика удостоверений), выберите **Facebook** и нажмите кнопку **ОК**.
6. Щелкните **Set up this identity provider** (Настроить этот поставщик удостоверений) и введите идентификатор приложения и секрет приложения (ранее созданного приложения Facebook) в поля **Идентификатор клиента** и **Секрет клиента** соответственно.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Facebook.

<!---HONumber=AcomDC_0608_2016-->