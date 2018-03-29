---
title: 'Конфигурация поставщика удостоверений GitHub: Azure AD B2C | Документация Майкрософт'
description: Обеспечение регистрации и входа для клиентов с учетными записями GitHub в приложениях, защищенных с помощью Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.openlocfilehash: 17ec4ebd8406f56c431666340ca8834c5ccf9670
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C: организация регистрации и входа для потребителей с учетными записями GitHub

> [!NOTE]
> Эта функция предоставляется в предварительной версии.
> 

В этой статье показано, как включить вход для пользователей с учетной записью GitHub.

## <a name="create-a-github-oauth-application"></a>Создание приложения OAuth GitHub

Чтобы использовать GitHub в качестве поставщика удостоверений в Azure AD B2C, необходимо сначала создать приложение GitHub OAuth и задать в нем правильные параметры.

1. После входа в GitHub выберите [GitHub Developer settings](https://github.com/settings/developers) (Параметры разработчика GitHub).
1. Щелкните **New OAuth App** (Создать приложение OAuth).
1. Заполните поля **Application name** (Имя приложения) и **Homepage URL** (URL-адрес домашней страницы).
1. В поле **Authorization callback URL** (URL-адрес обратного вызова авторизации) введите `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Замените **{tenant}** именем своего клиента Azure AD B2C (например, contosob2c.onmicrosoft.com).

    >[!NOTE]
    >Значение "tenant" в поле **Sign-on URL** (URL-адрес входа) следует указывать в нижнем регистре.

1. Щелкните **Register application** (Зарегистрировать приложение).
1. Сохраните значения **Client ID** (Идентификатор клиента) и **Client Secret** (Секрет клиента). Они понадобятся в следующем разделе.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Настройка GitHub в качестве поставщика удостоверений в клиенте Azure AD B2C

1. Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) на портале Azure.
1. В колонке функций B2C щелкните **Поставщики удостоверений**.
1. Нажмите **+Добавить** в верхней части колонки.
1. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите "GitHub".
1. Щелкните **Тип поставщика удостоверений**, выберите **GitHub** и нажмите кнопку **ОК**.
1. Щелкните **Настроить этот поставщик удостоверений** и введите скопированные ранее идентификатор клиента и секрет клиента для приложения OAuth GitHub.
1. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию GitHub.

## <a name="next-steps"></a>Дополнительная информация

Создайте или измените [встроенную политику](active-directory-b2c-reference-policies.md) и добавьте GitHub в качестве поставщика удостоверений.