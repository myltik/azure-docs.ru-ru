---
title: 'Azure AD Connect: сквозная проверка подлинности — часто задаваемые вопросы | Документы Майкрософт'
description: Ответы на часто задаваемые вопросы о сквозной аутентификации Azure Active Directory.
services: active-directory
keywords: сквозная проверка подлинности azure ad connect, установка active directory, необходимые компоненты для azure ad, единый вход
documentationcenter: ''
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
ms.openlocfilehash: 7a817092581ef0be0b6be063282f7484c46ff094
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Сквозная проверка подлинности Azure Active: ответы на часто задаваемые вопросы

В этой статье приводятся ответы на часто задаваемые вопросы о сквозной аутентификации Azure Active Directory (Azure AD). Следите за обновлениями содержимого.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Какой из методов входа в Azure AD следует выбрать: сквозную аутентификацию, синхронизацию хэша паролей или службы федерации Active Directory (AD FS)?

Это зависит от вашей локальной среды и требований организации. Ознакомьтесь со статьей [Параметры входа в Azure AD Connect](active-directory-aadconnect-user-signin.md), в которой проводится сравнение различных методов входа Azure AD.

## <a name="is-pass-through-authentication-a-free-feature"></a>Функция сквозной проверки подлинности является бесплатной?

Функция сквозной аутентификации предоставляется бесплатно. Для ее использования не требуются платные выпуски Azure AD.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Доступна ли сквозная аутентификация в [Microsoft Azure — Германия](http://www.microsoft.de/cloud-deutschland) и [Azure для государственных организаций](https://azure.microsoft.com/features/gov/)?

Нет. Сквозная аутентификация доступна только в доступном по всему миру экземпляре Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Работает ли [условный доступ](../active-directory-conditional-access-azure-portal.md) со сквозной аутентификацией?

Да. Да, со сквозной аутентификацией работают все возможности условного доступа, включая Многофакторную идентификацию Azure.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Поддерживает ли сквозная проверка подлинности в качестве имени пользователя "Alternate ID", а не "userPrincipalName"?

Да. Сквозная аутентификация поддерживает `Alternate ID` в качестве имени пользователя, если это настроено в Azure AD Connect. Дополнительные сведения см. в статье [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Не все приложения Office 365 поддерживают `Alternate ID`. Ознакомьтесь с заявлением о поддержке в документации по конкретным приложениям.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Действует ли синхронизация хэша паролей как переход на резервный ресурс при сквозной аутентификации?

Нет. Нет, сквозная аутентификация _не_ выполняет автоматический переход на синхронизацию хэшей паролей. Она является резервным действием для [сценариев, не поддерживаемых сквозной проверкой подлинности](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Чтобы избежать ошибок входа пользователей, следует настроить [высокий уровень доступности](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) сквозной проверки подлинности.

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Можно ли установить соединитель [прокси приложения Azure AD](../manage-apps/application-proxy.md) на одном сервере с агентом сквозной проверки подлинности?

Да. Эта конфигурация поддерживается в переработанной версии агента сквозной аутентификации (версия 1.5.193.0 или более поздняя).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Какие версии Azure AD Connect и агента сквозной проверки подлинности необходимы?

Для работы этой функции требуется Azure AD Connect версии 1.1.486.0 или более поздней и агент сквозной аутентификации версии 1.5.58.0 или более поздней версии. Все программное обеспечение следует устанавливать на серверы Windows Server 2012 R2 или более поздней версии.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Что произойдет, если срок действия паролей истек и пользователи пытаются выполнить вход с использованием сквозной аутентификации?

Если вы настроили [обратную запись паролей](../active-directory-passwords-update-your-own-password.md) для конкретного пользователя, то когда пользователь будет выполнять вход с использованием сквозной аутентификации, он сможет изменить или сбросить пароль. Пароли будут записаны обратно в локальный каталог Active Directory, как ожидается.

Если обратная запись паролей не настроена для определенного пользователя или ему не назначена действительная лицензия Azure AD, то он не сможет обновить свой пароль в облаке. Он не сможет обносить свой пароль, даже если истек срок его действия. Пользователь увидит следующее сообщение: "Ваша организация запрещает обновлять пароль на этом сайте. Обновите пароль рекомендованным организацией способом или обратитесь за помощью к администратору". Пользователь или администратор должен сбросить свой пароль в локальной службе Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Каким образом сквозная аутентификация обеспечивает защиту от атак методом подбора пароля?

Чтобы получить дополнительные сведения, прочитайте раздел [Сквозная проверка подлинности Azure Active Directory: смарт-блокировка](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Какие данные передают агенты сквозной проверки подлинности через порты 80 и 443?

- Агенты аутентификации выполняют HTTPS-запросы через порт 443 для всех операций этой функции.
- Агенты аутентификации выполняют HTTP-запросы через порт 80 для скачивания списков отзыва SSL-сертификатов (CLR).

     >[!NOTE]
     >В последних обновлениях было сокращено количество портов, необходимых для работы этой функции. Если вы используете предыдущие версии Azure AD Connect или агента аутентификации, также не закрывайте следующие порты: 5671, 8080, 9090, 9091, 9350, 9352 и 10100–10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Могут ли агенты сквозной проверки подлинности взаимодействовать через сервер веб-прокси для исходящего трафика?

Да. Если в локальной среде включена служба WPAD (автоматическое обнаружение веб-прокси), агенты аутентификации автоматически пытаются обнаружить и использовать сервер веб-прокси в сети.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Можно ли установить два или более агентов сквозной проверки подлинности на одном сервере?

Нет, на одном сервере можно установить только один агент сквозной проверки подлинности. Если вы хотите настроить сквозную аутентификацию для обеспечения высокой доступности, следуйте инструкциям в разделе [Краткое руководство по сквозной проверке подлинности Azure Active Directory](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Ка удалить агент сквозной аутентификации?

Если агент сквозной аутентификации выполняется, он остается активным и постоянно обрабатывает запросы на вход пользователей. Если вы хотите удалить агент аутентификации, выберите **Панель управления > Программы > Программы и компоненты** и удалите программы **Microsoft Azure AD Connect Authentication Agent** и **Microsoft Azure AD Connect Agent Updater**.

Если открыть колонку "Сквозная проверка подлинности" в [центре администрирования Azure Active Directory](https://aad.portal.azure.com) после завершения предыдущего шага, то вы увидите, что агент аутентификации **неактивен**. Это _ожидаемое поведение_. Агент аутентификации будет автоматически удален из списка через несколько дней.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Я уже использую AD FS для входа в Azure AD. Как изменить этот метод на сквозную проверку подлинности?

Если вы настроили AD FS в качестве метода входа с помощью мастера Azure AD Connect, измените метод входа этого пользователя на сквозную аутентификацию. В этом случае в клиенте будет включена сквозная аутентификация, а _все_ федеративные домены будет преобразованы в управляемые домены. Все последующие запросы на вход в клиенте будут обрабатываться службой сквозной аутентификации. Сейчас в Azure AD Connect отсутствует поддержка использования сочетания AD FS и сквозной проверки подлинности в разных доменах.

Если вы настроили AD FS в качестве метода входа _без_ помощи мастера Azure AD Connect, измените метод входа пользователя на сквозную аутентификацию. Это можно сделать с помощью параметра **Не настраивать**. В этом случае в клиенте будет включена сквозная аутентификация, но все федеративные домены продолжат использовать AD FS для входа. С помощью PowerShell вручную преобразуйте часть (или все) этих федеративных доменов в управляемые домены. После внесения этого изменения *только* служба сквозной аутентификации будет обрабатывать все запросы на вход, поступающие на управляемые домены.

>[!IMPORTANT]
>Сквозная аутентификация не обрабатывает вход облачных пользователей Azure AD.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Можно ли использовать сквозную аутентификацию в среде с несколькими лесами Active Directory?

Да. Среды с несколькими лесами поддерживаются, если между лесами Active Directory существуют отношения доверия и правильно настроена маршрутизация по суффиксу имени.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Сколько агентов сквозной аутентификации следует установить?

Установка нескольких агентов сквозной аутентификации обеспечивает [высокий уровень доступности](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Но эта установка не поддерживает детерминированную балансировку нагрузки между агентами аутентификации.

Рассмотрим ожидаемую пиковую и среднюю нагрузку запросов на вход для клиента. В качестве измерения производительности один агент аутентификации может обрабатывать от 300 до 400 операций аутентификации в секунду на стандартном сервере с четырехъядерным ЦП и 16 ГБ ОЗУ.

Чтобы оценить объем трафика, используйте следующие рекомендации по выбору размеров:
- Размер полезных данных каждого запроса составляет (0.5K + 1K * число_агентов) байт, т. е. отправляемых данных из Azure AD в агент аутентификации. Здесь "число_агентов" указывает количество агентов аутентификации, зарегистрированных в клиенте.
- Размер полезных данных каждого ответа составляет 1 КБ, т. е. отправляемых данных из агента аутентификации в Azure AD.

Для большинства клиентов двух или трех агентов аутентификации будет достаточно, чтобы обеспечить высокий уровень доступности и емкость. Рекомендуется устанавливать агенты аутентификации как можно ближе к контроллерам домена, чтобы сократить задержку входа.

>[!NOTE]
>Существует системное ограничение в 12 агентов аутентификации для каждого клиента.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Можно ли установить первый агент сквозной проверки подлинности на сервере, отличном от того, на котором выполняется Azure AD Connect?

Нет, этот сценарий _не_ поддерживается.

## <a name="how-can-i-disable-pass-through-authentication"></a>Как можно отключить сквозную проверку подлинности?

Повторно запустите мастер Azure AD Connect и измените метод входа пользователя со сквозной проверки подлинности на другой метод. Это позволит отключить сквозную проверку подлинности на клиенте и удалить агент проверки подлинности с сервера. Необходимо вручную удалить агенты аутентификации с других серверов.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Что происходит при удалении агента сквозной проверки подлинности?

Если удалить агент сквозной аутентификации с сервера, то этот сервер прекратит принимать запросы на вход. Чтобы избежать нарушения функции входа пользователей в клиенте, перед удалением агента сквозной аутентификации убедитесь, что запущен другой агент аутентификации.

## <a name="next-steps"></a>Дополнительная информация
- [Текущие ограничения](active-directory-aadconnect-pass-through-authentication-current-limitations.md). Сведения о том, какие сценарии поддерживаются, а какие нет.
- [Краткое руководство по сквозной аутентификации Azure Active Directory](active-directory-aadconnect-pass-through-authentication-quick-start.md). Настройка и подготовка к работе сквозной аутентификации Azure Active Directory.
- [Интеллектуальная блокировка](active-directory-aadconnect-pass-through-authentication-smart-lockout.md). Узнайте, как настроить возможность интеллектуальной блокировки в клиенте для защиты учетных записей пользователей.
- [Подробное техническое руководство](active-directory-aadconnect-pass-through-authentication-how-it-works.md). Поймите, как работает функция сквозной аутентификации.
- [Устранение неполадок](active-directory-aadconnect-troubleshoot-pass-through-authentication.md). Узнайте, как устранять распространенные проблемы со сквозной аутентификации.
- [Руководство по безопасности](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md). Получите дополнительные технические сведения о сквозной аутентификации.
- [Простой единый вход Azure Active Directory](active-directory-aadconnect-sso.md). Узнайте подробнее об этой дополнительной функции.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Оставить запрос на новые функции можно на форуме по Azure Active Directory.

