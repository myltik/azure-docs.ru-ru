<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: настройка Facebook | Microsoft Azure"
	description="Обеспечьте регистрацию и вход для пользователей с учетными записями Facebook в приложениях, защищенных с помощью Azure Active Directory B2C."
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
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory (AD) B2C: организация регистрации и входа для потребителей с учетными записями Facebook

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание приложения Facebook

Чтобы использовать Facebook в качестве поставщика удостоверений в Azure Active Directory (AD) B2C, необходимо сначала создать приложение Facebook и задать в нем правильные параметры. Для этого потребуется учетная запись Facebook. Если у вас ее нет, вы можете получить ее на сайте [https://www.facebook.com/](https://www.facebook.com/).

1. Перейдите на [веб-сайт разработчиков Facebook](https://developers.facebook.com/) и войдите с помощью учетной записи Facebook.
2. Если это еще не сделано, нажмите кнопку **Apps** (Приложения), затем **Register as a Developer** (Зарегистрироваться в качестве разработчика), примите условия политики и выполните действия по регистрации.
3. Щелкните **Apps** (Приложения) и **Add a new App** (Добавить новое приложение). В качестве платформы выберите **Website** (Веб-сайт) и щелкните **Skip and Create App ID** (Пропустить и создать идентификатор приложения).

    ![FB — создание нового приложения](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB — добавление нового приложения — веб-сайт](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB — создание идентификатора приложения](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. В форме введите **отображаемое имя**, выберите соответствующую **категорию** и нажмите кнопку **Create App ID** (Создать идентификатор приложения). Примечание. Для этого необходимо принять условия политики платформы Facebook и пройти проверку безопасности в сети.

    ![FB — создание идентификатора приложения](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. В левой области навигации щелкните **Settings** (Параметры). Введите действительный **контактный электронный адрес**.
6. Щелкните **+Add Platform** (+Добавить платформу) и выберите **Website** (Веб-сайт).

    ![FB — параметры](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB — параметры](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Введите адрес [https://login.microsoftonline.com/](https://login.microsoftonline.com/) в поле **Site URL** (URL-адрес сайта) и нажмите кнопку **Save Changes** (Сохранить изменения).
8. Скопируйте значение **App ID** (Идентификатор приложения). Нажмите кнопку **Show** (Показать) и скопируйте значение **App Secret** (Секрет приложения). Оба значения необходимы для настройки Facebook в качестве поставщика удостоверений в вашем каталоге.

    > [AZURE.NOTE]**Секрет приложения** — это важные учетные данные безопасности.

    ![FB — URL-адрес сайта](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. Щелкните вкладку **Advanced** (Дополнительно) вверху и введите адрес [https://login.microsoftonline.com/te/{каталог}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) в поле **Valid OAuth redirect URIs** (Действительные универсальные коды ресурсов (URI) OAuth) (в разделе **Security** (Безопасность)), заменив **{каталог}** именем своего каталога (например, contosob2c.onmicrosoft.com). Нажмите кнопку **Save Changes** (Сохранить изменения) в нижней части страницы.

    ![FB — универсальный код ресурса (URI) перенаправления OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Чтобы подготовить приложение Facebook к использованию Azure AD B2C, необходимо сделать его общедоступным. Для этого можно щелкнуть **Status & Review** (Состояние и просмотр) в левой области навигации и переключить параметр в верхней части страницы в значение **YES** (ДА). Затем нажмите кнопку **Confirm** (Подтвердить).

    ![FB — универсальный код ресурса (URI) перенаправления OAuth](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Настройка Facebook в качестве поставщика удостоверений в каталоге

1. Откройте колонку функций B2C на [портале предварительной версии Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. В колонке функций B2C щелкните **Поставщики удостоверений социальных сетей**.
3. Щелкните **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите FB.
5. Щелкните **Тип поставщика удостоверений**, выберите **Facebook** и нажмите кнопку **ОК**.
6. Щелкните **Настроить этого поставщика удостоверений** и введите **идентификатор клиента** и **секрет клиента** ранее созданного приложения Facebook в поля **Идентификатор клиента** и **Секрет клиента**, соответственно.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Facebook.

<!---HONumber=Sept15_HO4-->