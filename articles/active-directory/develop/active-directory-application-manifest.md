---
title: "Основные сведения о манифесте приложения Azure Active Directory | Документация Майкрософт"
description: "Подробное описание манифеста приложения Azure Active Directory, который представляет конфигурацию удостоверений приложения в клиенте Azure AD и используется для упрощения авторизации OAuth, предоставления согласия и многого другого."
services: active-directory
documentationcenter: 
author: sureshja
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: d5e18f41d6eb69ccb7eafaa4de2646c4c38df5e2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>Основные сведения о манифесте приложения Azure Active Directory
Приложения, которые интегрируются с Azure Active Directory (AD), необходимо зарегистрировать в клиенте Azure AD. Таким образом для приложения обеспечивается постоянная конфигурация удостоверения. Эта конфигурация запрашивается в среде выполнения, в результате чего включаются сценарии, позволяющие приложению привлекать внешние ресурсы и посредников для проверки подлинности и авторизации в Azure AD. Дополнительные сведения о модели приложения Azure AD см. в статье о [добавлении, обновлении и удалении приложения][ADD-UPD-RMV-APP].

## <a name="updating-an-applications-identity-configuration"></a>Обновление конфигурации удостоверения для приложения
Существует несколько способов, с помощью которых можно обновить свойства конфигурации удостоверения для приложения. Они различаются как по возможностям, так и по степени сложности. Вот некоторые из них.

* С помощью пользовательского веб-интерфейса **[портала Azure][AZURE-PORTAL]** можно изменить только общие свойства приложения. Это самый быстрый и надежный способ, но он не обеспечивает полный доступ ко всем свойствам, в отличие от двух следующих методов.
* В более сложных ситуациях, когда вам необходимо изменить свойства, не отображающиеся на классическом портале Azure, можно изменить **манифест приложения**. Это и есть главная тема данной статьи. Более подробно она рассматривается в следующем разделе.
* Вы также можете **написать приложение, в котором для обновления используется [API Graph][GRAPH-API]**, но это самый сложный способ. Его удобно использовать, если вам нужно создать программное обеспечение для управления или если свойства приложения необходимо регулярно обновлять в автоматическом режиме.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Обновление конфигурации удостоверения для приложения с помощью манифеста приложения
На [портале Azure][AZURE-PORTAL] можно управлять конфигурацией удостоверения приложения, обновляя его манифест с помощью встроенного редактора манифестов. Можно также скачать и передать манифест приложения как JSON-файл. В каталоге нет фактически существующих файлов. Манифест приложения является просто операцией HTTP GET с сущностью приложения API Graph в Azure AD, а отправка — операцией HTTP PATCH с сущностью приложения.

Поэтому, чтобы получить общие сведения о формате и свойствах манифеста приложения, см. документацию по [сущностям приложений][APPLICATION-ENTITY] API Graph. Вот некоторые примеры изменений, которые можно выполнить с помощью отправки манифеста приложения.

* **Объявление областей разрешений (oauth2Permissions)**, предоставляемых через веб-API. Прочитайте раздел, посвященный предоставлению веб-API в других приложениях, в статье [Интеграция приложений с Azure Active Directory][INTEGRATING-APPLICATIONS-AAD]. Там описано, как реализовать олицетворение пользователя с помощью области делегированных разрешений oauth2Permissions. Как упоминалось ранее, все свойства сущности приложения, включая свойство oauth2Permissions, которое является коллекцией типа [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION], описаны в справочной статье по [сущностям и сложным типам][APPLICATION-ENTITY] API Graph.
* **Объявление ролей приложения (appRoles), предоставляемых приложением**. Свойство appRoles сущности приложения — это коллекция типа [AppRole][APPLICATION-ENTITY-APP-ROLE]. Пример реализации см. в статье, посвященной [контролю доступа на основе ролей в облачных приложениях с помощью Azure AD][RBAC-CLOUD-APPS-AZUREAD].
* **Объявление известных клиентских приложений (knownClientApplications)**, которые позволяют логически привязать согласие приложений указанного клиента к ресурсу или веб-API.
* **Отправка в Azure AD запроса на выдачу утверждения о членстве в группах** пользователю, выполнившему вход (groupMembershipClaims).  Можно также настроить выдачу утверждений о ролях пользователя в каталоге. Пример реализации см. в статье, посвященной [авторизации в облачных приложениях с помощью групп AD][AAD-GROUPS-FOR-AUTHORIZATION].
* **Включение поддержки потоков неявного разрешения OAuth 2.0 (oauth2AllowImplicitFlow) в приложении**. Этот тип потоков доступа используется на страницах со встроенным JavaScript или в одностраничных приложениях (SPA). Дополнительные сведения о неявном предоставлении авторизации см. в статье с [общими сведениями о неявном потоке предоставления OAuth2 в Azure Active Directory][IMPLICIT-GRANT].
* **Разрешение использовать сертификаты X509 в качестве секретного ключа** (keyCredentials). Примеры реализации см. в статье, посвященной [созданию приложений служб и управляющих программ в Office 365][O365-SERVICE-DAEMON-APPS], и [руководстве разработчика по аутентификации с помощью API Azure Resource Manager][DEV-GUIDE-TO-AUTH-WITH-ARM].
* **Добавление нового универсального кода ресурса (URI) идентификатора приложения** (identifierURIs[]). Универсальные коды ресурсов (URI) идентификаторов приложений используются для уникальной идентификации приложения в рамках заданного клиента Azure AD (или нескольких клиентов Azure AD в соответствующих сценариях с определением через проверенный личный домен). Они используются при запросе разрешений или получения маркера доступа для приложения ресурсов. Обновление этого элемента выполняется одновременно с обновлением коллекции servicePrincipalNames[] соответствующего субъекта-службы, которая находится в основном клиенте приложения.

