---
title: "Самостоятельная регистрация на портале для службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Служба совместной работы Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
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
ms.date: 05/24/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 307373c75bbb87cec683f7a3097f8f159c9d5e61
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017

---


# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Самостоятельная регистрация на портале для службы совместной работы Azure AD B2B

С помощью встроенных функций, предоставленных для ИТ-администраторов через [портал Azure](https://portal.azure.com) и [панель доступа к приложениям](https://myapps.microsoft.com) для пользователей, клиенты могут выполнять множество различных операций. Но мы также знаем, что предприятиям требуется настроить рабочий процесс регистрации для пользователей B2B с учетом потребностей конкретной организации. Они могут это сделать с помощью [нашего интерфейса API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Обсуждая этот вопрос с нашими клиентами, мы выяснили, что есть одна общая потребность, которая преобладает над всеми остальными. Приглашающая организация не может знать наперед, каким отдельным внешним сотрудникам необходимо получить доступ к своим ресурсам. Таким организациям необходимо, чтобы пользователи из компаний-партнеров могли самостоятельно регистрироваться с применением набора политик, контролируемых приглашающей организацией. Такой сценарий можно реализовать с помощью наших API-интерфейсов, что мы и сделали в опубликованном на GitHub проекте: [пример проекта на портале GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Этот проект на портале GitHub демонстрирует, как с помощью наших API-интерфейсов организации могут предоставлять своим доверенным партнерам возможность самостоятельно регистрироваться на основе политики с правилами, определяющими, к каким приложениям они могут получать доступ. Пользователи из компаний-партнеров получают безопасный доступ к ресурсам, когда им это требуется, а со стороны приглашающей организации никто не должен их регистрировать вручную. Вы можете с легкостью развернуть проект в любую подписку Azure.

## <a name="as-is-code"></a>Код "как есть"

Не забывайте, что этот код предоставлен в качестве примера для демонстрации использования API приглашения службы Azure Active Directory B2B. Перед развертыванием в рабочем сценарии его должна настроить и проверить ваша команда разработчиков или ваш партнер.

## <a name="next-steps"></a>Дальнейшие действия

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
