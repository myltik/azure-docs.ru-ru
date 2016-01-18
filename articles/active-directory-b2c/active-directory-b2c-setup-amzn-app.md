<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: настройка Amazon | Microsoft Azure"
	description="Обеспечьте регистрацию и вход для пользователей с учетными записями Amazon в приложениях, защищенных с помощью Azure Active Directory B2C."
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
	ms.date="01/06/2016"
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

5. В разделе **Web Settings** (Параметры Интернета) скопируйте значения **Client ID** (Идентификатор клиента) и **Client secret** (Секрет клиента) (чтобы увидеть его, надо нажать кнопку **Show Secret** (Показать секрет). Оба значения необходимы для настройки Amazon в качестве поставщика удостоверений для вашего клиента. Щелкните **Edit** (Изменить) в нижней части раздела.

> [AZURE.NOTE]**Секрет клиента** — это важные учетные данные безопасности.

    ![Amazon - Client secret](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Введите адрес [https://login.microsoftonline.com](https://login.microsoftonline.com) в поле **Разрешенные источники JavaScript** и `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` в поле **Разрешенные URL-адреса возврата**, заменив **{клиент}** именем своего клиента (например contoso.onmicrosoft.com). Щелкните **Сохранить**.

> [AZURE.NOTE]В значении **{клиент}** необходимо учитывать регистр.

    ![Amazon - URLs](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## Настройка Amazon в качестве поставщика удостоверений для клиента

1. [Выполните следующие действия, чтобы перейти к колонке функций B2C на портале Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите Amzn.
5. Щелкните **Тип поставщика удостоверений**, выберите **Amazon** и нажмите кнопку **ОК**.
6. Щелкните **Настроить этого поставщика удостоверений** и введите **идентификатор клиента** и **секрет клиента** ранее созданного приложения Amazon.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Amazon.

<!---HONumber=AcomDC_0107_2016-->