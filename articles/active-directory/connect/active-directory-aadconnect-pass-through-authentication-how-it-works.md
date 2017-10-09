---
title: "Сквозная аутентификация в Azure AD Connect — как это работает? | Документация Майкрософт"
description: "В этой статье описывается процедура сквозной аутентификации Azure Active Directory."
services: active-directory
keywords: "сквозная аутентификация azure ad connect, установка active directory, необходимые компоненты для azure ad, единый вход"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 9507a718ba982eb8b3381a6ef70156f075729161
ms.contentlocale: ru-ru
ms.lasthandoff: 09/28/2017

---

# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Сквозная аутентификация Azure Active Directory — подробное техническое руководство
Приведенная ниже статья содержит обзор принципов работы сквозной аутентификации Azure AD.  Более подробные технические сведения и информацию о безопасности можно найти в разделе [**Руководство по безопасности сквозной аутентификации Azure Active Directory**](active-directory-aadconnect-pta-security-deep-dive.md).

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Как работает сквозная аутентификация Azure Active Directory?

Когда пользователь пытается войти в приложение, защищенное с помощью Azure Active Directory (Azure AD), и на клиенте включена сквозная аутентификация, выполняются следующие действия:

1. Пользователь пытается получить доступ к приложению (например, Outlook Web App — https://outlook.office365.com/owa/).
2. Если пользователь еще не выполнил вход, он перенаправляется на страницу входа в Azure AD.
3. Пользователь вводит имя пользователя и пароль на странице входа в Azure AD и нажимает кнопку "Вход".
4. Azure AD, получив запрос на вход, помещает имя пользователя и пароль (зашифрованный с помощью открытого ключа) в очередь.
5. Локальный агент сквозной аутентификации отправляет исходящий вызов в очередь и получает зашифрованный пароль, а также имя пользователя.
6. Агент расшифровывает пароль, используя свой закрытый ключ.
7. Затем агент проверяет имя пользователя и пароль в Active Directory, используя стандартные API-интерфейсы Windows (механизм, похожий на используемый службами федерации Active Directory). Именем пользователя может быть либо локальное имя пользователя по умолчанию (обычно это `userPrincipalName`), либо другой атрибут (известный как `Alternate ID`), настроенный в Azure AD Connect.
8. Локальный контроллер домена Active Directory (DC) анализирует запрос и возвращает агенту соответствующий ответ ("успешно", "ошибка", "срок действия пароля истек" или "пользователь заблокирован").
9. Агент, в свою очередь, возвращает этот ответ в Azure AD.
10. Azure AD анализирует ответ и соответствующим образом отвечает пользователю — например, немедленно выполняет ввод пользователя в систему или запрашивает многофакторную идентификацию (MFA).
11. При успешном входе в систему пользователь может получить доступ к приложению.

На схеме ниже показаны все соответствующие компоненты и шаги.

![Сквозная проверка подлинности](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Дальнейшие действия
- [**Текущие ограничения**](active-directory-aadconnect-pass-through-authentication-current-limitations.md). Сведения о том, какие сценарии поддерживаются, а какие нет.
- [**Краткое руководство по сквозной проверке подлинности Azure Active Directory**](active-directory-aadconnect-pass-through-authentication-quick-start.md). Настройка и подготовка к работе сквозной проверки подлинности Azure Active Directory.
- [**Часто задаваемые вопросы**](active-directory-aadconnect-pass-through-authentication-faq.md). Ответы на часто задаваемые вопросы.
- [**Устранение неполадок**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**Простой единый вход Azure Active Directory**](active-directory-aadconnect-sso.md). Дополнительные сведения об этой дополнительной функции.
- [**Руководство по безопасности**](active-directory-aadconnect-pta-security-deep-dive.md). Дополнительные технические сведения о сквозной аутентификации Azure AD.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.

