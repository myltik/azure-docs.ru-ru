---
title: "Azure AD Connect: простой единый вход — часто задаваемые вопросы | Документы Майкрософт"
description: "Ответы на часто задаваемые вопросы о простом едином входе Azure Active Directory."
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
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: e363ade43ab9e2b2432c9efdc3ce1b661e278b2a
ms.contentlocale: ru-ru
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Часто задаваемые вопросы о простом едином входе Azure Active Directory

В этой статье рассматриваются часто задаваемые вопросы о простом едином входе Azure AD. Следите за новым содержимым.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>С какими методами входа работает простой единый вход?

Простой единый вход можно использовать вместе с методами [синхронизация хэша паролей](active-directory-aadconnectsync-implement-password-synchronization.md) или [сквозной аутентификации](active-directory-aadconnect-pass-through-authentication.md), но не со службами федерации Active Directory (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>Простой единый вход — это бесплатная функция?

Это бесплатный компонент, и для его использования не требуются платные выпуски Azure AD. Он останется бесплатным после выхода общедоступной версии компонента.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Какие приложения используют возможность параметра `domain_hint` или `login_hint` простого единого входа?

В настоящее время мы готовим список приложений, которые отправляют эти параметры, а также приложений, которые их не отправляют. Если вас интересуют конкретные приложения, свяжитесь с нами в разделе "Примечания".

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Поддерживает ли простой единый вход `Alternate ID` в качестве имени пользователя, а не `userPrincipalName`?

Да. Поддерживает ли простой единый вход `Alternate ID` в качестве имени пользователя, если это настроено в Azure AD Connect, как показано [здесь](active-directory-aadconnect-get-started-custom.md). Не все приложения Office 365 поддерживают `Alternate ID`. Заявление о поддержке см. в документации на приложения.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Я хочу зарегистрировать устройства, на которых не применяется Windows 10, с помощью Azure AD, без использования AD FS. Можно вместо этого использовать простой единый вход?

Да, для этого сценария требуется версия 2.1 или более поздняя версия [клиента присоединения к рабочей области](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-disable-seamless-sso"></a>Как отключить простой единый вход?

Простой единый вход можно отключить с помощью Azure AD Connect.

Запустите Azure AD Connect, выберите "Смена имени пользователя для входа" и щелкните "Далее". Затем снимите флажок "Включить единый вход". Продолжайте выполнять указания мастера. После завершения работы мастера на вашем клиенте будет отключен простой единый вход.

Тем не менее вы увидите на экране следующее сообщение:

"Единый вход теперь отключен, но для полной очистки нужен ряд ручных действий. Подробнее".

Ниже приведены действия, которые нужно выполнить вручную.

1. Получение списка лесов AD, где включен простой единый вход
- Для начала скачайте и установите [помощник по входу в Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
- Затем скачайте и установите [64-разрядный модуль Azure Active Directory для Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
- Перейдите в папку `%programfiles%\Microsoft Azure Active Directory Connect`.
- Импортируйте модуль PowerShell для простого единого входа с помощью следующей команды: `Import-Module .\AzureADSSO.psd1`.
  - В PowerShell вызовите `New-AzureADSSOAuthenticationContext`. Появится всплывающее окно для ввода учетных данных администратора клиента Azure AD.
  - Вызовите `Get-AzureADSSOStatus`. Эта команда выводит список лесов AD (см. список "Домены"), в которых включена эта функция.
2. Вручную удалите учетную запись компьютера `AZUREADSSOACCT` из каждого леса AD в списке, упомянутом выше.

## <a name="next-steps"></a>Дальнейшие действия

- [**Краткое руководство**](active-directory-aadconnect-sso-quick-start.md). Настройка и подготовка к работе простого единого входа Azure AD.
- [**Техническое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-sso-how-it-works.md). Сведения о том, как работает эта функция.
- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-sso.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.

