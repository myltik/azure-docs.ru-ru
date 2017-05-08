---
title: "Azure Active Directory B2C: настройка Amazon | Документация Майкрософт"
description: "Обеспечение регистрации и входа для пользователей с учетными записями Amazon в приложениях, защищенных с помощью Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 77c099bb-a005-4d75-87f9-f61e3de48725
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: e37c48d6c92a8a2cd480458abdff0a3a1ca9338f
ms.openlocfilehash: f3154968710384e3f6186b277c4cae33b25c77d2
ms.lasthandoff: 05/05/2017


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: регистрация и вход пользователей с учетными записями Amazon
## <a name="create-an-amazon-application"></a>Создание приложения Amazon
Чтобы использовать Amazon в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение Amazon и задать в нем правильные параметры. Для этого потребуется учетная запись Amazon. Если у вас ее нет, зарегистрируйте ее на сайте [http://www.amazon.com/](http://www.amazon.com/).

1. Перейдите на сайт [Amazon Developer Center](https://login.amazon.com/) войдите с помощью учетной записи Amazon.
2. Если это еще не сделано, нажмите кнопку **Sign Up**(Регистрация), выполните действия для регистрации разработчика и примите условия политики.
3. Нажмите кнопку **Register new application**(Зарегистрировать новое приложение).
   
    ![Регистрация нового приложения на веб-сайте Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Укажите информацию о приложении (**имя**, **описание** и **URL-адрес для уведомления о конфиденциальности**) и нажмите кнопку **Сохранить**.
   
    ![Предоставление информации для регистрации нового приложения на сайте Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. В разделе **Веб-параметры** скопируйте значения **Идентификатор клиента** и **Секрет клиента**. Для отображения этих параметров необходимо нажать кнопку **Показать секрет**. Оба значения необходимы для настройки Amazon в качестве поставщика удостоверений для вашего клиента. Щелкните **Изменить** в нижней части раздела. **Секрет клиента** — это важные учетные данные безопасности.
   
    ![Предоставление идентификатора клиента и секрета клиента для нового приложения на сайте Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Введите `https://login.microsoftonline.com` в поле **Разрешенные источники JavaScript** и `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` — в поле **Разрешенные URL-адреса возврата**. Замените **{tenant}** именем своего клиента (например, contoso.onmicrosoft.com). Щелкните **Сохранить**. В значении **{клиент}** необходимо учитывать регистр.
   
    ![Предоставление источников JavaScript и URL-адресов возврата для нового приложения на сайте Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Настройка Amazon в качестве поставщика удостоверений для клиента
1. Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) на портале Azure.
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите Amzn.
5. Щелкните **Тип поставщика удостоверений**, выберите **Amazon** и нажмите кнопку **ОК**.
6. Щелкните **Set up this identity provider** (Настроить этот поставщик удостоверений), а затем введите идентификатор и секрет клиента ранее созданного приложения Amazon.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Amazon.


