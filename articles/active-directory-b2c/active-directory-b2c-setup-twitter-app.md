---
title: 'Azure Active Directory B2C: настройка Twitter | Документы Майкрософт'
description: Обеспечение регистрации и входа для пользователей с учетными записями Twitter в приложениях, защищенных с помощью Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/06/2017
ms.author: davidmu
ms.openlocfilehash: ee2d82f8c90b88a898428973a1febaa21034a14f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C: организация регистрации и входа для потребителей с учетными записями Twitter

## <a name="create-a-twitter-application"></a>Создание приложения Twitter
Чтобы использовать Twitter в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, нужно сначала создать приложение Twitter и задать в нем правильные параметры. Для этого потребуется учетная запись разработчика Twitter. Если у вас нет этой учетной записи, ее можно получить на сайте [https://dev.twitter.com/](https://dev.twitter.com/).

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

## <a name="next-steps"></a>Дополнительная информация

Создайте или измените [встроенную политику](active-directory-b2c-reference-policies.md) и добавьте Twitter в качестве поставщика удостоверений.