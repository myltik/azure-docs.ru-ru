<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: настройка LinkedIn | Microsoft Azure"
	description="Регистрация и вход пользователей с помощью учетных записей LinkedIn в приложениях, защищенных с помощью Azure Active Directory B2C"
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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory (AD) B2C: регистрация и вход пользователей с помощью учетных записей LinkedIn

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание приложения LinkedIn

Чтобы использовать LinkedIn в качестве поставщика удостоверений в Azure Active Directory (AD) B2C, необходимо сначала создать приложение LinkedIn и задать в нем правильные параметры. Для этого потребуется учетная запись LinkedIn. Если у вас ее нет, вы можете зарегистрироваться на сайте [https://www.linkedin.com/](https://www.linkedin.com/).

1. Посетите [сайт разработчиков LinkedIn](https://www.developer.linkedin.com/) и войдите с помощью своих учетных данных LinkedIn.
2. Нажмите **Мои приложения** в верхней строке меню и выберите пункт **Создать приложение**.

    ![LinkedIn — создание приложения](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. В форме **Создание нового приложения** укажите соответствующие сведения (**Название компании**, **Имя**, **Описание**, **URL-адрес логотипа приложения**, **Применение приложения**, **URL-адрес веб-сайта**, **Адрес электронной почты компании** и **Рабочий телефон**).
4. Примите условия использования API LinkedIn и нажмите кнопку **Отправить**.

    ![LinkedIn — регистрация приложения](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Скопируйте **идентификатор клиента** и **секрет клиента** (их можно найти в разделе **Ключи проверки подлинности**). Оба значения необходимы для настройки LinkedIn в качестве поставщика удостоверений в вашем каталоге.

    > [AZURE.NOTE]**Секрет клиента** — это важные учетные данные безопасности.

6. Введите адрес [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) в поле **Авторизованные URL-адреса перенаправления** (в разделе **OAuth 2.0**), заменив **{directory}** именем вашего каталога (например, contoso.onmicrosoft.com) и нажмите кнопку **Добавить**. Затем нажмите кнопку **Обновить**.

    ![LinkedIn — настройка приложения](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## Настройка LinkedIn в качестве поставщика удостоверений в каталоге

1. [Перейдите к колонке функций B2C на портале предварительной версии Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. В колонке функций B2C нажмите **Поставщики удостоверений социальных сетей**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите "LI".
5. Нажмите **Тип поставщика удостоверений**, выберите **LinkedIn** и нажмите кнопку **ОК**.
6. Нажмите **Настроить этого поставщика удостоверений** и введите **идентификатор клиента** и **секрет клиента** ранее созданного приложения LinkedIn.
7. Нажмите кнопку **ОК**, а затем **Создать**, чтобы сохранить конфигурацию LinkedIn.

<!---HONumber=Sept15_HO3-->