---
title: "Самостоятельная регистрация на портале для службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Служба совместной работы Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
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
ms.date: 05/24/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: bb63a3b23bdcaac5c94d43bb8e7294a82b0c3fa0
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2018
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Самостоятельная регистрация на портале для службы совместной работы Azure AD B2B

С помощью встроенных функций, доступных на [портале Azure](https://portal.azure.com) для ИТ-администраторов и [панели доступа к приложениям](https://myapps.microsoft.com) для пользователей, клиенты могут выполнять разные операции. Но мы также знаем, что предприятиям требуется настроить рабочий процесс регистрации для пользователей B2B с учетом потребностей конкретной организации. Это можно сделать с помощью [API приглашения](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

После обсуждения этого вопроса с клиентами выделилась одна общая приоритетная потребность. Приглашающая организация не может знать наперед, каким отдельным внешним сотрудникам необходимо получить доступ к своим ресурсам. Таким организациям необходимо, чтобы пользователи из компаний-партнеров могли самостоятельно регистрироваться с применением набора политик, контролируемых приглашающей организацией. Такой сценарий можно реализовать с помощью API-интерфейсов, что мы и сделали в [опубликованном на GitHub примере проекте](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Этот проект на портале GitHub демонстрирует, как с помощью API-интерфейсов организации могут предоставлять своим доверенным партнерам возможность самостоятельно регистрироваться на основе политики с правилами, определяющими, к каким приложениям они могут получать доступ. Пользователи из компаний-партнеров получают безопасный доступ к ресурсам, когда им это требуется, а со стороны приглашающей организации никто не должен их регистрировать вручную. Вы можете с легкостью развернуть проект в любую подписку Azure.

## <a name="as-is-code"></a>Код "как есть"

Не забывайте, что этот код предоставлен в качестве примера для демонстрации использования API приглашения службы Azure Active Directory B2B. Перед развертыванием в рабочем сценарии его должна настроить и проверить команда разработчиков или партнер.

## <a name="next-steps"></a>Дополнительная информация

Другие статьи о службе совместной работы Azure AD B2B:
* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](active-directory-b2b-admin-add-users.md)
* [Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?](active-directory-b2b-iw-add-users.md)
* [Элементы сообщения с приглашением в службу совместной работы B2B](active-directory-b2b-invitation-email.md)
* [Активация приглашения службы совместной работы B2B](active-directory-b2b-redemption-experience.md)
* [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](active-directory-b2b-licensing.md)
* [Устранение неполадок службы совместной работы Azure Active Directory B2B](active-directory-b2b-troubleshooting.md)
* [Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B](active-directory-b2b-faq.md)
* [Многофакторная идентификация для пользователей службы совместной работы B2B](active-directory-b2b-mfa-instructions.md)
* [Добавление пользователей службы совместной работы B2B без приглашения](active-directory-b2b-add-user-without-invite.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)