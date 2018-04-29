---
title: Сравнение службы совместной работы B2B и B2C в Azure Active Directory | Документация Майкрософт
description: Какова разница между службой совместной работы B2B Azure Active Directory и Azure AD B2C?
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 32d7d135e67a05b59279e647c1fb2aa8c9686cef
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Сравнение службы совместной работы B2B и B2C в Azure Active Directory

Служба Azure Active Directory B2B и Azure AD B2C позволяют работать с внешними пользователями в Azure AD. Но чем они отличаются?


Возможности службы совместной работы B2B |     Изолированное предложение Azure AD B2C
-------- | --------
Назначение: организации, которым требуется аутентифицировать пользователей из партнерской организации, вне зависимости от поставщика удостоверений. | Назначение: приглашение в Azure AD пользователей мобильных приложений и веб-приложений, как отдельных клиентов, так и учреждений или организаций.
Поддерживаемые удостоверения: сотрудники с рабочим или учебными учетными записями, партнеры с рабочим или учебными учетными записями или любой адрес электронной почты. Вскоре будет реализована поддержка прямой федерации.  | Поддерживаемые удостоверения: потребители с учетными записями в локальных приложениях (любой адрес электронной почты или имя пользователя) или какой-либо поддерживаемый внешний идентификатор с прямой федерацией.
Каталог, в котором находятся пользователи-партнеры: пользователи-партнеры из внешней организации обслуживаются в одном каталоге с сотрудниками, но со специальными заметками. Этими пользователями можно управлять так же, как и сотрудниками, их можно добавить в те же группы и так далее.  | Каталог, в котором находятся сущности пользователей: в каталоге приложения. Управление осуществляется отдельно от каталога сотрудников и партнеров организации (при наличии).
Поддерживается единый вход для всех подключенных к Azure AD приложений. Например, можно предоставить доступ к приложениям Office 365 или локальным приложениям, а также к другим приложениям SaaS, таким как Salesforce или Workday.  |  В клиентах Azure AD B2C поддерживается единый вход в приложения пользователя. Единый вход в Office 365 или другие приложения SaaS корпорации Майкрософт или сторонних поставщиков не поддерживается.
Жизненный цикл партнера: управление основной или приглашающей организацией.  | Жизненный цикл клиента: самообслуживание или управление приложением.
Политики безопасности и соответствие требованиям: управление основной или приглашающей организацией.  | Политики безопасности и соответствие требованиям: управление приложением.
Фирменная символика: используется торговая марка основной или приглашающей организации.  |    Фирменная символика: управление приложением. Как правило, используется торговая марка продукта с изображением фирменной символики организации на фоне.
Дополнительные сведения: [запись блога](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [документация](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).  | Дополнительные сведения: [страница продукта](https://azure.microsoft.com/services/active-directory-b2c/), [документация](https://docs.microsoft.com/azure/active-directory-b2c/).


### <a name="next-steps"></a>Дополнительная информация

Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Свойства пользователя службы совместной работы Azure Active Directory B2B](active-directory-b2b-user-properties.md)
* [Добавление пользователя службы совместной работы Azure Active Directory B2B в роль](active-directory-b2b-add-guest-to-role.md)
* [Делегирование приглашений для службы совместной работы Azure Active Directory B2B](active-directory-b2b-delegate-invitations.md)
* [Динамические группы и служба совместной работы Azure Active Directory B2B](active-directory-b2b-dynamic-groups.md)
* [Настройка приложений SaaS для службы совместной работы B2B](active-directory-b2b-configure-saas-apps.md)
* [Основные сведения о токенах пользователей в службе совместной работы Azure Active Directory B2B](active-directory-b2b-user-token.md)
* [Сопоставление утверждений пользователя службы совместной работы B2B в Azure Active Directory](active-directory-b2b-claims-mapping.md)
* [Доступ внешних пользователей к Office 365](active-directory-b2b-o365-external-user.md)
* [Текущие ограничения службы совместной работы Azure Active Directory B2B](active-directory-b2b-current-limitations.md)
* [Получение поддержки для службы совместной работы B2B](active-directory-b2b-support.md)
