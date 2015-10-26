<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: настройка Amazon | Microsoft Azure"
	description="Обеспечьте регистрацию и вход для пользователей с учетными записями Amazon в приложениях, защищенных с помощью Azure Active Directory B2C."
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
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory (AD) B2C: организация регистрации и входа для потребителей с учетными записями Amazon

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание приложения Amazon

Чтобы использовать Amazon в качестве поставщика удостоверений в Azure Active Directory (AD) B2C, необходимо сначала создать приложение Amazon и задать в нем правильные параметры. Для этого потребуется учетная запись Amazon. Если у вас ее нет, вы можете получить ее на сайте [http://www.amazon.com/](http://www.amazon.com/).

1. Перейдите на сайт [Amazon Developers Center](https://login.amazon.com/) и выполните вход с помощью учетной записи Amazon.
2. Если это еще не сделано, нажмите кнопку **Sign Up** (Зарегистрироваться), выполните действия для регистрации разработчика и примите условия политики.
3. Нажмите кнопку **Register new application** (Зарегистрировать новое приложение).

    ![Amazon — новое приложение](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Укажите информацию о приложении (**имя**, **описание** и **URL-адрес уведомления о конфиденциальности**) и нажмите кнопку **Save** (Сохранить).

    ![Amazon — регистрация приложения](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. В разделе **Web Settings** (Параметры Интернета) скопируйте значения **Client ID** (Идентификатор клиента) и **Client secret** (Секрет клиента) (чтобы увидеть его, надо нажать кнопку **Show Secret** (Показать секрет). Оба значения необходимы для настройки Amazon в качестве поставщика удостоверений в вашем каталоге. Щелкните **Edit** (Изменить) в нижней части раздела.

    > [AZURE.NOTE]**Секрет клиента** — это важные учетные данные безопасности.

    ![Amazon — секрет клиента](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Введите адрес [https://login.microsoftonline.com](https://login.microsoftonline.com) в поле **Allowed JavaScript origins** (Разрешенные источники JavaScript) и [https://login.microsoftonline.com/te/{каталог}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) в поле **Allowed Return URLs** (Разрешенные URL-адреса возврата), заменив **{каталог}** именем своего каталога (например, contoso.onmicrosoft.com). Щелкните **Сохранить**.

    > [AZURE.NOTE]В значении **{каталог}** учитывается регистр.

    ![Amazon — URL-адреса](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## Настройка Amazon в качестве поставщика удостоверений в каталоге

1. Откройте колонку функций B2C на [портале предварительной версии Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Щелкните **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите Amzn.
5. Щелкните **Тип поставщика удостоверений**, выберите **Amazon** и нажмите кнопку **ОК**.
6. Щелкните **Настроить этого поставщика удостоверений** и введите **идентификатор клиента** и **секрет клиента** ранее созданного приложения Amazon.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Amazon.

<!---HONumber=Oct15_HO3-->