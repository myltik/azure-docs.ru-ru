---
title: 'Azure AD Connect: простой единый вход | Документы Майкрософт'
description: В этом разделе описывается простой единый вход Azure Active Directory (Azure AD) и то, как он обеспечивает действительно единый вход пользователей корпоративных компьютеров в корпоративной сети.
services: active-directory
keywords: что такое Azure AD Connect, установка Active Directory, необходимые компоненты для Azure AD, единый вход
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: 6a106ba59f09ffc20f8461c5267ae06695df4cd0
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Простой единый вход Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Что такое простой единый вход Azure Active Directory?

Простой единый вход Azure Active Directory автоматически обеспечивает пользователям вход в систему, когда они работают на корпоративных устройствах, подключенных к корпоративной сети. Если он включен, пользователям не нужно вводить пароль для входа в Azure AD, а в большинстве случаев — даже вводить имя пользователя. Эта функция предоставляет пользователям удобный доступ к облачным приложениям и не требует установки каких-либо дополнительных локальных компонентов.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Простой единый вход можно использовать вместе с методами [синхронизация хэша паролей](active-directory-aadconnectsync-implement-password-hash-synchronization.md) или [сквозной проверки подлинности](active-directory-aadconnect-pass-through-authentication.md).

![Простой единый вход](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>Простой единый вход _не_ применяется к службам федерации Active Directory (AD FS).

## <a name="key-benefits"></a>Основные преимущества

- *Удобство работы для пользователей*
  - Пользователи автоматически входят как в локальные, так и в облачные приложения.
  - Пользователям не нужно вводить пароль несколько раз.
- *Простота развертывания и администрирования*
  - Дополнительные локальные компоненты не требуются.
  - Эта функция работает с любым методом аутентификации в облаке: [синхронизацией хэша паролей](active-directory-aadconnectsync-implement-password-hash-synchronization.md) и [сквозной аутентификацией](active-directory-aadconnect-pass-through-authentication.md).
  - Ее можно развернуть для всех пользователей или их части с помощью групповой политики.
  - Регистрация устройств не под управлением Windows 10 в Azure AD без необходимости в какой-либо инфраструктуре AD FS. Для использования этой возможности требуется версия 2.1 или более поздняя версия [клиента подключения к рабочему месту](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Краткие сведения о возможностях

- Именем пользователя для входа может быть либо локальное имя пользователя по умолчанию (`userPrincipalName`), либо другой атрибут, настроенный в Azure AD Connect (`Alternate ID`). Оба варианта использования работают, так как служба простого единого входа использует утверждение `securityIdentifier` в билете Kerberos для поиска соответствующего объекта-пользователя в Azure AD.
- Простой единый вход — ситуативно-обусловленная функция. Если в ней происходит сбой, процедура входа выполняется стандартно, то есть пользователь, как и прежде, должен просто ввести пароль на странице входа.
- Если приложение (например, https://myapps.microsoft.com/contoso.com)) пересылает параметр `domain_hint` (OpenID Connect), параметр `whr` (SAML), определяющий клиент, или параметр `login_hint`, определяющий пользователя, в запросе на вход в Azure AD, то пользователи автоматически входят в систему, не вводя имена пользователей и пароли.
- Пользователи могут использовать автоматический единый вход, если приложение (например, https://contoso.sharepoint.com)) отправляет запросы на вход на клиентские конечные точки Azure AD, то есть https://login.microsoftonline.com/contoso.com/<..> или https://login.microsoftonline.com/<tenant_ID>/<..>, вместо обычной конечной точки Azure AD (https://login.microsoftonline.com/common/<...>).
- Поддерживается выход. Это позволяет пользователям выбрать другую учетную запись Azure AD для входа вместо того, чтобы автоматически входить с помощью простого единого входа.
- Клиенты Office 365 (версии 16.0.8730.xxxx и более поздних версий) поддерживаются с помощью неинтерактивной процедуры.
- Функцию можно включить с помощью Azure AD Connect.
- Это бесплатная функция, и для ее использования не требуются платные выпуски Azure AD.
- Она доступна в клиентах на основе веб-браузера и клиентах Office, поддерживающих [современную проверку подлинности](https://aka.ms/modernauthga) на платформах и в браузерах с поддержкой проверки подлинности Kerberos.

| Операционная система и браузер |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Yes|Нет |Yes|Да\*|Недоступно
|Windows 8.1|Yes|Недоступно|Yes|Да\*|Недоступно
|Windows 8|Yes|Недоступно|Yes|Да\*|Недоступно
|Windows 7|Yes|Недоступно|Yes|Да\*|Недоступно
|Mac OS X|Недоступно|Недоступно|Да\*|Да\*|Да\*

\*Требуется [дополнительная настройка](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Чтобы обеспечить максимальное удобство единого входа в Azure AD, мы рекомендуем использовать в Windows 10 функцию [присоединения к Azure AD](../active-directory-azureadjoin-overview.md).

## <a name="next-steps"></a>Дополнительная информация

- [**Краткое руководство**](active-directory-aadconnect-sso-quick-start.md). Настройка и подготовка к работе простого единого входа Azure AD.
- [**Техническое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-sso-how-it-works.md). Сведения о том, как работает эта функция.
- [**Часто задаваемые вопросы**](active-directory-aadconnect-sso-faq.md). Ответы на часто задаваемые вопросы.
- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-sso.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.
