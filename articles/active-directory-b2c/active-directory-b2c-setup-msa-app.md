---
title: 'Azure Active Directory B2C: настройка учетной записи Майкрософт | Документация Майкрософт'
description: Регистрация и вход пользователей с помощью учетных записей Майкрософт в приложениях, защищенных с помощью Azure Active Directory B2C.
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
ms.openlocfilehash: e167c8737bc600f52759f33456fec3bc6e0a3678
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory (AD) B2C: организация регистрации и входа для потребителей с учетными записями Microsoft
## <a name="create-a-microsoft-account-application"></a>Создание приложения для учетной записи Майкрософт
Чтобы использовать учетную запись Майкрософт в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение для учетной записи Майкрософт и задать в нем правильные параметры. Для этого требуется учетная запись Майкрософт. Если у вас нет этой учетной записи, ее можно получить на сайте [https://www.live.com/](https://www.live.com/).

1. Перейдите в [Центр регистрации приложений Майкрософт](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) и войдите с помощью своей учетной записи Майкрософт.
2. Нажмите кнопку **Добавить приложение**.
   
    ![Учетная запись Майкрософт: добавление нового приложения](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Укажите **имя** приложения и нажмите кнопку **Создать приложение**.
   
    ![Учетная запись Майкрософт: имя приложения](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Скопируйте значение **Идентификатор приложения**. Оно необходимо для настройки учетной записи Майкрософт в качестве поставщика удостоверений в вашем клиенте.
   
    ![Учетная запись Майкрософт: идентификатор приложения](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Щелкните **Добавить платформу** и выберите параметр **Веб**.
   
    ![Учетная запись Майкрософт: добавление платформы](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Учетная запись Майкрософт: веб](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Введите `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` в поле **URI перенаправления** . Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com).
   
    ![Учетная запись Майкрософт: URL-адрес перенаправления](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Щелкните **Создать новый пароль** в разделе **Секреты приложения**. Скопируйте новый пароль с экрана. Оно необходимо для настройки учетной записи Майкрософт в качестве поставщика удостоверений в вашем клиенте. Данный пароль — важный элемент обеспечения безопасности.
   
    ![Microsoft учетной записи: создание нового пароля](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Microsoft учетной записи: новый пароль](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Установите флажок **Поддержка Live SDK** в разделе **Дополнительные параметры**. Выберите команду **Сохранить**.
   
    ![Учетная запись Майкрософт: поддержка Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Настройка учетной записи Майкрософт в качестве поставщика удостоверений в клиенте
1. Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) на портале Azure.
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите "MSA".
5. Щелкните **Тип поставщика удостоверений**, выберите **Учетная запись Майкрософт** и нажмите кнопку **ОК**.
6. Щелкните **Настроить этот поставщик удостоверений** и введите идентификатор приложения и пароль ранее созданного приложения учетной записи Майкрософт.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию учетной записи Майкрософт.

