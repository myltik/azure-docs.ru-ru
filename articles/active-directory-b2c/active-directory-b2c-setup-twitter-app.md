---
title: Конфигурация Twitter для Azure AD B2C | Документация Майкрософт
description: Обеспечение регистрации и входа для пользователей с учетными записями Twitter в приложениях, защищенных с помощью Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Обеспечение регистрации и входа для пользователей с учетными записями Twitter с помощью Azure AD B2C

## <a name="create-a-twitter-application"></a>Создание приложения Twitter
Чтобы использовать Twitter в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, нужно сначала создать приложение Twitter и задать в нем правильные параметры. Для этого потребуется учетная запись Twitter. Если у вас нет этой учетной записи, ее можно получить на сайте [https://twitter.com/signup](https://twitter.com/signup).

1. Перейдите на веб-сайт [Twitter Apps](https://apps.twitter.com/) и войдите с помощью своих учетных данных.
2. Щелкните **Создать новое приложение**. 
3. В форме укажите значения для полей **Name** (Имя), **Description** (Описание) и **Website** (Веб-сайт).
4. В поле **Callback URL** (URL-адрес обратного вызова) введите значение `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com).
5. Установите флажок, чтобы принять **соглашение с разработчиком** и нажмите кнопку **Create your Twitter application** (Создать приложение Twitter).
6. После создания приложения щелкните **Keys and Access Tokens** (Ключи и маркеры доступа).
7. Скопируйте значения **Consumer Key** (Ключ потребителя) и **Consumer Secret** (Секрет потребителя). Оба значения необходимы для настройки Twitter в качестве поставщика удостоверений в вашем клиенте.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Настройка Twitter в качестве поставщика удостоверений в клиенте
1. Войдите на [портал Azure](https://portal.azure.com/) как глобальный администратор клиента Azure AD B2C. 
2. Чтобы переключиться на клиент Azure AD B2C, выберите каталог Azure AD B2C в правом верхнем углу портала.
3. Щелкните **Все службы**, а затем выберите **Azure AD B2C** в списке служб в разделе **Безопасность и идентификация**.
4. Щелкните **Поставщики удостоверений**.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите Twitter.
5. Щелкните **Тип поставщика удостоверений**, выберите **Twitter (Preview)** (Twitter (предварительная версия)) и нажмите кнопку **ОК**.
6. Щелкните **Настроить этот поставщик удостоверений** и введите **ключ потребителя** в поле **Идентификатор клиента** и **секрет потребителя** Twitter в поле **Секрет клиента**.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Twitter.

## <a name="next-steps"></a>Дополнительная информация

Создайте или измените [встроенную политику](active-directory-b2c-reference-policies.md) и добавьте Twitter в качестве поставщика удостоверений.