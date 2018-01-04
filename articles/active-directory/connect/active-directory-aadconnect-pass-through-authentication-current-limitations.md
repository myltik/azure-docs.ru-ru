---
title: "Текущие ограничения сквозной проверки подлинности Azure AD Connect | Документация Майкрософт"
description: "Эта статья содержит сведения о текущих ограничениях сквозной аутентификации Azure Active Directory (Azure AD)."
services: active-directory
keywords: "сквозная проверка подлинности azure ad connect, установка active directory, необходимые компоненты для azure ad, единый вход"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: billmath
ms.openlocfilehash: 98de47eab2636277acfd6393a7574ae18487bc6a
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Текущие ограничения сквозной проверки подлинности Azure Active Directory

>[!IMPORTANT]
>Функция сквозной аутентификации Azure Active Directory (Azure AD) является бесплатной, и для ее использования не требуются платные выпуски Azure AD. Сквозная аутентификация доступна только в доступном по всему миру экземпляре Azure AD, но не в [облаке Microsoft Azure — Германия](http://www.microsoft.de/cloud-deutschland) или [облаке Microsoft Azure для государственных организаций](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

Следующие сценарии полностью поддерживаются:

- Вход пользователей во все браузерные приложения.
- Вход пользователя в приложениях Office, которые поддерживают [современная проверка подлинности](https://aka.ms/modernauthga): Office 2016 и Office 2013 _с_ современная проверка подлинности
- Входе пользователя в Скайп для бизнеса, поддержка современную проверку подлинности, включая Online и гибридной топологии. Дополнительные сведения о поддерживаемых топологий [здесь](https://technet.microsoft.com/library/mt803262.aspx).
- Присоединение устройств Windows 10 к доменам Azure AD.
- Поддержка Exchange ActiveSync.

## <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

Следующие сценарии _не_ поддерживаются:

- Вход пользователей в устаревшие клиентские приложения Office — Office 2010 и Office 2013 _без_ современной аутентификации. Организациям рекомендуется перейти на современные способы аутентификации, если это возможно. Современная аутентификация обеспечивает поддержку сквозной аутентификации. Он также позволяет лучше защитить учетные записи пользователей с помощью функций [условного доступа](../active-directory-conditional-access-azure-portal.md), таких как Многофакторная идентификация Azure.
- Входе пользователя в Скайп для клиентских приложений бизнес- _без_ современную проверку подлинности.
- Вход пользователей в PowerShell версии 1.0. Рекомендуется использовать PowerShell версии 2.0.
- Добавление паролей для Многофакторной идентификации.
- Определение пользователей с [утерянными учетными данными](../active-directory-reporting-risk-events.md#leaked-credentials).
- Доменные службы Azure AD требуется синхронизация хэшей паролей включена на клиенте. Поэтому клиенты, использующие проверку подлинности к серверу _только_ не поддерживаются для сценариев, требующих доменные службы Azure AD.

>[!IMPORTANT]
>_Исключительно_ в качестве временного решения для неподдерживаемых сценариев включите синхронизацию хэша паролей на странице [Дополнительные возможности](active-directory-aadconnect-get-started-custom.md#optional-features) в мастере Azure AD Connect.

>[!NOTE]
Включение синхронизации хэшей паролей позволяет выполнять отработку отказа для аутентификации при полном сбое локальной инфраструктуры. Отработка отказа сквозной аутентификации с переходом на синхронизацию хэша паролей Active Directory не выполняется автоматически. Необходимо вручную переключить метод входа с помощью Azure AD Connect. Если сервер под управлением Azure AD Connect вышел из строя, потребуется помощь службы поддержки Майкрософт, чтобы отключить сквозную аутентификацию.

## <a name="next-steps"></a>Дальнейшие действия
- [Краткое руководство](active-directory-aadconnect-pass-through-authentication-quick-start.md). Настройка и подготовка к работе сквозной аутентификации Azure Active Directory.
- [Интеллектуальная блокировка](active-directory-aadconnect-pass-through-authentication-smart-lockout.md). Узнайте, как настроить возможность интеллектуальной блокировки в клиенте для защиты учетных записей пользователей.
- [Подробное техническое руководство](active-directory-aadconnect-pass-through-authentication-how-it-works.md). Поймите, как работает функция сквозной аутентификации.
- [Часто задаваемые вопросы](active-directory-aadconnect-pass-through-authentication-faq.md). Найдите ответы на часто задаваемые вопросы о сквозной аутентификации.
- [Устранение неполадок](active-directory-aadconnect-troubleshoot-pass-through-authentication.md). Узнайте, как устранять распространенные проблемы со сквозной аутентификации.
- [Руководство по безопасности](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md). Получите дополнительные технические сведения о сквозной аутентификации.
- [Простой единый вход Azure Active Directory](active-directory-aadconnect-sso.md). Узнайте подробнее об этой дополнительной функции.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Оставить запрос на новые функции можно на форуме по Azure Active Directory.

