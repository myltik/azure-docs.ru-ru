---
title: "Сопоставление утверждений пользователя службы совместной работы B2B в Azure Active Directory | Документация Майкрософт"
description: "Справочные материалы по сопоставлению утверждений для службы совместной работы Azure Active Directory B2B."
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 3fe75e96c153a7cbcad638d606d0fbc248bd983a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Сопоставление утверждений пользователя службы совместной работы B2B в Azure Active Directory

Azure Active Directory (Azure AD) поддерживает настройку утверждений, которые передаются в токене SAML для пользователей службы совместной работы B2B. Когда пользователь проходит аутентификацию в приложении, Azure AD выдает токен SAML для приложения. В токене собрана информация (утверждения), которая однозначно идентифицирует пользователя. По умолчанию здесь указаны имя пользователя, адрес электронной почты, имя и фамилия пользователя. На вкладке "Атрибуты" можно просмотреть или изменить утверждения, которые передаются приложению в токене SAML.

Изменение утверждений, выданных в токене SAML, может потребоваться по двум основным причинам:

1. Приложение требует другого набора URI утверждений или значений утверждений.

2. Приложение требует, чтобы утверждение NameIdentifier содержало данные, отличные от имени участника-пользователя, которое хранится в Azure Active Directory.

  ![Просмотр утверждений в токене SAML](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Сведения о том, как добавлять и редактировать утверждения, см. в статье [Настройка утверждений, выпущенных в маркере SAML для предварительно интегрированных приложений в Azure Active Directory](develop/active-directory-saml-claims-customization.md). По соображениям безопасности сопоставление NameID и имени участника-пользователя между клиентами для пользователей службы совместной работы B2B запрещено.


## <a name="next-steps"></a>Дополнительная информация

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md)
* [Добавление пользователя службы совместной работы Azure Active Directory B2B в роль](active-directory-b2b-add-guest-to-role.md)
* [Делегирование приглашений для службы совместной работы Azure Active Directory B2B](active-directory-b2b-delegate-invitations.md)
* [Динамические группы и служба совместной работы Azure Active Directory B2B](active-directory-b2b-dynamic-groups.md)
* [Примеры кода и команд PowerShell для службы совместной работы Azure Active Directory B2B](active-directory-b2b-code-samples.md)
* [Настройка приложений SaaS для службы совместной работы B2B](active-directory-b2b-configure-saas-apps.md)
* [Доступ внешних пользователей к Office 365](active-directory-b2b-o365-external-user.md)
* [Основные сведения о токенах пользователей в службе совместной работы Azure Active Directory B2B](active-directory-b2b-user-token.md)
* [Текущие ограничения службы совместной работы Azure Active Directory B2B](active-directory-b2b-current-limitations.md)
