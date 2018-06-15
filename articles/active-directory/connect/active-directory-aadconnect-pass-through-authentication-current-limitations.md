---
title: Текущие ограничения сквозной проверки подлинности Azure AD Connect | Документация Майкрософт
description: Эта статья содержит сведения о текущих ограничениях сквозной аутентификации Azure Active Directory (Azure AD).
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
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ebfbb972d567963b6c302b7e6151f73165c4a87b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590680"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Текущие ограничения сквозной проверки подлинности Azure Active Directory

>[!IMPORTANT]
>Функция сквозной аутентификации Azure Active Directory (Azure AD) является бесплатной, и для ее использования не требуются платные выпуски Azure AD. Сквозная аутентификация доступна только в доступном по всему миру экземпляре Azure AD, но не в [облаке Microsoft Azure — Германия](http://www.microsoft.de/cloud-deutschland) или [облаке Microsoft Azure для государственных организаций](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

Следующие сценарии полностью поддерживаются:

- Вход пользователей во все браузерные приложения.
- Вход пользователей в приложения Office, поддерживающие [современную аутентификацию](https://aka.ms/modernauthga), — Office 2016 и Office 2013 _с_ современной аутентификацией.
- Вход пользователей в клиенты Outlook с помощью устаревших протоколов, таких как Exchange ActiveSync, SMTP, POP и IMAP.
- Вход пользователей в Skype для бизнеса с поддержкой современной аутентификации, включая онлайновую и гибридную топологии. Дополнительные сведения о поддерживаемых топологиях см. [здесь](https://technet.microsoft.com/library/mt803262.aspx).
- Присоединение устройств Windows 10 к доменам Azure AD.
- Добавление паролей для Многофакторной идентификации.

## <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

Следующие сценарии _не_ поддерживаются:

- Вход пользователей в устаревшие клиентские приложения Office, за исключением Outlook (см. **поддерживаемые сценарии** выше), — Office 2010 и Office 2013 _без_ современной аутентификации. Организациям рекомендуется перейти на современные способы аутентификации, если это возможно. Современная аутентификация обеспечивает поддержку сквозной аутентификации. Он также позволяет лучше защитить учетные записи пользователей с помощью функций [условного доступа](../active-directory-conditional-access-azure-portal.md), таких как Многофакторная идентификация Azure.
- Общий доступ к календарю и получение сведений о доступности в гибридных средах Exchange поддерживается только для Office 2010.
- Вход пользователей в клиентские приложения Skype для бизнеса _без_ современной аутентификации.
- Вход пользователей в PowerShell версии 1.0. Рекомендуется использовать PowerShell версии 2.0.
- Определение пользователей с [утерянными учетными данными](../active-directory-reporting-risk-events.md#leaked-credentials).
- Для использования доменных служб Azure AD в клиенте нужно включить синхронизацию хэшей паролей. Поэтому клиенты, использующие _только_ сквозную аутентификацию, не поддерживаются для сценариев, в которых требуются доменные службы Azure AD.
- Сквозная аутентификация не интегрирована с [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- Программа регистрации устройств Apple (Apple DEP)с использованием помощника по настройке iOS не поддерживает современную аутентификацию. Устройства Apple DEP нельзя зарегистрировать в Intune для использования управляемых доменов с помощью сквозной аутентификации. Рекомендуем в качестве альтернативы использовать [приложение корпоративного портала](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/).

>[!IMPORTANT]
>_Исключительно_ в качестве временного решения для неподдерживаемых сценариев включите синхронизацию хэша паролей на странице [Дополнительные возможности](active-directory-aadconnect-get-started-custom.md#optional-features) в мастере Azure AD Connect. Когда пользователи входят в приложения, которые перечислены в разделе "Неподдерживаемые сценарии", эти запросы на вход _не_ обрабатываются агентами сквозной проверки подлинности. Следовательно такие запросы не записываются в [журналы сквозной проверки подлинности](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

>[!NOTE]
Включение синхронизации хэшей паролей позволяет выполнять отработку отказа для аутентификации при полном сбое локальной инфраструктуры. Отработка отказа сквозной аутентификации с переходом на синхронизацию хэша паролей Active Directory не выполняется автоматически. Необходимо вручную переключить метод входа с помощью Azure AD Connect. Если сервер под управлением Azure AD Connect вышел из строя, потребуется помощь службы поддержки Майкрософт, чтобы отключить сквозную аутентификацию.

## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство](active-directory-aadconnect-pass-through-authentication-quick-start.md). Настройка и подготовка к работе сквозной аутентификации Azure Active Directory.
- [Интеллектуальная блокировка](active-directory-aadconnect-pass-through-authentication-smart-lockout.md). Узнайте, как настроить возможность интеллектуальной блокировки в клиенте для защиты учетных записей пользователей.
- [Подробное техническое руководство](active-directory-aadconnect-pass-through-authentication-how-it-works.md). Поймите, как работает функция сквозной аутентификации.
- [Часто задаваемые вопросы](active-directory-aadconnect-pass-through-authentication-faq.md). Найдите ответы на часто задаваемые вопросы о сквозной аутентификации.
- [Устранение неполадок](active-directory-aadconnect-troubleshoot-pass-through-authentication.md). Узнайте, как устранять распространенные проблемы со сквозной аутентификации.
- [Руководство по безопасности](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md). Получите дополнительные технические сведения о сквозной аутентификации.
- [Простой единый вход Azure Active Directory](active-directory-aadconnect-sso.md). Узнайте подробнее об этой дополнительной функции.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Оставить запрос на новые функции можно на форуме по Azure Active Directory.
