---
title: "Что такое служба совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Служба совместной работы Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: twooley
ms.custom: aaddev
ms.reviewer: sasubram
ms.openlocfilehash: e1fa0a3e32935a8d601eac83c19c6c2b91ded210
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2018
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Что такое служба совместной работы Azure AD B2B

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

Возможности совместной работы Azure AD B2B позволяют любой организации, использующей Azure AD, безопасно работать с пользователями из любой другой организации независимо от ее размера, наличия Azure AD и даже наличия ИТ-отдела. 

Организации, использующие Azure AD, могут предоставлять доступ к документам, ресурсам и приложениям своим партнерам, сохраняя полный контроль над корпоративными данными. Разработчики могут использовать предоставляемые интерфейсы API Azure AD типа "бизнес — бизнес" для написания приложений, которые защищенным способом соединяют две организации. Кроме того, предусмотрена быстрая и удобная навигация.

97 % клиентов сообщили, что служба совместной работы Azure AD B2B очень важна для них.

![круговая диаграмма](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

По состоянию на начало апреля 2017 года приблизительно 3 миллиона пользователей уже применяли возможности совместной работы Azure AD B2B. Более 23 % организаций со службой Azure AD и числом пользователей более 10 уже применяют эти возможности.

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>Основные преимущества службы совместной работы Azure AD B2B для организации

### <a name="work-with-any-user-from-any-partner"></a>Работа с любым пользователем из любой партнерской организации

* Партнеры используют собственные учетные данные

* От партнеров не требуется использовать Azure AD

* Не требуются внешние каталоги или сложная настройка

### <a name="simple-and-secure-collaboration"></a>Простая и безопасная совместная работа

* Предоставление доступа к любым корпоративным приложениям и данным с применением эффективных политик авторизации на основе Azure AD

* Простое использование

* Безопасность корпоративного уровня для приложений и данных

### <a name="no-management-overhead"></a>Отсутствие накладных расходов на управление

* Не требуется управлять внешними учетными записями или паролями

* Не требуется синхронизировать учетные записи или управлять их жизненным циклом вручную

* Отсутствие накладных расходов на внешнее администрирование

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>Простота добавления пользователей решения для совместной работы B2B в организации

Администраторы могут добавлять пользователей (гостей) решения для совместной работы B2B на [портале Azure](https://portal.azure.com).

![добавление гостевых пользователей](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Возможность использования партнерами собственных удостоверений

Партнеры B2B могут выполнять вход, используя удостоверения на свой выбор. Если у пользователя нет учетной записи Майкрософт или Azure AD, она быстро создается при активации предложения.

![выбор удостоверения для входа](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>Делегирование прав владельцами приложений и групп 
Владельцы приложений и групп могут напрямую добавлять пользователей B2B в любое приложение, будь то приложение Майкрософт или другого поставщика. Администраторы могут делегировать разрешение на добавление пользователей B2B пользователям, не являющимся администраторами. Эти пользователи могут с помощью [панели доступа к приложениям Azure AD](https://myapps.microsoft.com) добавлять пользователей B2B в приложения и группы.

![панель доступа](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![добавление участника](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Защита корпоративного содержимого с помощью политик авторизации

Вы можете применить политики условного доступа, например многофакторную проверку подлинности на следующих уровнях:
- на уровне клиента;
- на уровне приложения;
- или для конкретных пользователей, чтобы защитить корпоративные приложения и данные.

![добавление участника](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Быстрое создание приложений для регистрации с помощью интерфейсов API и образцов кода
Вы можете регистрировать внешних партнеров так, как требуется вашей организации.

С помощью [API-интерфейсов приглашения службы совместной работы B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) вы можете настроить собственные процедуры регистрации, в том числе создать портал для самостоятельной регистрации. В [GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) вы найдете образец кода для этого портала.

![портал регистрации](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

С помощью службы совместной работы Azure AD B2B вы можете максимально эффективно использовать возможности Azure AD для защиты своих отношений с партнерами, обеспечив при этом удобство работы пользователей. Присоединяйтесь к тысячам организаций, которые уже применяют Azure AD B2B для взаимодействия с внешними партнерами.

## <a name="next-steps"></a>Дополнительная информация

* Возможности для администраторов предоставляются на [портале Azure](https://portal.azure.com).

* Возможности для информационных работников доступны на [панели доступа](https://myapps.microsoft.com).

* Как всегда, вы можете связаться с группой разработчиков через [техническое сообщество Майкрософт](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b), чтобы предоставить отзыв, обсудить проблему или внести предложение.

Другие статьи о службе совместной работы Azure AD B2B:

* [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](active-directory-b2b-admin-add-users.md)
* [Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?](active-directory-b2b-iw-add-users.md)
* [Элементы сообщения с приглашением в службу совместной работы B2B](active-directory-b2b-invitation-email.md)
* [Активация приглашения службы совместной работы B2B](active-directory-b2b-redemption-experience.md)
* [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](active-directory-b2b-licensing.md)
* [Устранение неполадок службы совместной работы Azure Active Directory B2B](active-directory-b2b-troubleshooting.md)
* [Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B](active-directory-b2b-faq.md)
* [API службы совместной работы Azure Active Directory B2B и настройка](active-directory-b2b-api.md)
* [Многофакторная идентификация для пользователей службы совместной работы B2B](active-directory-b2b-mfa-instructions.md)
* [Добавление пользователей службы совместной работы B2B без приглашения](active-directory-b2b-add-user-without-invite.md)
* [Auditing and reporting a B2B collaboration user](active-directory-b2b-auditing-and-reporting.md) (Аудит и создание отчетов для пользователей службы совместной работы B2B)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
