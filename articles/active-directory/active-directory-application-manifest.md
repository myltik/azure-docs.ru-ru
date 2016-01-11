<properties
   pageTitle="Основные сведения о манифесте приложения Azure Active Directory | Microsoft Azure"
   description="Подробное описание использования манифеста приложения Azure Active Directory, который представляет конфигурацию удостоверения приложения в клиенте Azure AD и используется для упрощения авторизации OAuth, предоставления согласия и многого другого."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/18/2015"
   ms.author="dkershaw;bryanla"/>

# Основные сведения о манифесте приложения Azure Active Directory

Приложения, которые интегрируются с Azure Active Directory (AD), необходимо зарегистрировать в клиенте Azure AD. Таким образом для приложения обеспечивается постоянная конфигурация удостоверения. Эта конфигурация запрашивается в среде выполнения, в результате чего включаются сценарии, позволяющие приложению привлекать внешние ресурсы и посредников для проверки подлинности и авторизации в Azure AD. Дополнительные сведения о модели приложения Azure AD см. в статье [Добавление, обновление и удаление приложения][ADD-UPD-RMV-APP].

## Обновление конфигурации удостоверения для приложения

Существует несколько способов, с помощью которых можно обновить свойства конфигурации удостоверения для приложения. Они различаются как по возможностям, так и по степени сложности. Вот некоторые из них.

- На **[классическом портале Azure][AZURE-CLASSIC-PORTAL]** можно изменять только общие свойства приложения. Это самый быстрый и надежный способ, но он не обеспечивает полный доступ ко всем свойствам, в отличие от двух следующих методов.
- В более сложных ситуациях, когда вам необходимо изменить свойства, не отображающиеся на классическом портале Azure, можно изменить **манифест приложения**. Это и есть главная тема данной статьи. Более подробно она рассматривается в следующем разделе.
- Вы также можете **[написать приложение, в котором для обновления используется API Graph][GRAPH-API]**. Это самый трудоемкий способ. Его удобно использовать, если вам нужно создать программное обеспечение для управления или если свойства приложения необходимо регулярно обновлять в автоматическом режиме.

## Обновление конфигурации удостоверения для приложения с помощью манифеста приложения
Конфигурацией удостоверения приложения можно управлять через [классический портал Azure][AZURE-CLASSIC-PORTAL], скачивая и отправляя представление JSON-файла, которое называется манифестом приложения. В каталоге нет фактически существующих файлов — манифест приложения является просто операцией HTTP GET в сущности приложения с API Graph в Azure AD, а отправка — операцией HTTP PATCH в сущности приложения.

Поэтому, чтобы получить общие сведения о формате и свойствах манифеста приложения, следует обратиться к документации по [сущностям приложений][APPLICATION-ENTITY] с API Graph. Вот некоторые примеры изменений, которые можно выполнить с помощью отправки манифеста приложения.

- Объявление областей разрешений (oauth2Permissions), предоставляемых посредством веб-API. Сведения о реализации олицетворения пользователя с помощью делегированной области разрешений oauth2Permissions см. в статье "Предоставление веб-API в других приложениях" в разделе [Интеграция приложений с Azure Active Directory][INTEGRATING-APPLICATIONS-AAD]. Как упоминалось ранее, все свойства сущности приложения, в том числе свойство oauth2Permissions, которое является коллекцией типа [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION], описаны в справочной статье по API Graph [Справка по сущностям и сложным типам][APPLICATION-ENTITY].
- Объявление ролей приложения (appRoles), предоставляемых приложением. Свойство appRoles сущности приложения является коллекцией типа [AppRole][APPLICATION-ENTITY-APP-ROLE]. Пример реализации см. в статье [Контроль доступа на основе ролей в облачных приложениях с помощью Azure AD][RBAC-CLOUD-APPS-AZUREAD].
- Объявите известные клиентские приложения (knownClientApplications), которые позволяют логически привязать согласие приложений указанного клиента к ресурсу или веб-API.
- Отправьте в Azure AD запрос на выдачу утверждения о членстве в группах пользователю, выполнившему вход (groupMembershipClaims). ПРИМЕЧАНИЕ. Вы можете настроить дополнительную выдачу утверждений о ролях пользователя в каталоге. Пример реализации см. в статье [Авторизация в облачных приложениях с помощью групп AD][AAD-GROUPS-FOR-AUTHORIZATION].
- Разрешите поддержку потоков неявного доступа OAuth 2.0 (oauth2AllowImplicitFlow) в приложении. Этот тип потоков доступа используется на страницах со встроенным JavaScript или в одностраничных приложениях (SPA).
- Разрешите использовать сертификаты X509 в качестве секретного ключа (keyCredentials). Примеры реализации см. в статьях [Создание приложений служб и управляющих программ в Office 365][O365-SERVICE-DAEMON-APPS] и [Руководство разработчика по аутентификации с помощью API диспетчера ресурсов Azure][DEV-GUIDE-TO-AUTH-WITH-ARM].

