---
title: "Динамические группы и служба совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Службу совместной работы Azure Active Directory B2B можно использовать с динамическими группами Azure AD."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/12/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9519719a63387cb8c341c72e262c2aeaacc3cd4a
ms.lasthandoff: 04/12/2017


---

# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Динамические группы и служба совместной работы Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>Что такое динамические группы?
Динамическая конфигурация членства в группе безопасности для Azure Active Directory (Azure AD) доступна на [портале Azure](https://portal.azure.com). Администраторы могут устанавливать правила для заполнения групп, создаваемых в Azure Active Directory на основе атрибутов пользователей (например, userType, отдел или страна). Это позволяет автоматически добавлять участников в группу безопасности или удалять их из нее в случае изменения их атрибутов. Эти группы можно использовать для обеспечения доступа к приложениям или облачным ресурсам, таким как сайты и документы SharePoint, а также для назначения лицензий участникам. Дополнительные сведения о динамических групп см. в статье [Выделенные группы в Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Для подписок AAD уровня "Премиум P1" и "Премиум P2" портал Azure теперь предоставляет возможность создания расширенных правил для поддержки более сложного динамического членства в группах Azure Active Directory на основе атрибутов. Дополнительные сведения о создании расширенных правил см. в статье [Создание расширенных правил членства в группе с помощью атрибутов в предварительной версии Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Что такое встроенные динамические группы?
Динамическая группа **Все пользователи** дает администраторам клиента возможность одним щелчком создать группу, которая содержит всех пользователей в клиенте. По умолчанию группа **Все пользователи** включает в себя всех пользователей в каталоге, в том числе участников и гостей.
На новом портале администрирования Azure Active Directory в представлении "Параметры группы" можно включить группу **Все пользователи**.

![встроенные группы](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

Усиление защиты динамической группы "Все пользователи". По умолчанию группа **Все пользователи** также включает в себя (гостевых) пользователей службы совместной работы B2B. Если вы хотите усилить защиту группы **Все пользователи**, удалив из нее гостевых пользователей, то это можно сделать без труда. Просто измените правило на основе атрибутов, примененное к группе **Все пользователи**. На следующем снимке экрана показана группа **Все пользователи**, измененная для исключения гостей.

![включение группы "Все пользователи"](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

Возможно, вам также пригодится динамическая группа, в которую входят только гостевые пользователи. Создайте ее и применяйте, например, в политиках нацеливания (таких как политики условного доступа) для гостевых пользователей.
Ниже показано, как может выглядеть такая группа:

![исключение гостевых пользователей](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

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

