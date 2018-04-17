---
title: 'Azure Active Directory B2C: настройка Amazon | Документация Майкрософт'
description: Обеспечение регистрации и входа для пользователей с учетными записями Amazon в приложениях, защищенных с помощью Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: a2989baa61e7b69534fe5703b2501d62a4f8aa94
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: регистрация и вход пользователей с учетными записями Amazon
## <a name="create-an-amazon-application"></a>Создание приложения Amazon
Чтобы использовать Amazon в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение Amazon и задать в нем правильные параметры. Для этого потребуется учетная запись Amazon. Если у вас нет этой учетной записи, ее можно получить на сайте [http://www.amazon.com/](http://www.amazon.com/).

1. Перейдите на сайт [Amazon Developer Center](https://login.amazon.com/) войдите с помощью учетной записи Amazon.
2. Если это еще не сделано, нажмите кнопку **Sign Up**(Регистрация), выполните действия для регистрации разработчика и примите условия политики.
3. Нажмите кнопку **Register new application**(Зарегистрировать новое приложение).
   
    ![Регистрация нового приложения на веб-сайте Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Укажите информацию о приложении (**имя**, **описание** и **URL-адрес для уведомления о конфиденциальности**) и нажмите кнопку **Сохранить**.
   
    ![Предоставление информации для регистрации нового приложения на сайте Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. В разделе **Веб-параметры** скопируйте значения **Идентификатор клиента** и **Секрет клиента**. Для отображения этих параметров необходимо нажать кнопку **Показать секрет**. Оба значения необходимы для настройки Amazon в качестве поставщика удостоверений для вашего клиента. Щелкните **Изменить** в нижней части раздела. **Секрет клиента** — это важные учетные данные безопасности.
   
    ![Предоставление идентификатора клиента и секрета клиента для нового приложения на сайте Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Введите `https://login.microsoftonline.com` в поле **Разрешенные источники JavaScript** и `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` — в поле **Разрешенные URL-адреса возврата**. Замените **{tenant}** именем своего клиента (например, contoso.onmicrosoft.com). Выберите команду **Сохранить**. В значении **{клиент}** необходимо учитывать регистр.
   
    ![Предоставление источников JavaScript и URL-адресов возврата для нового приложения на сайте Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Настройка Amazon в качестве поставщика удостоверений для клиента
1. Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) на портале Azure.
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите Amzn.
5. Щелкните **Тип поставщика удостоверений**, выберите **Amazon** и нажмите кнопку **ОК**.
6. Щелкните **Set up this identity provider** (Настроить этот поставщик удостоверений), а затем введите идентификатор и секрет клиента ранее созданного приложения Amazon.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Amazon.

