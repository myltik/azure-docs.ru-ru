---
title: "Ограничения службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Текущие ограничения службы совместной работы Azure Active Directory B2B."
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
ms.date: 05/23/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 581e5d1fb5fb08d0dc89ed2c85edcb5f0005650b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Ограничения службы совместной работы Azure Active Directory B2B
К службе совместной работы Azure Active Directory B2B применяются ограничения, описанные в этой статье.

## <a name="possible-double-multi-factor-authentication"></a>Возможное дублирование Многофакторной идентификации
С помощью Azure AD B2B вы можете принудительно применять многофакторную проверку подлинности в ресурсной организации (приглашающей организации). Причины такого подхода подробно описаны в разделе [Многофакторная идентификация пользователей службы совместной работы Azure Active Directory B2B](active-directory-b2b-mfa-instructions.md). Если ваш партнер уже настроил многофакторную проверку подлинности и принудительно применяет ее, возможно, его пользователи должны будут выполнять эту проверку один раз в своей организации, а затем еще раз — в вашей организации.

## <a name="instant-on"></a>Мгновенное включение
В рабочих потоках службы совместной работы B2B пользователи добавляются в каталог и динамически обновляются в процессе активации приглашений, при назначении приложений и т. д. Операции обновления и записи обычно выполняются в одном экземпляре каталога и должны реплицироваться по всем экземплярам. Репликация будет завершена после обновления всех экземпляров. Иногда, когда объект записывается или обновляется в одном экземпляре, а вызов для извлечения этого объекта попадает на другой экземпляр, это вызывает задержку репликации. В таком случае выполните обновление или повторите попытку. Если вы пишете приложение с помощью нашего API, то повторы с некоторой задержкой являются хорошей защитной практикой для облегчения этой проблемы.

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