Манифест приложения также позволяет отслеживать состояние регистрации вашего приложения. Так как он доступен в формате JSON, представление файла можно добавить в систему управления версиями вместе с исходным кодом вашего приложения.

## <a name="step-by-step-example"></a>Пример подробными инструкциями
Теперь рассмотрим каждый этап изменения конфигурации удостоверения для приложения с помощью манифеста приложения. Мы воспользуемся одним из примеров, приведенных выше, и покажем, как объявить новую область разрешений для приложения ресурсов.

1. Войдите на [портал Azure][AZURE-PORTAL].
2. Пройдя аутентификацию, выберите клиент Azure AD, щелкнув его в правом верхнем углу страницы.
3. Выберите расширение **Azure Active Directory** на левой навигационной панели и щелкните **Регистрация приложений**.
4. Найдите в списке приложение, которое нужно изменить, и щелкните его.
5. Щелкните **Манифест** на странице приложения, чтобы открыть встроенный редактор манифестов. 
6. Можно напрямую изменить манифест с помощью этого редактора. Обратите внимание, что манифест следует схеме [сущности приложения][APPLICATION-ENTITY], как было упомянуто ранее. Например, предположим, что мы хотим реализовать и предоставить новое разрешение Employees.Read.All в нашем приложении-ресурсе (API). Для этого достаточно добавить новый или второй элемент в коллекцию oauth2Permissions, например:
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],
   
    Записи должны быть уникальными, поэтому необходимо создать новый глобальный уникальный идентификатор (GUID) для свойства `"id"` . Так как мы указали `"type": "User"`, это разрешение может быть предоставлено любой учетной записи, аутентифицированной в клиенте Azure AD, в котором зарегистрирован ресурс или приложение API. Это предоставляет клиентскому приложению разрешение на доступ к нему от имени этой учетной записи. Описание и отображаемое имя используются во время предоставления согласия, а также отображаются на портале Azure.
6. Завершив изменение манифеста, нажмите кнопку **Сохранить**, чтобы сохранить его.  
   
Теперь, когда манифест сохранен, можно предоставить зарегистрированному клиентскому приложению доступ к новому разрешению, которое мы добавили ранее. На этот раз можно воспользоваться пользовательским веб-интерфейсом портала Azure, а не изменять манифест клиентского приложения.  

1. Сначала перейдите в колонку **Параметры** клиентского приложения, которому необходимо настроить доступ к новому API, щелкните **Требуемые разрешения** и выберите **Выбор API**.
2. Вы увидите список зарегистрированных ресурсов приложения (API) клиента. Выберите приложение-ресурс или введите имя приложения в поле поиска. Если вы нашли приложение, нажмите кнопку **Выбрать**.  
3. Откроется страница **Выбор разрешений** со списком разрешений приложения и делегированных разрешений для приложения-ресурса. Щелкните новое разрешение, чтобы добавить его в запрошенный список разрешений клиента. Это новое разрешение будет храниться в конфигурации удостоверения клиентского приложения в свойстве коллекции requiredResourceAccess.


Вот и все. Теперь ваши приложения будут запускаться с новой конфигурацией удостоверений.

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о связи между объектами приложения и субъекта-службы см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory][AAD-APP-OBJECTS].
* Определения некоторых основных понятий для разработчиков Azure Active Directory (AD) см. в [глоссарии разработчика по Azure AD][AAD-DEVELOPER-GLOSSARY].

Оставляйте свои отзывы и предложения в разделе ниже. Они помогут нам улучшать содержимое веб-сайта.

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

