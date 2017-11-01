---
title: "Azure AD Connect: сквозная проверка подлинности — часто задаваемые вопросы | Документы Майкрософт"
description: "Ответы на часто задаваемые вопросы о сквозной проверке подлинности Azure Active Directory."
services: active-directory
keywords: "Сквозная аутентификация Azure AD Connect, установка Active Directory, необходимые компоненты для Azure AD, единый вход"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: e1bd58797124210f7c31e90fb20d728289a04ba2
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Сквозная проверка подлинности Azure Active: ответы на часто задаваемые вопросы

В этой статье приводятся ответы на часто задаваемые вопросы о сквозной проверке подлинности Azure Active Directory (Azure AD). Следите за новым содержимым.

## <a name="which-of-the-azure-ad-sign-in-methods---pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs---should-i-choose"></a>Какой из методов входа Azure AD следует выбрать: сквозная проверка подлинности, синхронизация хэша паролей, службы федерации Active Directory (AD FS)?

Это зависит от вашей локальной среды и требований организации. Ознакомьтесь со статьей, где проводится [сравнение различных методов входа Azure AD](active-directory-aadconnect-user-signin.md).

## <a name="is-pass-through-authentication-a-free-feature"></a>Функция сквозной проверки подлинности является бесплатной?

Функция сквозной проверки подлинности является бесплатной, и для ее использования не требуются платные выпуски Azure AD.

## <a name="is-pass-through-authentication-available-in-microsoft-cloud-germanyhttpwwwmicrosoftdecloud-deutschland-and-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Доступна ли сквозная проверка подлинности в [Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) и [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/)?

Нет, сквозная проверка подлинности доступна только в мировом экземпляре Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-accessmd-work-with-pass-through-authentication"></a>Работает ли [условный доступ](../active-directory-conditional-access.md) со сквозной проверкой подлинности?

Да, со сквозной проверкой подлинности работают все возможности условного доступа, включая Многофакторную идентификацию Azure.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Поддерживает ли сквозная проверка подлинности в качестве имени пользователя "Alternate ID", а не "userPrincipalName"?

Да. Сквозная проверка подлинности поддерживает `Alternate ID` в качестве имени пользователя, если это настроено в Azure AD Connect, как показано [здесь](active-directory-aadconnect-get-started-custom.md). Не все приложения Office 365 поддерживают `Alternate ID`. Заявление о поддержке см. в документации к приложению.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Действует ли синхронизация хэша паролей как переход на резервный ресурс при сквозной проверке подлинности?

