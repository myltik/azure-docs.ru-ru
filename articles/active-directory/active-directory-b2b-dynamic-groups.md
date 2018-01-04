---
title: "Динамические группы и служба совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Показано, как использовать динамические группы Azure AD с совместной работы Azure Active Directory B2B"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 12/14/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: d60b7f70332d6183a67003c1dceb96241fc6bae4
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Динамические группы и служба совместной работы Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>Что такое динамические группы?
Динамическая конфигурация членства в группе безопасности для Azure Active Directory (Azure AD) доступна на [портале Azure](https://portal.azure.com). Администратор может настроить правила для заполнения группы, созданные в Azure AD, на основе атрибутов пользователя (например, userType, отделу или страны). Участников можно автоматически добавлять в группу безопасности или удалять их из нее в зависимости от атрибутов. Эти группы могут предоставлять доступ к приложениям или облачным ресурсам, таким как сайты и документы SharePoint. Их также можно использовать для назначения лицензий участникам. Дополнительные сведения о динамических групп см. в статье [Выделенные группы в Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Для создания и использования динамических групп требуется соответствующее [лицензирование Azure AD Premium P1 или P2](https://azure.microsoft.com/pricing/details/active-directory/). Дополнительные сведения см. в статье [Создание правил на основе атрибутов для динамического членства в группах в Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Что такое встроенные динамические группы?
Динамическая группа **Все пользователи** дает администраторам клиента возможность одним щелчком создать группу, которая содержит всех пользователей в клиенте. По умолчанию группа **Все пользователи** включает в себя всех пользователей в каталоге, в том числе участников и гостей.
На новом портале администрирования Azure Active Directory в представлении "Параметры группы" можно включить группу **Все пользователи**.

![Показывает включение группы все пользователи, значение «Да»](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Усиление защиты динамической группы "Все пользователи"
По умолчанию группа **Все пользователи** также включает (гостевых) пользователей службы совместной работы B2B. Вы можете дополнительно обеспечить безопасность группы **Все пользователи**, используя правило для удаления гостевых пользователей. На следующем снимке экрана показана группа **Все пользователи**, измененная для исключения гостей.

![Правило показывает, где пользователь может ввести не равняется гостя](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

Вы можете также рассмотреть создание новой динамической группы, содержащей только гостевых пользователей, чтобы применить политики (например, политики условного доступа Azure AD).
Как такая группа может выглядеть:

![Правило показывает, где тип пользователя равно гостевой](media/active-directory-b2b-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Дальнейшие действия

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md)
* [Добавление пользователя службы совместной работы Azure Active Directory B2B в роль](active-directory-b2b-add-guest-to-role.md)
* [Делегирование приглашений для службы совместной работы Azure Active Directory B2B](active-directory-b2b-delegate-invitations.md)
* [Примеры кода и команд PowerShell для службы совместной работы Azure Active Directory B2B](active-directory-b2b-code-samples.md)
* [Настройка приложений SaaS для службы совместной работы B2B](active-directory-b2b-configure-saas-apps.md)
* [Основные сведения о токенах пользователей в службе совместной работы Azure Active Directory B2B](active-directory-b2b-user-token.md)
* [Сопоставление утверждений пользователя службы совместной работы B2B в Azure Active Directory](active-directory-b2b-claims-mapping.md)
* [Доступ внешних пользователей к Office 365](active-directory-b2b-o365-external-user.md)
* [Текущие ограничения службы совместной работы Azure Active Directory B2B](active-directory-b2b-current-limitations.md)
