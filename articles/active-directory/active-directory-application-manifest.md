---
title: "Основные сведения о манифесте приложения Azure Active Directory | Документация Майкрософт"
description: "Подробное описание манифеста приложения Azure Active Directory, который представляет конфигурацию удостоверений приложения в клиенте Azure AD и используется для упрощения авторизации OAuth, предоставления согласия и многого другого."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/11/2016
ms.author: dkershaw;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e19d4bf530ca18dc39a57b36650ba7a394b826ce


---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>Основные сведения о манифесте приложения Azure Active Directory
Приложения, которые интегрируются с Azure Active Directory (AD), необходимо зарегистрировать в клиенте Azure AD. Таким образом для приложения обеспечивается постоянная конфигурация удостоверения. Эта конфигурация запрашивается в среде выполнения, в результате чего включаются сценарии, позволяющие приложению привлекать внешние ресурсы и посредников для проверки подлинности и авторизации в Azure AD. Дополнительные сведения о модели приложения Azure AD см. в статье [Добавление, обновление и удаление приложения][ADD-UPD-RMV-APP].

## <a name="updating-an-applications-identity-configuration"></a>Обновление конфигурации удостоверения для приложения
Существует несколько способов, с помощью которых можно обновить свойства конфигурации удостоверения для приложения. Они различаются как по возможностям, так и по степени сложности. Вот некоторые из них.

* На **[классическом портале Azure][AZURE-CLASSIC-PORTAL] с помощью пользовательского веб-интерфейса** можно изменить только общие свойства приложения. Это самый быстрый и надежный способ, но он не обеспечивает полный доступ ко всем свойствам, в отличие от двух следующих методов.
* В более сложных ситуациях, когда вам необходимо изменить свойства, не отображающиеся на классическом портале Azure, можно изменить **манифест приложения**. Это и есть главная тема данной статьи. Более подробно она рассматривается в следующем разделе.
* Вы также можете **[создать приложение, в котором для обновления используется API Graph][GRAPH-API]**. Это самый трудоемкий способ. Его удобно использовать, если вам нужно создать программное обеспечение для управления или если свойства приложения необходимо регулярно обновлять в автоматическом режиме.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Обновление конфигурации удостоверения для приложения с помощью манифеста приложения
Конфигурацией удостоверения приложения можно управлять через [классический портал Azure][AZURE-CLASSIC-PORTAL], скачивая и отправляя представление в виде JSON-файла, которое называется манифестом приложения. В каталоге нет фактически существующих файлов. Манифест приложения является просто операцией HTTP GET с сущностью приложения API Graph в Azure AD, а отправка — операцией HTTP PATCH с сущностью приложения.

Поэтому, чтобы получить общие сведения о формате и свойствах манифеста приложения, следует обратиться к документации по [сущностям приложений][APPLICATION-ENTITY] API Graph. Вот некоторые примеры изменений, которые можно выполнить с помощью отправки манифеста приложения.

