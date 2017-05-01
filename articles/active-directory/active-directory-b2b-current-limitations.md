---
title: "Ограничения службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Текущие ограничения предварительной версии службы совместной работы Azure Active Directory B2B."
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
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: cdc951d4e16e7f0df425dba7c33d86255276f526
ms.lasthandoff: 04/13/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Ограничения службы совместной работы Azure Active Directory B2B
К службе совместной работы Azure Active Directory B2B применяются ограничения, описанные в этой статье.

## <a name="possible-double-multi-factor-authentication"></a>Возможное дублирование Многофакторной идентификации
С помощью Azure AD B2B вы можете принудительно применять Многофакторную идентификацию в ресурсной организации (приглашающей организации). Причины такого подхода подробно описаны в разделе [Многофакторная идентификация пользователей службы совместной работы Azure Active Directory B2B](active-directory-b2b-mfa-instructions.md). Это означает, что если ваш партнер уже настроил Многофакторную идентификацию и принудительно применяет ее, то, возможно, его пользователи должны будут выполнять эту проверку один раз в своей организации, а затем еще раз — в вашей организации.

В будущем выпуске будет реализована политика, позволяющая выбрать доверие к партнерской Многофакторной идентификации во избежание двойной проверки подлинности.


## <a name="instant-on"></a>Мгновенное включение
В рабочих потоках службы совместной работы B2B пользователи добавляются в каталог и динамически обновляются в процессе активации приглашений, при назначении приложений и т. д. Операции обновления и записи обычно выполняются в одном экземпляре каталога и должны реплицироваться по всем экземплярам. Чтобы завершить репликацию, может потребоваться некоторое время. Иногда, когда объект записывается или обновляется в одном экземпляре каталога, а вызов для извлечения этого объекта в связи с балансировкой нагрузки попадает на другой экземпляр, это вызывает проблемы авторизации. Мы проделали большую работу для устранения и уменьшения этих задержек репликации, но в некоторых редких случаях они по-прежнему возможны. В таком случае выполните обновление или повторите попытку. Если вы пишете приложение с помощью нашего API, то повторы с некоторой задержкой являются хорошей защитной практикой для облегчения этой проблемы.

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
* [Доступ внешних пользователей к Office 365](active-directory-b2b-o365-external-user.md)

