---
title: Внешний общий доступ Office 365 и служба совместной работы Azure Active Directory B2B | Документация Майкрософт
description: В этом документе описано общее использование ресурсов с внешними партнерами с помощью Office 365 и службы совместной работы Azure Active Directory B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: d55d587022b763a6890c098dd0a1b9bef2a3b7fb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267217"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Внешний общий доступ Office 365 и служба совместной работы Azure Active Directory B2B

Внешний общий доступ в Office 365 (OneDrive, SharePoint Online, единые группы и т. д.) и служба совместной работы Azure Active Directory (Azure AD) B2B — это одно и то же с технической точки зрения. Все решения внешнего общего доступа (за исключением OneDrive и SharePoint Online), в том числе гости в группах Office 365, уже используют для общего доступа API приглашения службы совместной работы Azure AD B2B.

В OneDrive и SharePoint Online используется другой диспетчер приглашений. Поддержка внешнего общего доступа в OneDrive и SharePoint Online была реализована до того, как ее внедрили в Azure AD. Со временем функция внешнего общего доступа OneDrive и SharePoint Online обросла дополнительными компонентами, и миллионы пользователей используют встроенную модель общего доступа этого продукта. Однако существуют незначительные различия в принципах работы внешнего общего доступа OneDrive и SharePoint Online и службы совместной работы Azure AD B2B:

- OneDrive и SharePoint Online добавляют пользователей в каталог после того, как пользователи активировали приглашения. Таким образом, до активации вы не видите пользователя на портале Azure AD. В то же время если пользователь приглашается из другого сайта, то создается новое приглашение. При использовании службы совместной работы Azure AD B2B пользователь добавляется непосредственно после отправки приглашения, поэтому он сразу везде отображается.

- Процесс активации приглашения в OneDrive и SharePoint Online отличается от аналогичной процедуры в службе совместной работы Azure AD B2B. После того как пользователь активирует приглашение, интерфейсы будут выглядеть одинаково.

- Пользователей, приглашенных в службу совместной работы Azure AD B2B, можно выбирать в диалоговых окнах общего доступа OneDrive и SharePoint Online. Приглашенные пользователи OneDrive и SharePoint Online также отображаются в Azure AD после того, как они активируют свои приглашения.

- Чтобы управлять внешним общим доступом в OneDrive и SharePoint Online с помощью службы совместной работы Azure AD B2B, задайте для параметра внешнего общего доступа OneDrive и SharePoint Online значение **Only allow sharing with external users already in the directory** (Разрешить общий доступ только внешним пользователям, которые уже внесены в каталог). Пользователи могут перейти на внешние сайты, предоставляющие общий доступ, и выбрать из внешних сотрудников, добавленных администратором. Администратор может добавлять внешних сотрудников с помощью интерфейсов API приглашения службы совместной работы B2B.

![Настройка внешнего общего доступа в OneDrive и SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Дополнительная информация

* [Что такое служба совместной работы Azure AD B2B?](what-is-b2b.md)
* [Добавление пользователя службы совместной работы Azure Active Directory B2B в роль](add-guest-to-role.md)
* [Делегирование приглашений для службы совместной работы Azure Active Directory B2B](delegate-invitations.md)
* [Динамические группы и служба совместной работы Azure Active Directory B2B](use-dynamic-groups.md)