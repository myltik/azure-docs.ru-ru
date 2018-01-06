---
title: "Azure AD Connect: простой единый вход — часто задаваемые вопросы | Документы Майкрософт"
description: "Ответы на часто задаваемые вопросы о простом едином входе Azure Active Directory."
services: active-directory
keywords: "что такое Azure AD Connect, установка Active Directory, необходимые компоненты для Azure AD, единый вход"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: billmath
ms.openlocfilehash: bbaed9ee5db895810fac476ea5bf560800b0bdec
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Часто задаваемые вопросы о простом едином входе Azure Active Directory

В этой статье представлены часто задаваемые вопросы о простом едином входе Azure Active Directory. Следите за новым содержимым.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>С какими методами входа работает простой единый вход?

Простой единый вход можно использовать вместе с методами [синхронизация хэша паролей](active-directory-aadconnectsync-implement-password-synchronization.md) или [сквозной проверки подлинности](active-directory-aadconnect-pass-through-authentication.md). Однако эту функцию нельзя использовать со службами федерации Active Directory (AD FS).

## <a name="is-seamless-sso-a-free-feature"></a>Простой единый вход — это бесплатная функция?

Это бесплатный компонент, и для его использования не требуются платные выпуски Azure AD.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Доступен прозрачную единого входа в [облако Microsoft Azure Германия](http://www.microsoft.de/cloud-deutschland) и [облако Microsoft Azure для государственных](https://azure.microsoft.com/features/gov/)?

Нет. Эффективная SSO доступна только в глобальный экземпляр Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Какие приложения используют возможность параметра `domain_hint` или `login_hint` простого единого входа?

В настоящее время мы готовим список приложений, которые отправляют эти параметры, а также приложений, которые их не отправляют. Если у вас есть приложения, которые вас интересуют, свяжитесь с нами в разделе "Примечания".

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Поддерживает ли простой единый вход `Alternate ID` в качестве имени пользователя, а не `userPrincipalName`?

Да. Поддерживает ли простой единый вход `Alternate ID` в качестве имени пользователя, если это настроено в Azure AD Connect, как показано [здесь](active-directory-aadconnect-get-started-custom.md). Не все приложения Office 365 поддерживают `Alternate ID`. Заявление о поддержке см. в документации на приложения.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>То, что разница между единого входа предоставляется [присоединения Azure AD](../active-directory-azureadjoin-overview.md) и эффективная единого входа?

[Соединения Azure AD](../active-directory-azureadjoin-overview.md) обеспечивает единый вход для пользователей, если их устройства зарегистрированы в Azure AD. Эти устройства не должны входить в домен. SSO обеспечивается с помощью *токенов обновления основного* или *PRTs*, а не Kerberos. Взаимодействие с пользователем является наиболее оптимальным на устройствах Windows 10. Единый вход выполняется автоматически в браузере Edge. Она также работает Chrome с использованием расширение браузера.

Можно использовать присоединения Azure AD и эффективная единого входа для клиента. Эти две возможности дополняют друг друга. Если включены обе эти функции единого входа из присоединения Azure AD имеет приоритет над прозрачную единого входа.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Я хочу зарегистрировать устройства, на которых не применяется Windows 10, с помощью Azure AD, без использования AD FS. Можно вместо этого использовать простой единый вход?

Да, для этого сценария требуется версия 2.1 или более поздняя версия [клиента присоединения к рабочей области](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Как можно сменить ключ расшифровки Kerberos компьютерной учетной записи `AZUREADSSOACC`?

Очень важно часто менять ключ расшифровки Kerberos компьютерной учетной записи `AZUREADSSOACC` (представляющей Azure AD) в локальном лесу AD.

>[!IMPORTANT]
>Мы настоятельно рекомендуем, чтобы вы меняли ключ расшифровки Kerberos хотя бы раз в 30 дней.

Выполните следующие действия на локальном сервере, на котором выполняется Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Шаг 1. Получение списка лесов AD, где включен простой единый вход

1. Для начала скачайте и установите [помощник по входу в Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Затем скачайте и установите [64-разрядный модуль Azure Active Directory для Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Перейдите в папку `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Импортируйте модуль PowerShell для простого единого входа с помощью следующей команды: `Import-Module .\AzureADSSO.psd1`.
5. Откройте PowerShell от имени администратора. В PowerShell вызовите `New-AzureADSSOAuthenticationContext`. Появится всплывающее окно для ввода учетных данных глобального администратора клиента.
6. Вызовите `Get-AzureADSSOStatus`. Эта команда выводит список лесов AD (см. список "Домены"), в которых включена эта функция.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Шаг 2. Обновить ключ расшифровки Kerberos в каждом лесу AD, в котором он был настроен.

1. Вызовите `$creds = Get-Credential`. При запросе введите свои учетные данные администратора домена для нужного леса AD.
2. Вызовите `Update-AzureADSSOForest -OnPremCredentials $creds`. Эта команда обновляет ключ расшифровки Kerberos для компьютерной учетной записи `AZUREADSSOACC` в этом лесу AD и обновляет его в Azure AD.
3. Повторите предыдущие шаги для каждого леса AD, где настроена эта функция.

>[!IMPORTANT]
>_Не_ выполняйте команду `Update-AzureADSSOForest` более одного раза. В противном случае функция перестанет действовать, пока не истечет срок действия пользовательского билета Kerberos и пока локальный экземпляр Active Directory не выдаст новый билет.

## <a name="how-can-i-disable-seamless-sso"></a>Как отключить простой единый вход?

Простой единый вход можно отключить с помощью Azure AD Connect.

Запустите Azure AD Connect, выберите "Смена имени пользователя для входа" и щелкните "Далее". Затем снимите флажок "Включить единый вход". Продолжайте выполнять указания мастера. После завершения работы мастера на вашем клиенте будет отключен простой единый вход.

Тем не менее вы увидите на экране следующее сообщение:

"Единый вход теперь отключен, но для полной очистки нужен ряд ручных действий. Подробнее".

Чтобы завершить процесс, выполните действия в этом руководстве на локальном сервере с запущенным Azure AD Connect.

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Шаг 1. Получение списка лесов AD, где включен простой единый вход

1. Для начала скачайте и установите [помощник по входу в Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Затем скачайте и установите [64-разрядный модуль Azure Active Directory для Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Перейдите в папку `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Импортируйте модуль PowerShell для простого единого входа с помощью следующей команды: `Import-Module .\AzureADSSO.psd1`.
5. Откройте PowerShell от имени администратора. В PowerShell вызовите `New-AzureADSSOAuthenticationContext`. Появится всплывающее окно для ввода учетных данных глобального администратора клиента.
6. Вызовите `Get-AzureADSSOStatus`. Эта команда выводит список лесов AD (см. список "Домены"), в которых включена эта функция.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Шаг 2. Вручную удалите учетную запись компьютера `AZUREADSSOACCT` из каждого леса AD в списке, упомянутом выше.

## <a name="next-steps"></a>Дальнейшие действия

- [**Краткое руководство**](active-directory-aadconnect-sso-quick-start.md). Настройка и подготовка к работе простого единого входа Azure AD.
- [**Техническое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-sso-how-it-works.md). Сведения о том, как работает эта функция.
- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-sso.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.
