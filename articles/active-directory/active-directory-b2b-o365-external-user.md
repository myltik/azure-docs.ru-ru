---
title: "Внешний общий доступ Office 365 и служба совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Справочные материалы по сопоставлению утверждений для службы совместной работы Azure Active Directory B2B."
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 6c8d7dc7d070ce65cbe637359bf7933eac68ce23
ms.openlocfilehash: ae87c3487abd194c524d067a2a9b7bb9f8df8ac7
ms.lasthandoff: 02/24/2017


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Внешний общий доступ Office 365 и служба совместной работы Azure Active Directory B2B

Внешний общий доступ в Office 365 (OneDrive, SharePoint Online, единые группы и т. д.) и служба совместной работы Azure Active Directory (Azure AD) B2B — это технически то же самое. Все решения внешнего общего доступа (за исключением OneDrive и SharePoint Online), в том числе гости в единых группах, уже используют для общего доступа API приглашения службы совместной работы Azure AD B2B.

В OneDrive и SharePoint Online используется другой диспетчер приглашений. Поддержка внешнего общего доступа в OneDrive и SharePoint Online была реализована до того, как ее внедрили в Azure AD. Со временем функция внешнего общего доступа OneDrive и SharePoint Online обросла дополнительными компонентами, и миллионы пользователей используют встроенную модель общего доступа этого продукта. Мы сотрудничаем с OneDrive и SharePoint Online и работаем над внедрением интерфейсов API приглашения службы Azure AD B2B (описанных в этой документации), чтобы полностью объединить процесс во всех продуктах и реализовать все возможности приглашений, используемые в Azure AD. Однако существуют незначительные различия в принципах работы внешнего общего доступа OneDrive и SharePoint Online и службы совместной работы Azure AD B2B:

- OneDrive и SharePoint Online добавляют пользователей в каталог после того, как пользователи активировали приглашения. Таким образом, до активации вы не видите пользователя на портале Azure AD. В то же время если пользователь приглашается из другого сайта, то создается новое приглашение. При использовании службы совместной работы Azure AD B2B пользователь добавляется непосредственно после отправки приглашения, поэтому он сразу везде отображается.

- Процесс активации приглашения в OneDrive и SharePoint Online отличается от аналогичной процедуры в службе совместной работы Azure AD B2B. После того как пользователь активирует приглашение, интерфейсы будут выглядеть одинаково.

- Пользователей, приглашенных в службу совместной работы Azure AD B2B, можно выбирать в диалоговых окнах общего доступа OneDrive и SharePoint Online. Приглашенные пользователи OneDrive и SharePoint Online также отображаются в Azure AD после того, как они активируют свои приглашения.

- Чтобы использовать функцию внешнего общего доступа в OneDrive и SharePoint Online со службой совместной работы Azure AD B2B, управляя и применяя администраторский контроль, задайте для параметра внешнего общего доступа OneDrive и SharePoint Online значение **Only allow sharing with external users already in the directory** (Разрешить общий доступ только для внешних пользователей, которые уже внесены в каталог). Пользователи могут перейти на внешние сайты, предоставляющие общий доступ, и выбрать из внешних сотрудников, добавленных администратором. Администратор может добавлять внешних сотрудников с помощью интерфейсов API приглашения службы совместной работы B2B.

![Настройка внешнего общего доступа в OneDrive и SharePoint Online](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Дальнейшие действия

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md)
* [Добавление пользователя службы совместной работы Azure Active Directory B2B в роль](active-directory-b2b-add-guest-to-role.md)
* [Делегирование приглашений для службы совместной работы Azure Active Directory B2B](active-directory-b2b-delegate-invitations.md)
* [Динамические группы и служба совместной работы Azure Active Directory B2B](active-directory-b2b-dynamic-groups.md)
* [Примеры кода и команд PowerShell для службы совместной работы Azure Active Directory B2B](active-directory-b2b-code-samples.md)
* [Настройка приложений SaaS для службы совместной работы B2B](active-directory-b2b-configure-saas-apps.md)
* [Основные сведения о токенах пользователей в службе совместной работы Azure Active Directory B2B](active-directory-b2b-user-token.md)
* [Сопоставление утверждений пользователя службы совместной работы B2B в Azure Active Directory](active-directory-b2b-claims-mapping.md)
* [Текущие ограничения службы совместной работы Azure Active Directory B2B](active-directory-b2b-current-limitations.md)

