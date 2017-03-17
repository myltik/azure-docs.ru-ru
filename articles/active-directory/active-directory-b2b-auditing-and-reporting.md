---
title: "Аудит и отчеты для пользователей службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Свойства пользователя службы совместной работы Azure Active Directory B2B можно настраивать."
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
ms.date: 02/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e25c0dab34f0977b4511046ad53f4c0b409990
ms.openlocfilehash: fd2fe0046d0709bc4527cb680795610daae01a87
ms.lasthandoff: 02/23/2017


---


# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Аудит и отчеты для пользователей службы совместной работы B2B
Для гостевых пользователей в предварительной версии доступны такие же возможности аудита, как и для пользователей-участников. Ниже приведен пример журнала приглашений и активаций только что приглашенного пользователя Сэма Угла (Sam Oogle):

![журнал аудита](./media/active-directory-b2b-auditing-and-reporting/audit-log.png)

Вы можете подробней рассмотреть каждое из этих событий. Например, давайте просмотрим сведения о принятии приглашения.

![сведения о действии](./media/active-directory-b2b-auditing-and-reporting/activity-details.png)

Вы также можете экспортировать эти журналы из Azure AD и с помощью любого инструмента для создания отчетов создать собственный настраиваемый отчет.

### <a name="next-steps"></a>Дальнейшие действия

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
* [Доступ внешних пользователей к Office 365](active-directory-b2b-o365-external-user.md)
* [Текущие ограничения службы совместной работы Azure Active Directory B2B](active-directory-b2b-current-limitations.md)

