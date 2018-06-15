---
title: 'Azure AD Connect: сквозная аутентификация | Документация Майкрософт'
description: В этой статье описывается сквозная проверка подлинности Azure Active Directory (Azure AD) и ее применение для входа в Azure AD c помощью проверки пользовательских паролей в локальном каталоге Active Directory.
services: active-directory
keywords: Что такое сквозная аутентификация Azure AD Connect, установка Active Directory, необходимые компоненты для Azure AD, единый вход
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 37239a45cffe18478b8302ccf1ee95c8edd0423a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592166"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Вход пользователей с помощью сквозной проверки подлинности Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Что такое сквозная проверка подлинности Azure Active Directory?

Сквозная проверка подлинности Azure Active Directory (Azure AD) позволяет пользователям входить в локальные и облачные приложения, используя те же пароли. Эта функция облегчает работу пользователей, так как им не нужно запоминать на один пароль больше, и сокращает затраты на ИТ-поддержку, так как снижается вероятность того, что пользователи забудут процедуру входа. Если пользователи выполняют вход с помощью Azure AD, эта функция проверяет пароли пользователей непосредственно в локальной службе Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Сквозная проверка подлинности является альтернативой [синхронизации хэшей паролей Azure AD](active-directory-aadconnectsync-implement-password-hash-synchronization.md), которая также обеспечивает преимущество облачной проверки подлинности в организации. Однако политики безопасности и соответствия требованиям в некоторых организациях не позволяют отправлять пароли пользователей за пределы внутренней сети даже в хэшированной форме. Сквозная проверка подлинности является подходящим решением для таких организаций.

![Сквозная аутентификация Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Вы можете использовать сквозную проверку подлинности вместе с функцией [простого единого входа](active-directory-aadconnect-sso.md). Таким образом, когда пользователи обращаются к приложениям на корпоративных компьютерах, входящих в корпоративную сеть, им не нужно вводить пароль для входа.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Основные преимущества сквозной проверки подлинности Azure AD

- *Удобство работы для пользователей*
  - Пользователи применяют одни и те же пароли для входа в локальные и облачные приложения.
  - Пользователи тратят меньше времени на обращения в службу технической поддержки с целью разрешения проблем, связанных с паролями.
  - Пользователи могут выполнять задачи [самостоятельного управления паролями](../authentication/active-directory-passwords-overview.md) в облаке.
- *Простота развертывания и администрирования*
  - Не требуются сложные локальные развертывания или настройка сети.
  - В локальной среде необходимо установить лишь упрощенный клиент.
  - Отсутствие накладных расходов на управление. Агент автоматически получает усовершенствования и исправления ошибок.
- *Безопасность*
  - Локальные пароли ни в каком виде не хранятся в облаке.
  - Агент устанавливает только исходящие подключения из вашей сети. Таким образом, вам не нужно устанавливать агент в сети периметра.
  - Защита учетных записей пользователей благодаря взаимодействию с [политиками условного доступа Azure AD](../active-directory-conditional-access-azure-portal.md), включая Многофакторную идентификацию (MFA), и [фильтрации атак методом подбора пароля](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).
- *Высокая доступность*
  - Дополнительные агенты можно установить на нескольких локальных серверах, чтобы достичь высокого уровня доступности запросов на вход.

## <a name="feature-highlights"></a>Краткие сведения о возможностях

- Поддерживает вход пользователей во все браузерные приложения и клиентские приложения Microsoft Office, которые используют [современную проверку подлинности](https://aka.ms/modernauthga).
- Именем пользователя для входа может быть либо локальное имя пользователя по умолчанию (`userPrincipalName`), либо другой атрибут, настроенный в Azure AD Connect (известный как `Alternate ID`).
- Эта функция взаимодействует с функциями [условного доступа](../active-directory-conditional-access-azure-portal.md), такими как Многофакторная Идентификация (MFA), для защиты пользователей.
- Она интегрирована с [самостоятельным управлением паролями](../authentication/active-directory-passwords-overview.md) на основе облака, включая обратную запись паролей в локальный каталог Active Directory и защиту пароля с помощью запрета часто используемых паролей.
- Среды с несколькими лесами поддерживаются, если между лесами AD существуют отношения доверия и правильно настроена маршрутизация по суффиксу имени.
- Это бесплатная функция, и для ее использования не требуются платные выпуски Azure AD.
- Функцию можно включить с помощью [Azure AD Connect](active-directory-aadconnect.md).
- Она использует упрощенный локальный агент, который прослушивает запросы на проверку пароля и отвечает на них.
- Установка нескольких агентов обеспечивает высокий уровень доступности запросов на вход.
- Она [защищает](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) локальные учетные записи от атак методом подбора пароля в облаке.

## <a name="next-steps"></a>Дополнительная информация

- [**Краткое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-pass-through-authentication-quick-start.md). Настройка и подготовка к работе сквозной проверки подлинности Azure Active Directory.
- [**Интеллектуальная блокировка**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md). Настройте возможность интеллектуальной блокировки на клиенте для защиты учетных записей пользователей.
- [**Текущие ограничения**](active-directory-aadconnect-pass-through-authentication-current-limitations.md). Сведения о том, какие сценарии поддерживаются, а какие нет.
- [**Техническое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-pass-through-authentication-how-it-works.md). Сведения о том, как работает эта функция.
- [**Часто задаваемые вопросы**](active-directory-aadconnect-pass-through-authentication-faq.md). Ответы на часто задаваемые вопросы.
- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**Руководство по безопасности**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md). Дополнительные технические сведения об этом компоненте.
- [**Простой единый вход Azure Active Directory**](active-directory-aadconnect-sso.md). Дополнительные сведения об этой дополнительной функции.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.
