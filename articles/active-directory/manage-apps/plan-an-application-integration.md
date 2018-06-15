---
title: Приступая к интеграции Azure AD с приложениями | Документация Майкрософт
description: Эта статья представляет собой вводное руководство по интеграции Azure Active Directory (AD) с локальными приложениями и облачными приложениями.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 183c5f3196653ce9519c0742db7a67cd36c63fb9
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292843"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Руководство по интеграции Azure Active Directory с приложениями
## <a name="overview"></a>Обзор
В этой статье описан план интеграции приложений с Azure Active Directory (AD). Каждый из приведенных ниже разделов содержит краткое содержание более подробной статьи. Таким образом, вы сможете определить, какие части этого руководства по началу работы соответствуют вашим потребностям.  Для более подробного изучения каждой темы воспользуйтесь соответствующими ссылками.

## <a name="before-you-begin-take-inventory"></a>Инвентаризация перед началом работы
Прежде чем перейти к интеграции приложений с Azure AD, нужно узнать, чем вы располагаете и что требуется сделать.  Перечисленные ниже вопросы помогут вам обдумать проект интеграции приложений с Azure AD.

### <a name="application-inventory"></a>Инвентаризация приложений
* Где находятся все ваши приложения? Кто ими владеет?
* Какой тип проверки подлинности требуется для приложений?
* Кому требуется доступ и к какому из приложений?
* Вы хотите развернуть новое приложение?
  * Вы создадите его локально и развернете в экземпляре службы вычислений Azure?
  * Вы будете использовать одно из приложений коллекции Azure?

### <a name="user-and-group-inventory"></a>Инвентаризация пользователей и групп
* Где находятся учетные записи пользователей?
  * Локальная служба Active Directory
  * Azure AD
  * в отдельной базе данных приложений, владельцем которой вы являетесь;
  * в несанкционированных приложениях;
  * все вышеперечисленное.
* Какие разрешения и роли в настоящее время назначены пользователям? Необходимо ли пересмотреть права доступа, или вы уверены, что право доступа и роли назначены пользователям правильно?
* В вашем локальном каталоге Active Directory уже созданы группы?
  * Как эти группы упорядочены?
  * Кто входит в эти группы?
  * Какие разрешения и роли назначены группам в настоящее время?
* Необходима ли очистка баз данных пользователей или групп перед интеграцией?  (Это чрезвычайно важный вопрос, ведь результат напрямую зависит от исходного состояния.)

### <a name="access-management-inventory"></a>Инвентаризация управления доступом
* Как вы в настоящее время управляете доступом пользователей к приложениям? Требуются ли изменения?  Рассматривали ли вы другие способы управления доступом, например с помощью [RBAC](../../role-based-access-control/role-assignments-portal.md) ?
* Кому из пользователей требуется доступ к тем или иным ресурсам?

Возможно, вы сразу не сможете ответить на все эти вопросы, но это нормально.  Это руководство поможет вам ответить на некоторые из этих вопросов и принять обоснованные решения.

## <a name="prerequisites"></a>предварительным требованиям
* Подписка Azure и каталог Azure Active Directory.  Если у вас еще нет подписки Azure, вы можете получить бесплатную пробную версию Azure на 30 дней. [Просто попробуйте!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Интеграция приложений с Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Поиск несанкционированных облачных приложений с Cloud App Discovery
Как было сказано выше, могут обнаружиться приложения, которые до сих пор были вне поля зрения вашей организации.  В процессе инвентаризации можно найти несанкционированные облачные приложения. См. статью [Поиск неуправляемых облачных приложений с помощью Cloud App Discovery](cloud-app-discovery.md).

### <a name="authentication-types"></a>Типы проверки подлинности
Каждое приложение может иметь разные требования к проверке подлинности. С помощью Azure AD сертификаты подписи можно использовать с приложениями, использующими SAML 2.0, WS-Federation или протоколы подключения OpenID, а также единый вход с паролем. Дополнительные сведения о типах аутентификации в приложениях для использования с Azure AD см. в статье [Управление сертификатами для федеративного единого входа в Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) и разделе [Единый вход на основе пароля](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Включение единого входа с помощью прокси приложений Azure AD
С помощью прокси приложений Microsoft Azure AD вы можете предоставлять безопасный доступ к приложениям, расположенным в частной сети, из любого места и с любого устройства. После установки в своей среде соединителя прокси-сервера приложения его можно легко настроить с Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Интеграция приложений с Azure AD
В следующих статьях рассматриваются разные способы интеграции приложений с Azure AD и приводятся некоторые рекомендации.

* [Администрирование каталога Azure AD.](../active-directory-administer.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](what-is-single-sign-on.md)
* [Список учебников по интеграции приложений SaaS с Azure Active Directory.](../active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Управление доступом к приложениям
В следующих статьях описываются способы управления доступом к приложениям после интеграции с Azure AD с помощью соединителей Azure AD и Azure AD.

* [Управление доступом к приложениям с помощью Azure AD.](what-is-access-management.md)
* [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory.](../active-directory-saas-app-provisioning.md)
* [Назначение пользователей для приложения](../active-directory-applications-guiding-developers-assigning-users.md)
* [Назначение групп для приложения](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Совместное использование учетных записей.](../active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Интеграция пользовательских приложений
Если вы создаете новое приложение и хотите помочь разработчикам в использовании мощных возможностей Azure AD, то см. статью [Руководство для разработчиков](../active-directory-applications-guiding-developers-for-lob-applications.md).

Если вы хотите добавить пользовательское приложение в коллекцию приложений Azure, то см. запись блога [“Bring your own app” with Azure AD Self-Service SAML configuration](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/) (Использование собственных приложений с конфигурацией SAML самообслуживания Azure AD).

## <a name="see-also"></a>См. также
* [Указатель статьей по управлению приложениями в Azure Active Directory](../active-directory-apps-index.md)

