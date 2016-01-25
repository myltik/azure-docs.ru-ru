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
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory (AD) B2C: организация регистрации и входа для потребителей с учетными записями Facebook

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание приложения Facebook

Чтобы использовать Facebook в качестве поставщика удостоверений в Azure Active Directory (AD) B2C, необходимо сначала создать приложение Facebook и задать в нем правильные параметры. Для этого потребуется учетная запись Facebook. Если у вас ее нет, вы можете получить ее на сайте [https://www.facebook.com/](https://www.facebook.com/).

1. Перейдите на [веб-сайт разработчиков Facebook](https://developers.facebook.com/) и войдите с помощью учетной записи Facebook.
2. Если вы этого еще не сделали, необходимо зарегистрироваться в качестве разработчика Facebook. Для этого щелкните **Зарегистрироваться** (в правом верхнем углу страницы), примите политики Facebook и выполните необходимые действия для регистрации.
3. Щелкните **Приложения** и **Добавить новое приложение**. В качестве платформы выберите **Website** (Веб-сайт) и щелкните **Skip and Create App ID** (Пропустить и создать идентификатор приложения).

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
8. Скопируйте значение **App ID** (Идентификатор приложения). Нажмите кнопку **Show** (Показать) и скопируйте значение **App Secret** (Секрет приложения). Оба значения необходимы для настройки Facebook в качестве поставщика удостоверений для вашего клиента. Примечание. **Секрет приложения** — это важные учетные данные безопасности.

    ![FB — URL-адрес сайта](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. Щелкните вкладку **Дополнительно** вверху и введите `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` в поле **Действительные URI перенаправления OAuth** (в разделе **Безопасность**), заменив **{клиент}** именем своего клиента (например contosob2c.onmicrosoft.com). Нажмите кнопку **Save Changes** (Сохранить изменения) в нижней части страницы.

    ![FB — универсальный код ресурса (URI) перенаправления OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Чтобы подготовить приложение Facebook к использованию Azure AD B2C, необходимо сделать его общедоступным. Это можно сделать, щелкнув **Состояние и сводка** в левой области навигации и отправив приложение для просмотра (для этого нажмите кнопку **Начать отправку**). После того как приложение будет утверждено Facebook, его можно сделать общим, переключив параметр в верхней части страницы в значение **ДА**. После этого щелкните **Подтвердить**.

    ![FB — отправка приложения](./media/active-directory-b2c-setup-fb-app/fb-app-submission.png)

    ![FB — общедоступное приложение](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Настройка Facebook в качестве поставщика удостоверений для клиента

1. [Выполните следующие действия, чтобы перейти к колонке функций B2C на портале Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите FB.
5. Щелкните **Тип поставщика удостоверений**, выберите **Facebook** и нажмите кнопку **ОК**.
6. Щелкните **Настроить этого поставщика удостоверений** и введите **идентификатор клиента** и **секрет клиента** ранее созданного приложения Facebook в поля **Идентификатор клиента** и **Секрет клиента**, соответственно.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Facebook.

<!---HONumber=AcomDC_0114_2016-->