Нет, сквозная проверка подлинности _не_ переходит на синхронизацию хэшей паролей автоматически. Она является резервным действием для [сценариев, не поддерживаемых сквозной проверкой подлинности](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Чтобы избежать ошибок входа пользователей, следует настроить [высокий уровень доступности](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) сквозной проверки подлинности.

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Можно ли установить соединитель [прокси приложения Azure AD](../active-directory-application-proxy-get-started.md) на одном сервере с агентом сквозной проверки подлинности?

Да, эта конфигурация поддерживается в переработанной версии агента сквозной аутентификации (версия 1.5.193.0 или более поздняя).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Какие версии Azure AD Connect и агента сквозной проверки подлинности необходимы?

Для работы этой функции требуется Azure AD Connect версии 1.1.486.0 или более поздней и агент сквозной проверки подлинности версии 1.5.58.0 или более поздней. Все программное обеспечение следует устанавливать на серверы Windows Server 2012 R2 или более поздней версии.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-using-pass-through-authentication"></a>Что произойдет, если срок действия паролей истек и пользователи пытаются выполнить вход с использованием сквозной проверки подлинности?

Если вы настроили [обратную запись паролей](../active-directory-passwords-update-your-own-password.md) для конкретного пользователя, то когда пользователь будет выполнять вход с использованием сквозной проверки подлинности, он сможет изменить или сбросить пароль. Пароли будут записаны обратно в локальный каталог Active Directory, как ожидается.

Но если обратная запись паролей не настроена для определенного пользователя или пользователю не назначена действительная лицензия Azure AD, то он не сможет обновить свой пароль в облаке. Он не сможет обносить свой пароль, даже если истек срок его действия. Пользователь увидит следующее сообщение: "Ваша организация запрещает обновлять пароль на этом сайте. Обновите пароль рекомендованным организацией способом или обратитесь за помощью к администратору". Пользователь или администратор должен сбросить пароль в локальной службе Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Каким образом сквозная проверка подлинности обеспечивает защиту от атак методом подбора пароля?

Дополнительные сведения см. в [этой статье](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Какие данные передают агенты сквозной проверки подлинности через порты 80 и 443?

- Агенты аутентификации выполняют HTTPS-запросы через порт 443 для всех операций этой функции.
- Агенты проверки подлинности выполняют HTTP-запросы через порт 80 для скачивания списков отзыва SSL-сертификатов.

     >[!NOTE]
     >В последних обновлениях мы сократили количество портов, необходимых для работы этой функции. Если вы используете предыдущие версии Azure AD Connect или агента аутентификации, также не закрывайте следующие порты: 5671, 8080, 9090, 9091, 9350, 9352 и 10100–10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Могут ли агенты сквозной проверки подлинности взаимодействовать через сервер веб-прокси для исходящего трафика?

Да. Если в локальной среде включена служба WPAD (автоматическое обнаружение веб-прокси), агенты проверки подлинности автоматически пытаются обнаружить и использовать сервера веб-прокси в сети.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Можно ли установить два или более агентов сквозной проверки подлинности на одном сервере?

Нет, на одном сервере можно установить только один агент сквозной проверки подлинности. Если вы хотите настроить сквозную проверку подлинности для обеспечения высокой доступности, следуйте инструкциям этой [статьи](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="i-already-use-active-directory-federation-services-ad-fs-for-azure-ad-sign-in-how-do-i-switch-it-to-pass-through-authentication"></a>Я уже использую службы федерации Active Directory (AD FS) для входа в Azure AD. Как изменить этот метод на сквозную проверку подлинности?

Если вы настроили AD FS в качестве метода входа с помощью мастера Azure AD Connect, измените метод входа пользователей на сквозную проверку подлинности. В этом случае на клиенте будет включена сквозная проверка подлинности, а _все_ федеративные домены будет преобразованы в управляемые домены. Все последующие запросы на вход в клиенте будут обрабатываться сквозной проверкой подлинности. Сейчас в Azure AD Connect отсутствует поддержка использования сочетания AD FS и сквозной проверки подлинности в разных доменах.

Если службы AD FS были настроены в качестве метода входа _вне_ мастера Azure AD Connect, измените метод входа пользователей на сквозную проверку подлинности (используя параметр "Не настраивать"). После этого изменения на клиенте будет включена сквозная проверка подлинности. Однако все федеративные домены продолжат использовать AD FS для входа. С помощью PowerShell вручную преобразуйте некоторые или все эти федеративные домены в управляемые домены. После этого все запросы на вход на управляемых доменах (_только_) будут обрабатываться сквозной проверкой подлинности.

>[!IMPORTANT]
>Сквозная проверка подлинности не обрабатывает вход только для облачных пользователей Azure AD.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-ad-environment"></a>Можно ли использовать сквозную проверку подлинности в среде с несколькими лесами AD?

Да. Среды с несколькими лесами поддерживаются, если между лесами AD существуют отношения доверия и правильно настроена маршрутизация по суффиксу имени.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Сколько агентов сквозной аутентификации следует установить?

Установка нескольких агентов сквозной аутентификации обеспечивает [высокий уровень доступности](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Но эта установка не поддерживает детерминированную балансировку нагрузки между агентами проверки подлинности.

Рассмотрим ожидаемую пиковую и среднюю нагрузку запросов на вход для клиента. В качестве измерения производительности один агент аутентификации может обрабатывать от 300 000 до 400 000 операций аутентификации в секунду на стандартном сервере с четырехъядерным ЦП и 16 ГБ ОЗУ. Для большинства клиентов двух или трех агентов аутентификации будет достаточно, чтобы обеспечить высокий уровень доступности и емкость.

Рекомендуется устанавливать агенты проверки подлинности как можно ближе к контроллерам домена, чтобы сократить задержку входа.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Можно ли установить первый агент сквозной проверки подлинности на сервере, отличном от того, на котором выполняется Azure AD Connect?

Нет, этот сценарий _не_ поддерживается.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>Сколько агентов сквозной проверки подлинности следует устанавливать?

Примите во внимание следующие рекомендации.

- Общее количество установленных агентов проверки подлинности не должно превышать два или три экземпляра. Такой конфигурации будет достаточно для большинства клиентов.
- Цель установки агентов проверки подлинности на контроллерах домена (или как можно ближе к ним) заключается в сокращении задержки входа.
- Вы гарантируете, что серверы (на которых установлены агенты проверки подлинности) добавляются в тот же лес AD, что и пользователи, пароль которых необходимо проверить.

>[!NOTE]
>Существует системное ограничение в 12 агентов аутентификации для каждого клиента.

## <a name="how-can-i-disable-pass-through-authentication"></a>Как можно отключить сквозную проверку подлинности?

Повторно запустите мастер Azure AD Connect и измените метод входа пользователя со сквозной проверки подлинности на другой метод. Это позволит отключить сквозную проверку подлинности на клиенте и удалить агент проверки подлинности с сервера. Необходимо вручную удалить агенты проверки подлинности с других серверов.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Что происходит при удалении агента сквозной проверки подлинности?

Удаление агента сквозной проверки подлинности на сервере приводит к прекращению принятия запросов на вход. Перед выполнением этой операции убедитесь, что запущен другой агент проверки подлинности, чтобы избежать нарушения входа пользователей в клиент.

## <a name="next-steps"></a>Дальнейшие действия
- [**Текущие ограничения**](active-directory-aadconnect-pass-through-authentication-current-limitations.md). Сведения о том, какие сценарии поддерживаются, а какие нет.
- [**Краткое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-pass-through-authentication-quick-start.md). Настройка и подготовка к работе сквозной проверки подлинности Azure Active Directory.
- [**Интеллектуальная блокировка**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md). Настройте возможность интеллектуальной блокировки на клиенте для защиты учетных записей пользователей.
- [**Техническое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-pass-through-authentication-how-it-works.md). Сведения о том, как работает эта функция.
- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**Руководство по безопасности**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md). Дополнительные технические сведения об этом компоненте.
- [**Простой единый вход Azure Active Directory**](active-directory-aadconnect-sso.md). Дополнительные сведения об этой дополнительной функции.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.
