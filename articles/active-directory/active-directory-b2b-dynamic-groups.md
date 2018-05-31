---
title: Динамические группы и служба совместной работы Azure Active Directory B2B | Документация Майкрософт
description: Использование службы совместной работы Azure Active Directory B2B с динамическими группами Azure AD.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 12/14/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 751502c2be84e9454c507f09a47b609d003ce2c5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33927857"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Динамические группы и служба совместной работы Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>Что такое динамические группы?
Динамическая конфигурация членства в группе безопасности для Azure Active Directory (Azure AD) доступна на [портале Azure](https://portal.azure.com). Администраторы могут устанавливать правила для заполнения групп, создаваемых в Azure AD на основе атрибутов пользователей (например, userType, department или country). Участников можно автоматически добавлять в группу безопасности или удалять их из нее в зависимости от атрибутов. Эти группы могут предоставлять доступ к приложениям или облачным ресурсам, таким как сайты и документы SharePoint. Их также можно использовать для назначения лицензий участникам. Дополнительные сведения о динамических групп см. в статье [Выделенные группы в Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Для создания и использования динамических групп требуется соответствующее [лицензирование Azure AD Premium P1 или P2](https://azure.microsoft.com/pricing/details/active-directory/). Дополнительные сведения см. в статье [Создание правил на основе атрибутов для динамического членства в группах в Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Что такое встроенные динамические группы?
Динамическая группа **Все пользователи** дает администраторам клиента возможность одним щелчком создать группу, которая содержит всех пользователей в клиенте. По умолчанию группа **Все пользователи** включает в себя всех пользователей в каталоге, в том числе участников и гостей.
На новом портале администрирования Azure Active Directory в представлении "Параметры группы" можно включить группу **Все пользователи**.

![Показывает включение группы "Все пользователи" с помощью значения "Да".](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Усиление защиты динамической группы "Все пользователи"
По умолчанию группа **Все пользователи** также включает (гостевых) пользователей службы совместной работы B2B. Вы можете дополнительно обеспечить безопасность группы **Все пользователи**, используя правило для удаления гостевых пользователей. На следующем снимке экрана показана группа **Все пользователи**, измененная для исключения гостей.

![Показывает правило, в котором пользователь не является гостем.](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

Вы можете также рассмотреть создание новой динамической группы, содержащей только гостевых пользователей, чтобы применить политики (например, политики условного доступа Azure AD).
Как такая группа может выглядеть:

![Показывает правило, в котором пользователь является гостем.](media/active-directory-b2b-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Дополнительная информация

- [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md)
- [Добавление пользователя службы совместной работы Azure Active Directory B2B в роль](active-directory-b2b-add-guest-to-role.md)
- [Conditional access for B2B collaboration users](active-directory-b2b-mfa-instructions.md) (Условный доступ пользователей в службе совместной работы B2B)

