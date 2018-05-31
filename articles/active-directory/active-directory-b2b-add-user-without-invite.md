---
title: Добавление пользователей службы совместной работы B2B в Azure Active Directory без приглашения | Документация Майкрософт
description: Вы можете разрешить гостевому пользователю добавлять других гостевых пользователей в Azure AD без активации приглашения в службу совместной работы Azure Active Directory B2B.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075629"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Добавление гостевых пользователей службы совместной работы B2B без приглашения

> [!NOTE]
> Теперь гостевым пользователям больше не нужно электронное приглашение, за исключением особых случаев. Дополнительные сведения см. в статье [Azure Active Directory B2B collaboration invitation redemption](active-directory-b2b-redemption-experience.md) (Активация приглашения службы совместной работы Azure Active Directory B2B).  

Можно разрешить пользователю, например представителю партнера, добавлять пользователей из партнерской организации в вашу организацию без необходимости активировать приглашения. Все, что необходимо сделать, — предоставить этому пользователю привилегии на перечисление в каталоге, используемом для партнерской организации. 

Предоставляйте эти привилегии в следующих случаях.

1. Пользователь в главной организации (например, WoodGrove) приглашает одного пользователя из партнерской организации (например, Sam@litware.com) в качестве гостя.
2. Администратор главной организации [настраивает политики](active-directory-b2b-delegate-invitations.md), позволяющие Сэму идентифицировать и добавлять других пользователей из партнерской организации (Litware).
3. Теперь Сэм может добавлять других пользователей из Litware в каталог, группы или приложения WoodGrove без отправки и активации приглашений. Если у Сэма есть соответствующие привилегии на перечисление в Litware, то это происходит автоматически.

### <a name="next-steps"></a>Дополнительная информация

- [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?](active-directory-b2b-iw-add-users.md)
- [Активация приглашения службы совместной работы B2B](active-directory-b2b-redemption-experience.md)
- [Делегирование приглашений для службы совместной работы Azure Active Directory B2B](active-directory-b2b-delegate-invitations.md)