* **Объявление областей разрешений (oauth2Permissions)**, предоставляемых через веб-API. Прочитайте раздел "Предоставление веб-API в других приложениях" в статье [Интеграция приложений с Azure Active Directory][INTEGRATING-APPLICATIONS-AAD], чтобы получить сведения о реализации олицетворения пользователя с помощью области делегированных разрешений oauth2Permissions. Как упоминалось ранее, свойства сущности приложения, в том числе свойство oauth2Permissions, которое является коллекцией типа [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION], описаны в справочнике по [сущностям и сложным типам][APPLICATION-ENTITY] API Graph.
* **Объявление ролей приложения (appRoles), предоставляемых приложением**. Свойство appRoles сущности приложения является коллекцией типа [AppRole][APPLICATION-ENTITY-APP-ROLE]. Пример реализации представлен в статье [Role based access control in cloud applications using Azure AD][RBAC-CLOUD-APPS-AZUREAD] (Управление доступом на основе ролей в облачных приложениях с помощью Azure AD).
* **Объявление известных клиентских приложений (knownClientApplications)**, которые позволяют логически привязать согласие приложений указанного клиента к ресурсу или веб-API.
* **Отправка в Azure AD запроса на выдачу утверждения о членстве в группах** пользователю, выполнившему вход (groupMembershipClaims).  Можно также настроить выдачу утверждений о ролях пользователя в каталоге. Пример реализации представлен в статье [Authorization in Cloud Applications using AD Groups][AAD-GROUPS-FOR-AUTHORIZATION] (Авторизация в облачных приложениях с помощью групп Active Directory).
* **Включение поддержки потоков неявного разрешения OAuth 2.0 (oauth2AllowImplicitFlow) в приложении**. Этот тип потоков доступа используется на страницах со встроенным JavaScript или в одностраничных приложениях (SPA). Дополнительные сведения о неявном предоставлении авторизации см. в статье [Общие сведения о неявном потоке предоставления OAuth2 в Azure Active Directory (AD)][IMPLICIT-GRANT].
* **Разрешение использовать сертификаты X509 в качестве секретного ключа** (keyCredentials). Примеры реализации см. в статьях [Build service and daemon apps in Office 365][O365-SERVICE-DAEMON-APPS] (Создание приложений служб и управляющих программ в Office 365) и [Developer’s guide to auth with Azure Resource Manager API][DEV-GUIDE-TO-AUTH-WITH-ARM] (Руководство разработчика по аутентификации с помощью API Azure Resource Manager).
* **Добавление нового универсального кода ресурса (URI) идентификатора приложения** (identifierURIs[]). Универсальные коды ресурсов (URI) идентификаторов приложений используются для уникальной идентификации приложения в рамках заданного клиента Azure AD (или нескольких клиентов Azure AD в соответствующих сценариях с определением через проверенный личный домен). Они используются при запросе разрешений или получения маркера доступа для приложения ресурсов. Обновление этого элемента выполняется одновременно с обновлением коллекции servicePrincipalNames[] соответствующего субъекта-службы, которая находится в основном клиенте приложения.

Манифест приложения также позволяет отслеживать состояние регистрации вашего приложения. Так как он доступен в формате JSON, представление файла можно добавить в систему управления версиями вместе с исходным кодом вашего приложения.

## <a name="step-by-step-example"></a>Пример подробными инструкциями
Теперь рассмотрим каждый этап изменения конфигурации удостоверения для приложения с помощью манифеста приложения. Мы воспользуемся одним из примеров, приведенных выше, и покажем, как объявить новую область разрешений для приложения ресурсов.

1. Перейдите на [классический портал Azure][AZURE-CLASSIC-PORTAL] и войдите с помощью учетной записи с привилегиями администратора или соадминистратора службы.
2. После проверки подлинности прокрутите страницу вниз и в области навигации слева выберите пункт Active Directory (1). Затем щелкните клиент Azure AD, в котором зарегистрировано приложение (2).
   
    ![Выбор клиента Azure AD][SELECT-AZURE-AD-TENANT]
3. Когда появится страница каталога, щелкните в верхней части страницы пункт "Приложения" (1), чтобы открыть список зарегистрированных на клиенте приложений. Найдите в списке приложение, которое нужно изменить, и щелкните его (2).
   
    ![Выбор клиента Azure AD][SELECT-AZURE-AD-APP]
4. В самом низу главной страницы приложения обратите внимание на кнопку "Управление манифестом" (1). Если щелкнуть ее, вам будет предложено скачать или отправить JSON-файл манифеста. Щелкните "Загрузить манифест" (2). Когда появится запрос на подтверждение скачивания, щелкните "Загрузить манифест" (3), а затем откройте или сохраните файл на своем компьютере (4).
   
    ![Управление манифестом, скачивание][MANAGE-MANIFEST-DOWNLOAD]
   
    ![Скачивание манифеста][DOWNLOAD-MANIFEST]
