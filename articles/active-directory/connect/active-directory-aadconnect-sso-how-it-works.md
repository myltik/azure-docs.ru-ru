---
title: "Azure AD Connect: простой единый вход — как это работает | Документы Майкрософт"
description: "В этой статье описывается принцип работы функции простого единого входа в Azure Active Directory."
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
ms.date: 07/24/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 1f12a8e3e11bc7eee1fc536e231f71c1d17c3fe4
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Подробное техническое руководство по простому единому входу Azure Active Directory

В этой статье приводятся технические сведения о работе функции простого единого входа Azure Active Directory.

## <a name="how-does-seamless-sso-work"></a>Как работает простой единый вход?

Этот раздел состоит из двух частей.
1. Настройка функции простого единого входа.
2. Как транзакция единого входа пользователя работает с простым единым входом.

### <a name="how-does-set-up-work"></a>Как выполняется настройка?

Простой единый вход включается с помощью Azure AD Connect, как показано [здесь](active-directory-aadconnect-sso-quick-start.md). При включении функции выполняются следующие действия:
- В локальной службе Active Directory (AD) создается учетная запись компьютера с именем `AZUREADSSOACCT` (представляет Azure AD).
- С помощью Azure AD безопасным образом предоставляется ключ расшифровки Kerberos учетной записи компьютера.
- Кроме того, создаются два имени субъектов-служб (SPN) Kerberos, представляющие URL-адреса, используемые при входе в Azure AD.

>[!NOTE]
> Учетная запись компьютера и имена участников-служб Kerberos создаются в каждом лесу AD, который синхронизируется с Azure AD (с помощью Azure AD Connect) и для пользователей которого должен иметься простой единый вход. Переместите учетную запись `AZUREADSSOACCT` компьютера в подразделение (OU), где хранятся другие учетные записи компьютеров, чтобы управлять им таким же образом и избежать удаления.

>[!IMPORTANT]
>Настоятельно рекомендуется, чтобы вы [меняли ключ расшифровки Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacct-computer-account) для учетной записи компьютера `AZUREADSSOACCT` хотя бы раз в 30 дней.

### <a name="how-does-sign-in-with-seamless-sso-work"></a>Как работает вход с функцией простого единого входа?

После завершения настройки функция простого входа в Azure AD работает так же, как любая другая функция входа, использующая встроенную проверку подлинности Windows (IWA). Процесс выглядит следующим образом:

1. Пользователь пытается получить доступ к приложению (например, Outlook Web App - https://outlook.office365.com/owa/) с корпоративного устройства, присоединенного к домену, в корпоративной сети.
2. Если пользователь еще не выполнил вход, он перенаправляется на страницу входа в Azure AD.

  >[!NOTE]
  >Если запрос на вход в Azure AD содержит параметр `domain_hint` (идентифицирует клиент, например contoso.onmicrosoft.com) или `login_hint` (определяет пользователя, например user@contoso.onmicrosoft.com или user@contoso.com), то шаг 2 пропускается.

3. Пользователь вводит свое имя на странице входа в Azure AD.
4. С помощью JavaScript, выполняемого в фоновом режиме, Azure AD запрашивает у браузера билет Kerberos (возвращается ответ 401 — не авторизовано).
5. В свою очередь браузер запрашивает в Active Directory билет для учетной записи компьютера `AZUREADSSOACCT` (которая представляет Azure AD).
6. Active Directory находит учетную запись компьютера и возвращает браузеру билет Kerberos, зашифрованный с использованием секрета этой учетной записи компьютера.
7. Браузер отправляет билет Kerberos, полученный из Active Directory, в Azure AD (по одному из [URL-адресов Azure AD, предварительно добавленных в настройки зоны интрасети браузера](active-directory-aadconnect-sso-quick-start.md#step-3-roll-out-the-feature)).
8. Azure AD расшифровывает билет Kerberos, который содержит удостоверение пользователя, выполнившего вход в корпоративное устройство, с помощью полученного ранее общего ключа.
9. После анализа Azure AD либо возвращает маркер приложению, либо предлагает пользователю выполнить дополнительную проверку, например многофакторную идентификацию.
10. При успешном входе в систему пользователь может получить доступ к приложению.

На схеме ниже показаны все соответствующие компоненты и шаги.

![Простой единый вход](./media/active-directory-aadconnect-sso/sso2.png)

Простой единый вход — ситуативно-обусловленная функция. Это означает, что если в ней происходит сбой, процедура входа выполняется стандартно, то есть пользователь, как и прежде, должен просто ввести пароль для входа.

## <a name="next-steps"></a>Дальнейшие действия

- [**Краткое руководство**](active-directory-aadconnect-sso-quick-start.md). Настройка и подготовка к работе простого единого входа Azure AD.
- [**Часто задаваемые вопросы**](active-directory-aadconnect-sso-faq.md). Ответы на часто задаваемые вопросы.
- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-sso.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.

