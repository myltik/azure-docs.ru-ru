<properties
   pageTitle="Основные сведения о манифесте приложения Azure Active Directory | Microsoft Azure"
   description="Дополнительные сведения о манифесте приложения Azure AD, который сопровождает каждую конфигурацию приложения в клиенте Azure AD."
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
   ms.date="09/03/2015"
   ms.author="dkershaw;bryanla"/>

# Основные сведения о манифесте приложения Azure Active Directory
Приложения, которые интегрируются с Azure Active Directory (AD), необходимо зарегистрировать в клиенте Azure AD. Таким образом для приложения обеспечивается постоянная конфигурация удостоверения. Эта конфигурация запрашивается в среде выполнения, в результате чего включаются сценарии, позволяющие приложению привлекать внешние ресурсы и посредников для проверки подлинности и авторизации в Azure AD. Дополнительные сведения о модели приложения Azure AD см. в статье [Добавление, обновление и удаление приложения][ADD-UPD-RMV-APP].

## Обновление конфигурации удостоверения для приложения

Существует несколько способов, с помощью которых можно обновить свойства конфигурации удостоверения для приложения. Они различаются как по возможностям, так и по степени сложности. Вот некоторые из них.

- На **[портале Azure][AZURE-PORTAL]** можно изменять только общие свойства приложения. Это самый быстрый и надежный способ, но он не обеспечивает полный доступ ко всем свойствам, в отличие от двух следующих методов.
- В более сложных ситуациях, когда вам необходимо изменить свойства, не отображающиеся на портале Azure, можно изменить **манифест приложения**. Это и есть главная тема данной статьи. Более подробно она рассматривается в следующем разделе.
- Можно также **[написать приложение, в котором для обновления приложения используется API Graph][GRAPH-API]**. Это самый трудоемкий из способов. Его удобно использовать, если вам нужно создать программное обеспечение для управления или если свойства приложения необходимо регулярно обновлять в автоматическом режиме.

## Обновление конфигурации удостоверения для приложения с помощью манифеста приложения
Конфигурацией удостоверения приложения можно управлять через [портал Azure][AZURE-PORTAL], скачивая и отправляя представление JSON-файла, которое называется манифестом приложения. В каталоге нет фактически существующих файлов — манифест приложения является просто операцией HTTP GET в сущности приложения с API Graph в Azure AD, а отправка — операцией HTTP PATCH в сущности приложения.

Поэтому, чтобы получить общие сведения о формате и свойствах манифеста приложения, следует обратиться к документации по [сущностям приложений][APPLICATION-ENTITY] с API Graph. Вот некоторые примеры изменений, которые можно выполнить с помощью отправки манифеста приложения.

- Объявление областей разрешений (oauth2Permissions), предоставляемых посредством веб-API. Сведения о реализации олицетворения пользователя с помощью делегированной области разрешений oauth2Permissions см. в статье «Предоставление веб-API в других приложениях» в разделе [Интеграция приложений с Azure Active Directory][INTEGRATING-APPLICATIONS-AAD]. Как упоминалось ранее, все свойства сущности приложения, в том числе область oauth2Permissions, которая принадлежит к типу [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION], описаны в справочной статье по API Graph [Справка по сущностям и сложным типам][APPLICATION-ENTITY].
- Объявление ролей приложения (appRoles), предоставляемых приложением. Пример реализации см. в статье [Контроль доступа на основе ролей в облачных приложениях с помощью Azure AD][RBAC-CLOUD-APPS-AZUREAD].
- Объявление известных клиентских приложений.
- Отправка запроса в Azure AD на выдачу утверждения о членстве в группах пользователю, выполнившему вход. ПРИМЕЧАНИЕ. Вы можете настроить дополнительную выдачу утверждений о ролях пользователя в каталоге. Пример реализации см. в статье [Авторизация в облачных приложениях с помощью групп AD][AAD-GROUPS-FOR-AUTHORIZATION].
- Разрешение приложению поддерживать потоки неявного доступа OAuth 2.0 (для внедренных веб-страниц JavaScript или приложений SPA).
- Разрешение использовать сертификаты X509 как секретный ключ. Пример реализации см. в статье [Создание сервисных и управляющих программ в Office 365][O365-SERVICE-DAEMON-APPS]. 

Манифест приложения также позволяет отслеживать состояние регистрации вашего приложения. Так как он доступен в формате JSON, представление файла можно добавить в систему управления версиями вместе с исходным кодом вашего приложения.

### Пример подробными инструкциями
Теперь давайте рассмотрим каждый этап изменения конфигурации удостоверения для приложения с помощью манифеста.

1. Перейдите на [портал Azure][AZURE-PORTAL] и войдите в учетную запись, имеющую права администратора или соадминистратора службы.


2. После проверки подлинности прокрутите страницу вниз и в области навигации слева выберите пункт Active Directory (1). Затем щелкните клиент Azure AD, в котором зарегистрировано приложение (2).

	![Выбор клиента Azure AD][SELECT-AZURE-AD-TENANT]


3. Когда появится страница каталога, щелкните в верхней части страницы пункт «Приложения» (1), чтобы открыть список зарегистрированных на клиенте приложений. Найдите в списке приложение, которое нужно изменить, и щелкните его (2).

	![Выбор клиента Azure AD][SELECT-AZURE-AD-APP]


4. В самом низу главной страницы приложения обратите внимание на кнопку «Управление манифестом» (1). Если щелкнуть ее, вам будет предложено скачать или отправить JSON-файл манифеста. Щелкните «Загрузить манифест» (2). Когда появится запрос на подтверждение скачивания, щелкните «Загрузить манифест» (3), а затем откройте или сохраните файл на своем компьютере (4).

	![Управление манифестом, скачивание][MANAGE-MANIFEST-DOWNLOAD]

	![Скачивание манифеста][DOWNLOAD-MANIFEST]


5. Мы сохранили JSON-файл локально, чтобы иметь возможность открыть его в редакторе, внести изменения и сохранить их. Вот как выглядит структура JSON в редакторе Visual Studio. Обратите внимание, что в файле используется схема [сущности приложения][APPLICATION-ENTITY], о чем мы упоминали ранее.

	![Изменение JSON-файла с манифестом][UPDATE-MANIFEST]


6. Внеся нужные изменения в манифест, вернитесь на страницу приложения Azure AD на портале Azure, нажмите кнопку «Управление манифестом» (1) и выберите пункт «Отправить манифест» (2). В появившемся окне укажите расположение JSON-файла: Нажмите кнопку «Обзор файла...» (3), в окне «Выберите файл для передачи» выберите JSON-файл (4) и нажмите кнопку «Открыть». Когда диалоговое окно исчезнет, нажмите кнопку «ОК» (5).

	![Управление манифестом, отправка][MANAGE-MANIFEST-UPLOAD]

	![Отправка JSON-файла с манифестом][UPLOAD-MANIFEST]

	![Отправка JSON-файла с манифестом, подтверждение][UPLOAD-MANIFEST-CONFIRM]

Вот и все. Теперь ваше приложение будет работать с новой конфигурацией, в основе которой лежат ваши изменения в манифесте.

## Дальнейшие действия
Оставляйте свои замечания и пожелания в разделе DISQUS ниже. Они помогают нам улучшать содержимое веб-сайта.

<!--Image references-->
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: https://msdn.microsoft.com/library/azure/dn132599.aspx
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-PORTAL]: https://manage.windowsazure.com
[GRAPH-API]: https://msdn.microsoft.com/library/azure/hh974476.aspx
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=Sept15_HO2-->