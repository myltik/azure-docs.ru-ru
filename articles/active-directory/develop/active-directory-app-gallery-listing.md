---
title: Добавление приложения в коллекцию приложений Azure Active Directory | Документация Майкрософт
description: Узнайте, как добавить приложение, поддерживающее единый вход, в коллекцию приложений Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 5f42a706bd7cb44162765bb77039cc3173d6941e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354458"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Добавление приложения в коллекцию приложений Azure Active Directory


##  <a name="what-is-the-azure-ad-application-gallery"></a>Что такое коллекция приложений Azure AD?

Azure Active Directory (Azure AD) — это облачная служба идентификации. [Коллекция приложений Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) представляет собой хранилище приложений Azure Marketplace, в котором публикуются все соединители приложений для единого входа и подготовки пользователей. Клиенты, которые используют Azure AD в качестве поставщика удостоверений, находят опубликованные здесь различные соединители приложений SaaS. Администраторы ИТ-систем добавляют соединители из коллекции приложений, а затем настраивают и используют эти соединители для единого входа и подготовки. Azure AD поддерживает для функции единого входа все основные протоколы федерации, в том числе SAML 2.0, OpenID Connect, OAuth и WS-Fed.

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>В чем польза от включения приложения в коллекцию?

*  Клиенты получают самый удобный способ единого входа.

*  Конфигурация приложения будет простой и минимально необходимой. 

*  Ваше приложение можно будет найти в коллекции с помощью быстрого поиска.

*  Эту интеграцию могут использовать все клиенты Azure AD категорий "Бесплатный", "Базовый" и "Премиум". 

*  Нашим общим клиентам предлагается пошаговое руководство по настройке. 

*  Клиенты, которые пользуются SCIM, могут использовать подготовку для того же приложения.


##  <a name="prerequisites-implement-federation-protocol"></a>Предварительные требования: реализовать протокол федерации

Чтобы включить приложение в коллекцию Azure AD, сначала необходимо реализовать один из следующих протоколов федерации, поддерживаемых Azure AD. Прочитайте описанные здесь условия работы с коллекцией приложений Azure AD. 

*   **OpenID Connect**. Создайте многопользовательское приложение в Azure AD и реализуйте в нем [инфраструктуру согласия Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework). Направляйте запрос на вход к общедоступной конечной точке, чтобы любой клиент мог предоставить согласие для приложения. Доступом пользователей можно управлять на основе идентификатора клиента и имени участника-пользователя, которые передаются в маркере. Чтобы интегрировать приложение с Azure AD, следуйте [инструкциям для разработчиков](active-directory-authentication-scenarios.md).

    ![Временная шкала включения в коллекцию приложения OpenID Connect](./media/active-directory-app-gallery-listing/openid.png)

    * Если вы хотите добавить в коллекцию приложение, использующее OpenID Connect, выберите **OpenID Connect & OAuth 2.0** (OpenID Connect и OAuth 2.0), как описано выше.

    * Если у вас возникнут проблемы с доступом к порталу, свяжитесь с [командой интеграции единого входа Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **SAML 2.0** или **WS-Fed**. Приложение должно поддерживать интеграцию единого входа SAML и (или) WS-Fed в режиме, инициируемом поставщиком услуг или поставщиком удостоверений. Если ваше приложение поддерживает SAML 2.0, его можно интегрировать непосредственно из клиента Azure AD, используя [инструкции по добавлению настраиваемого приложения](../active-directory-saas-custom-apps.md).

    ![Временная шкала включения в коллекцию приложения SAML 2.0 или WS-Fed](./media/active-directory-app-gallery-listing/saml.png)

    * Если вы хотите добавить в коллекцию приложение, использующее **SAML 2.0** или **WS-Fed**, выберите **SAML 2.0/WS-Fed** (SAML 2.0 или WS-Fed), как описано выше.

    * Если у вас возникнут проблемы с доступом к порталу, свяжитесь с [командой интеграции единого входа Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **Единый вход с помощью пароля**. Создайте веб-приложение с HTML-страницей единого входа, чтобы [настроить единый вход с помощью пароля](../manage-apps/what-is-single-sign-on.md). Единый вход с помощью пароля (хранение пароля в хранилище) позволяет управлять доступом и паролями для веб-приложений, которые не поддерживают федерацию удостоверений. Также он полезен в ситуациях, когда несколько пользователей работают под одной учетной записью (например, с корпоративными страницами в социальных сетях).

    ![Временная шкала включения в коллекцию приложения, использующего единый вход по паролю](./media/active-directory-app-gallery-listing/passwordsso.png)

    * Если вы хотите добавить в коллекцию приложение, использующее единый вход по паролю, выберите **Password SSO** (Единый вход по паролю), как описано выше.

    * Если у вас возникнут проблемы с доступом к порталу, свяжитесь с [командой интеграции единого входа Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

##  <a name="updateremove-existing-listing"></a>Обновление или удаление существующего приложения

Чтобы обновить или удалить существующее приложение в коллекции приложений Azure AD, сначала необходимо отправить запрос на [портал сети приложений](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Если у вас есть учетная запись Office 365, используйте ее для входа на этот портал. Если ее нет, используйте для входа учетную запись Майкрософт (например, Outlook или Hotmail).

* Выберите соответствующий вариант из рисунке ниже.

    ![График включения в коллекцию для приложений SAML](./media/active-directory-app-gallery-listing/updateorremove.png)
    
    * Если вы хотите обновить существующее приложение, выберите **Update existing application listing** (Обновить существующее приложение).

    * Если вы хотите удалить существующее приложение из коллекции Azure AD, выберите **Remove existing application listing** (Удалить существующее приложение).

    * Если у вас возникнут проблемы с доступом к порталу, свяжитесь с [командой интеграции единого входа Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="submit-the-request-in-the-portal"></a>Отправка запроса на портале

Протестировав интеграцию приложения с Azure AD, отправьте запрос на доступ через наш [портал сети приложений](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Если у вас есть учетная запись Office 365, используйте ее для входа на этот портал. Если ее нет, используйте для входа учетную запись Майкрософт (например, Outlook или Hotmail).

После входа откроется показанная ниже страница. Укажите коммерческое обоснование для доступа в текстовом поле, а затем выберите **Запросить доступ**. Наша команда рассмотрит указанные сведения и предоставит доступ соответствующим образом. После этого вы сможете войти на портал и отправить подробный запрос на включение приложения.

Если у вас возникнут проблемы с доступом к порталу, свяжитесь с [командой интеграции единого входа Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Запрос доступа на портале SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>График выполнения процессов
    
Процесс включения приложения SAML 2.0 или WS-Fed в коллекцию занимает 7–10 рабочих дней.

   ![График включения в коллекцию для приложений SAML](./media/active-directory-app-gallery-listing/timeline.png)

Процесс включения в коллекцию приложения OpenID Connect занимает 2–5 рабочих дней.

   ![График включения в коллекцию для приложений SAML](./media/active-directory-app-gallery-listing/timeline2.png)

Временная шкала добавления в коллекцию приложения с поддержкой подготовки пользователей составляет 40–45 рабочих дней.

   ![График включения в коллекцию для приложений SAML](./media/active-directory-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>Эскалация вопросов

При эскалации любых вопросов отправьте письмо [команде интеграции единого входа Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) на электронный адрес SaaSApplicationIntegrations@service.microsoft.com, и мы ответим вам в кратчайшие сроки.