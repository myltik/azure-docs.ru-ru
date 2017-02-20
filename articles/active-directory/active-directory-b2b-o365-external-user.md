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
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 716d19be09085db91c17d7ed76422e05d164d137


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Внешний общий доступ Office 365 и служба совместной работы Azure Active Directory B2B

Внешний общий доступ в Office&365; (OneDrive, SharePoint Online, единые группы и т. д.) и служба совместной работы Azure Active Directory (Azure AD) B2B — это технически то же самое. Все решения внешнего общего доступа (за исключением OneDrive и SharePoint Online), в том числе **гости в единых группах**, уже используют для общего доступа API приглашения службы совместной работы Azure AD B2B.

В OneDrive и SharePoint Online (ODSP) используется другой диспетчер приглашений, так как поддержка внешнего общего доступа в ODSP была реализована до того, как ее внедрили в структуру Azure AD. Со временем функция внешнего общего доступа ODSP обросла дополнительными компонентами, и миллионы пользователей используют встроенную модель общего доступа этого продукта. Мы сотрудничаем с ODSP и работаем над внедрением интерфейсов API приглашения службы Azure AD B2B (описанных в этой документации), чтобы полностью объединить все преимущества и реализовать все возможности приглашений, используемые в Azure AD. Однако, существуют незначительные различия в принципах работы внешнего общего доступа ODSP и службы Azure AD B2B:

1. ODSP добавляет пользователя в каталог после того, как он активировал свое приглашение. Таким образом, пока пользователь не активировал приглашение, вы не увидите его на портале Azure AD. В то же время, если пользователь приглашается из другого сайта, то создается новое приглашение. А при использовании службы совместной работы B2B пользователь добавляется непосредственно с приглашением, поэтому он сразу везде отображается.

2. Процесс активации приглашения в ODSP отличается от аналогичной процедуры в службе совместной работы B2B.

3. Тем не менее, если пользователь активирует приглашение, то действия похожи.

4. Пользователей, приглашенных в службу совместной работы B2B, можно выбирать в диалоговых окнах общего доступа ODSP. Приглашенные пользователи ODSP также отображаются в Azure AD после того, как они активируют свои приглашения.

5. Чтобы использовать функцию внешнего общего доступа в ODSP со службой совместной работы B2B, управляя и применяя администраторский контроль, задайте для параметра внешнего общего доступа ODSP значение **Only allow sharing with external users already in the directory** (Разрешить общий доступ только для внешних пользователей, которые уже внесены в каталог). Пользователи могут перейти на внешние сайты, предоставляющие общий доступ, и выбрать из внешних сотрудников, добавленных администратором. Администратор может добавлять внешних сотрудников с помощью интерфейсов API приглашения службы совместной работы B2B.

![Настройка внешнего общего доступа в ODSP](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

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



<!--HONumber=Feb17_HO1-->


