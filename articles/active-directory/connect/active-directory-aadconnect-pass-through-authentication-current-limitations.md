---
title: "Текущие ограничения сквозной проверки подлинности Azure AD Connect | Документация Майкрософт"
description: "Эта статья содержит сведения о текущих ограничениях сквозной проверки подлинности Azure Active Directory (Azure AD)."
services: active-directory
keywords: "сквозная проверка подлинности azure ad connect, установка active directory, необходимые компоненты для azure ad, единый вход"
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
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 3ed2ee8c996628caabefd60b5ecff7528402eb52
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Текущие ограничения сквозной проверки подлинности Azure Active Directory

>[!IMPORTANT]
>Сквозная проверка подлинности Azure AD доступна в предварительной версии. Это бесплатная функция, и для ее использования не требуются платные выпуски Azure AD. Сквозная проверка подлинности доступна только в общедоступном экземпляре Azure AD, а не в [Microsoft Cloud для Германии](http://www.microsoft.de/cloud-deutschland) и [облаке Microsoft Azure для государственных организаций](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

На этапе предварительной версии полностью поддерживаются следующие сценарии.

- Вход пользователей во все браузерные приложения.
- Вход пользователей в клиентские приложения Office 365, поддерживающие [современную проверку подлинности](https://aka.ms/modernauthga).

## <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

На этапе предварительной версии _не_ поддерживаются следующие сценарии.

- Вход пользователей в клиентские приложения Office прежних версий и Exchange ActiveSync (т. е. собственные почтовые приложения на мобильных устройствах). Организациям рекомендуется перейти на современные способы аутентификации, если это возможно. Современная проверка подлинности обеспечит поддержку сквозной проверки подлинности, а также поможет защитить удостоверения с помощью таких функций [условного доступа](../active-directory-conditional-access.md), как многофакторная проверка подлинности.
- Вход пользователей в клиентские приложения Skype для бизнеса.
- Вход пользователей в PowerShell версии 1.0. Вместо этого рекомендуется использовать PowerShell версии 2.0.
- Присоединение устройств Windows 10 к Azure AD.

>[!IMPORTANT]
>В качестве временного решения для неподдерживаемых сценариев включите синхронизацию хэша паролей на странице [Дополнительные возможности](active-directory-aadconnect-get-started-custom.md#optional-features) в мастере Azure AD Connect. Синхронизация хэша паролей используется в качестве запасного варианта _только_ для предыдущих сценариев (а _не_ в качестве универсального варианта для сквозной проверки подлинности). Если эти сценарии не требуются, отключите синхронизацию хэша паролей.

## <a name="next-steps"></a>Дальнейшие действия
- [**Краткое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-pass-through-authentication-quick-start.md). Настройка и подготовка к работе сквозной проверки подлинности Azure Active Directory.
- [**Azure Active Directory Pass-through Authentication: Technical deep dive**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) (Техническое руководство по сквозной проверке подлинности Azure Active Directory). Сведения о том, как работает эта функция.
- [**Azure Active Directory Pass-through Authentication: Frequently asked questions**](active-directory-aadconnect-pass-through-authentication-faq.md) (Часто задаваемые вопросы о сквозной проверке подлинности Azure Active Directory). Ответы на часто задаваемые вопросы.
- [**Как устранять неполадки в работе сквозной аутентификации Azure Active Directory.**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**Простой единый вход Azure Active Directory.**](active-directory-aadconnect-sso.md) Дополнительные сведения об этой дополнительной функции.
- [**UserVoice.**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Подача новых запросов функций.

