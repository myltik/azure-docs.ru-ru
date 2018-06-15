---
title: Сквозная аутентификация Azure AD Connect — как это работает? | Документация Майкрософт
description: В этой статье описывается процедура сквозной аутентификации Azure Active Directory.
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
ms.date: 01/24/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ca501bb3ad37353f00ffe5d46f72822c7c5487bf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591401"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Сквозная аутентификация Azure Active Directory — подробное техническое руководство
Эта статья содержит обзор принципов работы сквозной аутентификации Azure Active Directory (Azure AD). Подробные технические сведения и информацию о безопасности см. в [руководстве по безопасности](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md).

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Как работает сквозная аутентификация Azure Active Directory?

Когда пользователь пытается войти в приложение, защищенное с помощью Azure AD, и на клиенте включена сквозная аутентификация, выполняются следующие действия.

1. Пользователь пытается получить доступ к приложению, например [Outlook Web App](https://outlook.office365.com/owa/).
2. Если пользователь еще не выполнил вход, он перенаправляется на страницу **Вход пользователя** Azure AD.
3. Пользователь вводит имя пользователя и пароль на странице входа Azure AD и нажимает кнопку **Вход**.
4. Служба Azure AD, получив запрос на вход, помещает имя пользователя и пароль (зашифрованный с помощью открытого ключа) в очередь.
5. Локальный агент проверки подлинности получает имя пользователя и зашифрованный пароль из очереди. Обратите внимание, что агент не часто опрашивает запросы из очереди, но извлекает запросы через готовое постоянное подключение.
6. Агент расшифровывает пароль, используя свой закрытый ключ.
7. Затем агент проверяет имя пользователя и пароль в Active Directory, используя стандартные интерфейсы API Windows (механизм, похожий на используемый службами федерации Active Directory (AD FS)). Именем пользователя может быть либо локальное имя пользователя по умолчанию (обычно это `userPrincipalName`), либо другой атрибут (известный как `Alternate ID`), настроенный в Azure AD Connect.
8. Локальный контроллер домена Active Directory анализирует запрос и возвращает агенту соответствующий ответ ("успешно", "ошибка", "срок действия пароля истек" или "пользователь заблокирован").
9. Агент проверки подлинности, в свою очередь, возвращает этот ответ в Azure AD.
10. Azure AD оценивает этот ответ и соответствующим образом отвечает пользователю. Например, Azure AD либо сразу же выполняет вход пользователя, либо запрашивает Многофакторную идентификацию Azure.
11. После успешного входа в систему пользователь может получить доступ к приложению.

На схеме ниже показаны все соответствующие компоненты и шаги.

![Сквозная проверка подлинности](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Дополнительная информация
- [Текущие ограничения](active-directory-aadconnect-pass-through-authentication-current-limitations.md). Сведения о том, какие сценарии поддерживаются, а какие нет.
- [Краткое руководство по сквозной аутентификации Azure Active Directory](active-directory-aadconnect-pass-through-authentication-quick-start.md). Настройка и подготовка к работе сквозной аутентификации Azure Active Directory.
- [Интеллектуальная блокировка](active-directory-aadconnect-pass-through-authentication-smart-lockout.md). Настройте возможность интеллектуальной блокировки на клиенте для защиты учетных записей пользователей.
- [Часто задаваемые вопросы](active-directory-aadconnect-pass-through-authentication-faq.md). Найдите ответы на часто задаваемые вопросы.
- [Устранение неполадок](active-directory-aadconnect-troubleshoot-pass-through-authentication.md). Узнайте, как устранять распространенные проблемы со сквозной аутентификации.
- [Руководство по безопасности](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md). Получите дополнительные технические сведения о сквозной аутентификации.
- [Простой единый вход Azure Active Directory](active-directory-aadconnect-sso.md). Узнайте подробнее об этой дополнительной функции.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Оставить запрос на новые функции можно на форуме по Azure Active Directory.

