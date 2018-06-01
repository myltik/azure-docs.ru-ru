---
title: Основные сведения о манифесте приложения Azure Active Directory | Документация Майкрософт
description: Подробное описание манифеста приложения Azure Active Directory, который представляет конфигурацию удостоверений приложения в клиенте Azure AD и используется для упрощения авторизации OAuth, предоставления согласия и многого другого.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: 9f73f31c7afd7ca13107653d097e1ac11ef94f0d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157081"
---
# <a name="azure-active-directory-application-manifest"></a>Манифест приложения Azure Active Directory
Приложения, которые интегрируются с Azure AD необходимо зарегистрировать в клиенте Azure AD. Это приложение можно настроить с помощью манифеста приложения (в колонке Azure AD) на [портале Azure](https://portal.azure.com).

## <a name="manifest-reference"></a>Справка по манифесту

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Ключ  |Тип значения |Пример значения  |ОПИСАНИЕ  |
|---------|---------|---------|---------|
|appID     |  Строка идентификатора       |""|  Уникальный идентификатор приложения, который назначается приложению в Azure AD.|
|appRoles     |    Тип массива     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|Коллекция ролей, которые может объявить приложение. Эти роли могут назначаться пользователям, группам или субъектам-службам.|
|AvailableToOtherTenants|Логическое|`true`|Если установлено значение true, приложение доступно для других клиентов. Если установлено значение false, приложение будет доступно только клиенту, в котором оно зарегистрировано. Дополнительные сведения см. в статье [Как реализовать вход любого пользователя Azure Active Directory (AD) с помощью шаблона мультитенантного приложения](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |строка         |`MyRegisteredApp`         |Отображаемое имя приложения. |
|errorURL     |строка         |`http://MyRegisteredAppError`         |URL-адрес для ошибок, возникающих в приложении. |
|groupMembershipClaims     |    строка     |    `1`     |   Битовая маска, настраивающая утверждение "groups", выданное в маркере пользователя или маркере доступа OAuth 2.0, которого ожидает приложение. Значения битовой маски: 0 — нет, 1 — группы безопасности и роли Azure AD, 2 — зарезервировано и 4 — зарезервировано. Если задать битовой маске значение 7, это предоставит все группы безопасности, группы рассылки и роли каталога Azure AD, членом которых является выполнивший вход пользователь. |
|optionalClaims     |  строка       |     `null`    |    [Необязательные утверждения](active-directory-optional-claims.md), возвращаемые в токене службы токенов безопасности для этого конкретного приложения. |
|acceptMappedClaims    |      Логическое   | `true`        |    Если установлено значение true, приложение может использовать сопоставление утверждений без указания пользовательского ключа подписи.|
|homepage     |  строка       |`http://MyRegistererdApp`         |    URL-адрес домашней страницы приложения. |
|identifierUris     |  Массив строк       | `http://MyRegistererdApp`        |   Определяемые пользователем URI, которые уникальным образом идентифицируют веб-приложение в клиенте Azure AD или в проверенном личном домене, если приложение является мультитенантным. |
|keyCredentials     |   Тип массива      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   Это свойство содержит ссылки на учетные данные, назначенные приложению, строковые общие секреты и сертификаты X.509. Эти учетные данные используются при запросе маркеров доступа (когда приложение работает в качестве клиента, а не ресурса). |
|knownClientApplications     |     Тип массива    |    [guid]     |     Значение используется для объединения согласия, если у вас есть решение, которое содержит две части: клиентское приложение и пользовательское приложение веб-API. Если в это значение ввести appID клиентского приложения, пользователю нужно будет только единожды предоставить согласие на использование клиентского приложения. Azure AD будет знать, что предоставление клиенту согласия подразумевает неявное одобрение веб-API, и автоматически предоставит субъекты-службы для клиентского приложения и для веб-API одновременно. И клиентское приложение, и веб-API должны быть зарегистрированы в одном и том же клиенте.|
|logoutUrl     |   строка      |     `http://MyRegisteredAppLogout`    |   URL-адрес для выхода из приложения. |
|oauth2AllowImplicitFlow     |   Логическое      |  `false`       |       Определяет, может ли это веб-приложение запрашивать маркеры неявного потока OAuth2.0. Его значение по умолчанию — false. Этот флаг используется для браузерных приложений, таких как одностраничные приложения JavaScript. |
|oauth2AllowUrlPathMatching     |   Логическое      |  `false`       |   Указывает, будет ли Azure AD в рамках запросов маркеров OAuth 2.0 разрешать сопоставление путей URI перенаправления с replyUrls приложения. Его значение по умолчанию — false. |
|oauth2Permissions     | Тип массива         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Коллекция областей разрешений доступа OAuth 2.0, которые веб-API (ресурс) предоставляет клиентским приложениям. Эти области действия разрешений могут быть назначены клиентским приложениям во время предоставления согласия. |
|oauth2RequiredPostResponse     | Логическое        |    `false`     |      Указывает, будет ли Azure AD в рамках запросов маркеров OAuth 2.0 разрешать запросы POST в отличие от запросов GET. Значение по умолчанию — false. В таком случае будут разрешены только запросы GET. 
|objectId     | Строка идентификатора        |     ""    |    Уникальный идентификатор для приложения в каталоге. Это не тот идентификатор, который используется для идентификации приложения в транзакциях протоколов. Он используется для ссылки на объект в запросах к каталогу.|
|passwordCredentials     | Тип массива        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    Описание см. в свойстве keyCredentials. |
|publicClient     |  Логическое       |      `false`   | Указывает, является ли это приложение общедоступным клиентом (например, установленным приложением, которое работает на мобильном устройстве). Значение по умолчанию — false. |
|supportsConvergence     |  Логическое       |   `false`      | Не следует изменять это свойство. Примите значение по умолчанию. |
|replyUrls     |  Массив строк       |   `http://localhost`     |  Это свойство с несколькими значениями хранит список зарегистрированных значений redirect_uri, которые Azure AD будет принимать в качестве назначений при возвращении маркеров. |
|requiredResourceAccess     |     Тип массива    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   Указывает ресурсы, которые требуются этому приложению, и задает области разрешений OAuth и роли приложения, которые необходимы в каждом из этих ресурсов. Эта предварительная настройка доступа к необходимым ресурсам выполняется при получении согласия.|
|resourceAppId     |    Строка идентификатора     |  ""      |   Уникальный идентификатор для ресурса, доступ к которому нужен приложению. Это значение должно соответствовать appId, который был объявлен в целевом приложении ресурса. |
|resourceAccess     |  Тип массива       | Ознакомьтесь с примером значения свойства requiredResourceAccess. |   Список областей разрешений OAuth2.0 и ролей приложения, которые необходимы приложению от определенного ресурса (содержит значения идентификатора и типа определенного ресурса).        |
|samlMetadataUrl    |строка| `http://MyRegisteredAppSAMLMetadata` |URL-адрес метаданных SAML для приложения.| 

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о связи между объектами приложения и субъекта-службы см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory (Azure AD)][AAD-APP-OBJECTS].
* Определения некоторых основных понятий для разработчиков Azure Active Directory (AD) см. в [глоссарии разработчика по Azure AD][AAD-DEVELOPER-GLOSSARY].

Оставляйте свои замечания и пожелания в разделе ниже. Ваши отзывы помогают нам улучшать содержимое веб-сайта.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

