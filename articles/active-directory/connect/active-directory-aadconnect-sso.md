---
title: "Azure AD Connect: простой единый вход | Документы Майкрософт"
description: "В этом разделе описывается простой единый вход Azure Active Directory (Azure AD) и то, как он обеспечивает действительно единый вход пользователей корпоративных компьютеров в корпоративной сети."
services: active-directory
keywords: "что такое Azure AD Connect, установка Active Directory, необходимые компоненты для Azure AD, единый вход"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: c4cd80c50dca5b97c36f1c9785d8ea347b35285c
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Простой единый вход Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Что такое простой единый вход Azure Active Directory?

Простой единый вход Azure Active Directory автоматически обеспечивает пользователям вход в систему, когда они работают на корпоративных устройствах, подключенных к корпоративной сети. Если он включен, пользователям не нужно вводить пароль для входа в Azure AD, а в большинстве случаев — даже вводить имя пользователя. Эта функция предоставляет пользователям удобный доступ к облачным приложениям и не требует установки каких-либо дополнительных локальных компонентов.

Простой единый вход можно использовать вместе с методами [синхронизация хэша паролей](active-directory-aadconnectsync-implement-password-synchronization.md) или [сквозной проверки подлинности](active-directory-aadconnect-pass-through-authentication.md).

![Простой единый вход](./media/active-directory-aadconnect-sso/sso1.png)

>[!NOTE]
>Эта функция _не_ применима к службам федерации Active Directory (AD FS), в которых она уже реализована.

## <a name="key-benefits"></a>Основные преимущества

- *Удобство работы для пользователей*
  - Пользователи автоматически входят как в локальные, так и в облачные приложения.
  - Пользователям не нужно вводить пароль несколько раз.
- *Простота развертывания и администрирования*
  - Дополнительные локальные компоненты не требуются.
  - Эта функция работает с любым методом аутентификации в облаке: [синхронизацией хэша паролей](active-directory-aadconnectsync-implement-password-synchronization.md) и [сквозной аутентификацией](active-directory-aadconnect-pass-through-authentication.md).
  - Ее можно развернуть для всех пользователей или их части с помощью групповой политики.
  - Регистрация устройств с ОС, отличной от Windows 10, в Azure AD. Для этого требуется версия 2.1 или более поздняя версия [клиента подключения к рабочему месту](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Краткие сведения о возможностях

- Именем пользователя для входа может быть либо локальное имя пользователя по умолчанию (`userPrincipalName`), либо другой атрибут, настроенный в Azure AD Connect (`Alternate ID`).
- Простой единый вход — ситуативно-обусловленная функция. Если в ней происходит сбой, процедура входа выполняется стандартно, то есть пользователь, как и прежде, должен просто ввести пароль на странице входа.
- Если приложение пересылает параметр `domain_hint` (определяющий клиента) или `login_hint` (определяющий пользователя) в запросе на вход Azure AD, пользователи автоматически входят в систему, не вводя имена пользователей и пароли.
- Функцию можно включить с помощью Azure AD Connect.
- Это бесплатная функция, и для ее использования не требуются платные выпуски Azure AD.
- Она доступна в клиентах на основе веб-браузера и клиентах Office, поддерживающих [современную проверку подлинности](https://aka.ms/modernauthga) на платформах и в браузерах с поддержкой проверки подлинности Kerberos.

| Операционная система и браузер |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Да|Да|Да|Да\*|Недоступно
|Windows 8.1|Да|Недоступно|Да|Да\*|Недоступно
|Windows 8|Да|Недоступно|Да|Да\*|Недоступно
|Windows 7|Да|Недоступно|Да|Да\*|Недоступно
|Mac OS X|Недоступно|Недоступно|Да\*|Да\*|Да\*

\*Требуется [дополнительная настройка](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Чтобы обеспечить максимальное удобство единого входа в Azure AD, мы рекомендуем использовать в Windows 10 функцию [присоединения к Azure AD](../active-directory-azureadjoin-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

- [**Краткое руководство**](active-directory-aadconnect-sso-quick-start.md). Настройка и подготовка к работе простого единого входа Azure AD.
- [**Техническое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-sso-how-it-works.md). Сведения о том, как работает эта функция.
- [**Часто задаваемые вопросы**](active-directory-aadconnect-sso-faq.md). Ответы на часто задаваемые вопросы.
- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-sso.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.

