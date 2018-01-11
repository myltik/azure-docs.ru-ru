---
title: "Azure Active Directory B2C: настройка Twitter | Документы Майкрософт"
description: "Обеспечение регистрации и входа для пользователей с учетными записями Twitter в приложениях, защищенных с помощью Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 579a6841-9329-45b8-a351-da4315a6634e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/06/2017
ms.author: parakhj
ms.openlocfilehash: dde17c89c49223c75842f328f455c6637aa1915b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C: организация регистрации и входа для потребителей с учетными записями Twitter

> [!NOTE]
> Эта функция предоставляется в предварительной версии.
> 

## <a name="create-a-twitter-application"></a>Создание приложения Twitter
Чтобы использовать Twitter в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, нужно сначала создать приложение Twitter и задать в нем правильные параметры. Для этого потребуется учетная запись разработчика Twitter. Если у вас ее нет, вы можете создать такую учетную запись на сайте [https://dev.twitter.com/](https://dev.twitter.com/).

1. Перейдите на [веб-сайт разработчиков Twitter](https://dev.twitter.com/) и войдите с помощью своих учетных данных.
2. Щелкните **My apps** (Мои приложения) в разделе **Tools & Support** (Средства и поддержка), а затем выберите **Create New App** (Создать приложение). 
3. В форме укажите значения для полей **Name** (Имя), **Description** (Описание) и **Website** (Веб-сайт).
4. В поле **Callback URL** (URL-адрес обратного вызова) введите значение `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com).
5. Установите флажок, чтобы принять **соглашение с разработчиком** и нажмите кнопку **Create your Twitter application** (Создать приложение Twitter).
6. После создания приложения щелкните **Keys and Access Tokens** (Ключи и токены доступа).
7. Скопируйте значения **Consumer Key** (Ключ потребителя) и **Consumer Secret** (Секрет потребителя). Оба значения необходимы для настройки Twitter в качестве поставщика удостоверений в вашем клиенте.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Настройка Twitter в качестве поставщика удостоверений в клиенте
1. Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) на портале Azure.
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите Twitter.
5. Щелкните **Тип поставщика удостоверений**, выберите **Twitter** и нажмите кнопку **ОК**.
6. Щелкните **Настроить этот поставщик удостоверений** и введите **ключ потребителя** в поле **Идентификатор клиента** и **секрет потребителя** Twitter в поле **Секрет клиента**.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Twitter.