Манифест приложения также позволяет отслеживать состояние регистрации вашего приложения. Так как он доступен в формате JSON, представление файла можно добавить в систему управления версиями вместе с исходным кодом вашего приложения.

## Пример подробными инструкциями
Теперь рассмотрим каждый этап изменения конфигурации удостоверения для приложения с помощью манифеста приложения. Мы воспользуемся одним из примеров, приведенных выше, и покажем, как объявить новую область разрешений для ресурса приложения:

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

    Записи должны быть уникальными, поэтому необходимо создать новый глобальный уникальный идентификатор (GUID) для свойства `"id"`. Так как мы указали `"type": "User"`, это разрешение может быть использовано любой учетной записью, прошедшей проверку подлинности клиента Azure AD, в котором зарегистрирован ресурс или приложение API, предоставляя клиентскому приложению разрешение на доступ к нему от имени учетной записи. Описание и отображаемое имя используются во время предоставления согласия, а также отображаются в классическом портале Azure.

6. Внеся нужные изменения в манифест, вернитесь на страницу приложения Azure AD на классическом портале Azure, снова щелкните «Управление манифестом» (1), но на этот раз щелкните пункт «Отправить манифест» (2). В появившемся окне укажите расположение JSON-файла: Нажмите кнопку "Обзор файла..." (3), в окне "Выберите файл для передачи" выберите JSON-файл (4) и нажмите кнопку "Открыть". Когда диалоговое окно исчезнет, нажмите кнопку "ОК" (5).

    ![Управление манифестом, отправка][MANAGE-MANIFEST-UPLOAD]

    ![Отправка JSON-файла с манифестом][UPLOAD-MANIFEST]

    ![Отправка JSON-файла с манифестом, подтверждение][UPLOAD-MANIFEST-CONFIRM]

Теперь, когда манифест сохранен, вы можете предоставить зарегистрированному клиентскому приложению доступ к новому разрешению, которое мы добавили выше, но на этот раз вместо изменения манифеста приложения клиента вы можете воспользоваться веб-интерфейсом классического портала Azure:

1. Перейдите на страницу "Настройка" клиентского приложения, для которого необходимо добавить доступ к новому API, и нажмите кнопку "Добавить приложение".
2. Вы увидите список зарегистрированных ресурсов приложения (API) клиента. Нажмите кнопку "плюс" рядом с названием ресурса приложения, чтобы выбрать ресурс.  
3. Затем установите флажок в правом нижнем углу. 
4. После возврата в раздел "Добавление приложения" страницы конфигурации клиента вы увидите новое приложение в списке. Переместите курсор мыши в раздел "Делегированные разрешения" справа от этой строки, и вы увидите раскрывающийся список. Щелкните список, а затем выберите новое разрешение, чтобы добавить его в запрошенный список разрешений клиента. Примечание: это новое разрешение будет храниться в конфигурации удостоверения клиентского приложения в свойстве коллекции requiredResourceAccess.

![Разрешения для других приложений][PERMS-TO-OTHER-APPS]

![Разрешения для других приложений][PERMS-SELECT-APP]

![Разрешения для других приложений][PERMS-SELECT-PERMS]

Вот и все. Теперь ваши приложения будут запускаться с новой конфигурацией удостоверений.

## Дальнейшие действия
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
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=AcomDC_1223_2015-->