5. Мы сохранили JSON-файл локально, чтобы иметь возможность открыть его в редакторе, внести изменения и сохранить их. Вот как выглядит структура JSON в редакторе Visual Studio. Обратите внимание, что в файле используется схема [сущности приложения][APPLICATION-ENTITY], о чем мы упоминали ранее.
   
    ![Изменение JSON-файла с манифестом][UPDATE-MANIFEST]
   
    Например, предположим, что мы хотим реализовать и открыть новое разрешение под названием Employees.Read.All в ресурсах нашего приложения (API). Для этого достаточно добавить новый или второй элемент в коллекцию oauth2Permissions, например:
   
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
   
    Записи должны быть уникальными, поэтому необходимо создать новый глобальный уникальный идентификатор (GUID) для свойства `"id"` . Так как мы указали `"type": "User"`, это разрешение может быть предоставлено любой учетной записи, аутентифицированной в клиенте Azure AD, в котором зарегистрирован ресурс или приложение API. Это предоставляет клиентскому приложению разрешение на доступ к нему от имени этой учетной записи. Описание и отображаемое имя используются во время предоставления согласия, а также отображаются в классическом портале Azure.
6. Внеся нужные изменения в манифест, вернитесь на страницу приложения Azure AD на классическом портале Azure и снова щелкните "Управление манифестом" (1). Но на этот раз щелкните "Отправить манифест" (2). В появившемся окне укажите расположение JSON-файла: Нажмите кнопку "Обзор файла..." (3), в окне "Выберите файл для передачи" выберите JSON-файл (4) и нажмите кнопку "Открыть". Когда диалоговое окно исчезнет, нажмите кнопку "ОК" (5).  
   
    ![Управление манифестом, отправка][MANAGE-MANIFEST-UPLOAD]
   
    ![Отправка JSON-файла с манифестом][UPLOAD-MANIFEST]
   
    ![Отправка JSON-файла с манифестом, подтверждение][UPLOAD-MANIFEST-CONFIRM]

Теперь, когда манифест сохранен, можно предоставить зарегистрированному клиентскому приложению доступ к новому разрешению, которое мы добавили ранее. На этот раз можно воспользоваться пользовательским веб-интерфейсом классического портала Azure, а не изменять манифест клиентского приложения.  

1. Перейдите на страницу "Настройка" клиентского приложения, для которого необходимо добавить доступ к новому API, и нажмите кнопку "Добавить приложение".
2. Вы увидите список зарегистрированных ресурсов приложения (API) клиента. Нажмите кнопку "плюс" рядом с названием ресурса приложения, чтобы выбрать ресурс.  
3. Затем установите флажок в правом нижнем углу.
4. После возврата в раздел "Добавление приложения" страницы конфигурации клиента вы увидите новое приложение в списке. Переместите курсор мыши в раздел "Делегированные разрешения" справа от этой строки, и вы увидите раскрывающийся список. Щелкните список, а затем выберите новое разрешение, чтобы добавить его в запрошенный список разрешений клиента. Примечание: это новое разрешение будет храниться в конфигурации удостоверения клиентского приложения в свойстве коллекции requiredResourceAccess.

![Разрешения для других приложений][PERMS-TO-OTHER-APPS]

![Разрешения для других приложений][PERMS-SELECT-APP]

![Разрешения для других приложений][PERMS-SELECT-PERMS]

Вот и все. Теперь ваши приложения будут запускаться с новой конфигурацией удостоверений.

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о связи между объектами приложения и субъекта-службы см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory][AAD-APP-OBJECTS].
* Определения некоторых основных понятий для разработчиков Azure Active Directory (AAD) приведены в [глоссарии Azure Active Directory для разработчика][AAD-DEVELOPER-GLOSSARY].

Оставляйте свои замечания и пожелания в разделе DISQUS ниже. Они помогают нам улучшать содержимое веб-сайта.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/




<!--HONumber=Nov16_HO3-->


