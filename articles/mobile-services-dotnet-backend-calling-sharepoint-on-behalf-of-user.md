<properties urlDisplayName="Access SharePoint on behalf of the user" pageTitle="Доступ к SharePoint от имени пользователя | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to make calls to SharePoint on behalf of the user" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Access SharePoint on behalf of the user" authors="mahender" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="mahender" />

# Доступ к SharePoint от имени пользователя

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показано, как получить доступ к интерфейсам API SharePoint от имени текущего пользователя, который вошел в систему.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике. В представленном видео Мэт Веллозо (Mat Velloso) поясняет процесс обновления приложения в магазине Windows для взаимодействия с SharePoint Online.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('http://media.ch9.ms/ch9/f217/3f8cbf94-f36b-4162-b3da-1c00339ff217/AzureMobileServicesAADO365AuthenticationIdentityA_960.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">12:51:00</span></div>
</div>

В этом учебнике будет показана процедура обновления приложения из учебника "Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory" для создания документа Word в SharePoint Online, когда добавляется новый элемент TodoItem.

В этом учебнике рассматривается процедура разрешения доступа к SharePoint от имени пользователя:

1. [Регистрация своего приложения для делегированного доступа к SharePoint]
2. [Добавление информации SharePoint к своей мобильной службе]
3. [Получение токена доступа и вызов SharePoint API]
4. [Создание и загрузка документа Word]
5. [Тестирование приложения]

Для работы с данным учебником требуется следующее:

* Пакет Visual Studio 2013, работающий в среде Windows 8.1.
* Активная подписка на [SharePoint Online] 
* Успешное ознакомление с учебником [Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory]. Необходимо использовать клиент, доступный через подписку SharePoint.

## <a name="configure-permissions"></a>Регистрация своего приложения для делегированного доступа к SharePoint
По умолчанию токен, полученный из AAD, имеет ограниченные права. Чтобы получить доступ к сторонним ресурсам или приложению SaaS, например SharePoint Online, такой доступ необходимо явным образом разрешить.

1. В разделе **Active Directory** на [портале управления Azure] выберите свой клиент. Перейдите в веб-приложение, созданное для мобильной службы.

    ![][0]

2. На вкладке **Настройка** прокрутите страницу вниз до раздела прав для других приложений. Выберите **Office 365 SharePoint Online** и назначьте делегированное разрешение **Редактирование или удаление файлов пользователей**. Затем щелкните **Сохранить**.

    ![][1]

Вы настроили AAD на выдачу токена доступа SharePoint к мобильной службе.

## <a name="store-credentials"></a>Добавление информации SharePoint к своей мобильной службе

Для отправки вызова в SharePoint необходимо указать конечные точки, с которыми должна будет общаться мобильная служба. Вам также потребуется подтвердить идентичность своей мобильной службы. Это выполняется с помощью пары "идентификатор клиента - секрет клиента". Вы уже получили и сохранили идентификатор клиента для мобильной службы при настройке входа в систему AAD. Так как это конфиденциальные учетные данные, не храните их в виде открытого текста в своем коде. Вместо этого задайте эти значения как параметры приложения для своей мобильной службы.

1. Вернитесь на вкладку "Приложения AAD" для своего клиента и выберите веб-приложение для своей мобильной службы.

2. В разделе "Настройка" прокрутите вниз до раздела "Ключи". Секрет клиента вы получите путем формирования нового ключа. Следует отметить, что после создания ключа и выхода с данной страницы вы больше никак не сможете снова получить данную информацию из портала. После создания секрета это значение необходимо скопировать и сохранить в надежном месте. Выберите срок действия для своего ключа, а затем щелкните "Сохранить" и скопируйте результирующее значение.

    ![][2]

3. В разделе "Мобильные службы" на портале управления перейдите на вкладку "Настройка" и прокрутите вниз до параметров приложения. Здесь можно ввести пару "ключ-значение", которая позволит ссылаться на необходимые учетные данные.

    ![][3]

4. ведите SP_Authority в качестве конечной точки центра для своего клиента AAD. Это значение должно совпадать со значением центра, используемым для вашего клиентского приложения. Значение будет представлено в формате https://login.windows.net/contoso.onmicrosoft.com

5. Введите SP_ClientSecret в качестве значения секрета клиента, которое вы получили ранее.

6. Введите SP_SharePointURL в качестве URL-адреса для своего сайта SharePoint. Это значение должно быть представлено в формате https://contoso-my.sharepoint.com

Эти значения можно будет получить в коде снова с помощью ApiServices.Settings.

## <a name="obtain-token"></a>Получение токена доступа и вызов SharePoint API

Для доступа к SharePoint необходимо иметь специальный токен доступа с SharePoint в качестве целевой аудитории. Чтобы получить этот токен, нужно отправить обратный вызов в службу AAD с идентификатором мобильной службы и токеном, который был выдан для пользователя.

1. Откройте свой серверный проект мобильных служб в Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

2. В своем серверном проекте мобильных служб создайте новый класс с именем SharePointUploadContext. Добавьте в него следующую информацию:

        private String accessToken;
        private String mySiteApiPath;
        private String clientId;
        private String clientSecret;
        private String sharepointURL;
        private String authority;
        private const string getFilesPath = "/getfolderbyserverrelativeurl('Documents')/Files";

        public static async Task<SharePointUploadContext> createContext(ServiceUser user, ServiceSettingsDictionary settings)
        {
            //Get the current access token
            AzureActiveDirectoryCredentials creds = (await user.GetIdentitiesAsync()).OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            string userToken = creds.AccessToken;
            return new SharePointUploadContext(userToken, settings);
        }

        private SharePointUploadContext(string userToken, ServiceSettingsDictionary settings)
        {
            //Call ADAL and request a token to SharePoint with the access token
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new UserAssertion(userToken), new ClientCredential(clientId, clientSecret));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web"; 
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

3. Теперь создайте метод для добавления файла в библиотеку документов пользователя:

        public async Task<bool> UploadDocument(string docName, byte[] document)
        {
            string uploadUri = sharepointURL + mySiteApiPath + getFilesPath + string.Format(@"/Add(url='{0}.docx', overwrite=true)", docName);
            using (HttpClient client = new HttpClient())
            {
                Func<HttpRequestMessage> requestCreator = () =>
                {
                    HttpRequestMessage UploadRequest = new HttpRequestMessage(HttpMethod.Post, uploadUri);
                    UploadRequest.Content = new System.Net.Http.ByteArrayContent(document);
                    UploadRequest.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                    UploadRequest.Content.Headers.ContentType.Parameters.Add(new NameValueHeaderValue("odata", "verbose"));
                    return UploadRequest;
                };
                using (HttpRequestMessage uploadRequest = requestCreator.Invoke())
                {
                    uploadRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
                    HttpResponseMessage uploadResponse = await client.SendAsync(uploadRequest);
                }
            }
            return true;
        }

## <a name="create-document"></a>Создание и загрузка документа Word

Для создания документа Word необходимо использовать пакет OpenXML NuGet. Чтобы установить этот пакет, откройте диспетчер NuGet и найдите DocumentFormat.OpenXml.

1. Добавьте следующий код в TodoItemController. В результате будет создан документ Word на основе элемента TodoItem. В качестве текста документа будет использоваться имя элемента.

        private static byte[] CreateWordDocument(TodoItem todoItem)
        {
            byte[] document;
            using (MemoryStream generatedDocument = new MemoryStream())
            {
                using (WordprocessingDocument package = WordprocessingDocument.Create(generatedDocument, WordprocessingDocumentType.Document))
                {
                    MainDocumentPart mainPart = package.MainDocumentPart;
                    if (mainPart == null)
                    {
                        mainPart = package.AddMainDocumentPart();
                        new Document(new Body()).Save(mainPart);
                    }
                    Body body = mainPart.Document.Body;
                    Paragraph p =
                        new Paragraph(
                            new Run(
                                new Text(todoItem.Text)));
                    body.Append(p);
                    mainPart.Document.Save();
                }
                document = generatedDocument.ToArray();
            }
            return document;
        }

2. Замените PostTodoItem на следующую информацию:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            
            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);
            
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>Тестирование приложения

1. Опубликуйте изменения для серверной части, а затем запустите свое клиентское приложение. В ответ на приглашение системы выполните вход и введите новый TodoItem.

2. Перейдите на свой сайт SharePoint и войдите с тем же именем пользователем.

3. Перейдите на вкладку "OneDrive". В папке документов должен появиться документ Word с названием GUID. При ее открытии появится текст для TodoItem.

    ![][4]


<!-- Images. -->

[0]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-web-application.png
[1]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-sharepoint-permissions.png
[2]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-manage-secret-key.png
[3]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/mobile-services-app-settings-sharepoint.png
[4]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/sharepoint-document-created.png

<!-- Anchors. -->

[Регистрация своего приложения для делегированного доступа к SharePoint]: #configure-permissionss
[Добавление информации SharePoint к своей мобильной службе]: #store-credentials
[Получение токена доступа и вызов SharePoint API]: #obtain-token
[Создание и загрузка документа Word]: #create-document
[Тестирование приложения]: #test-application

<!-- URLs. -->
[Портал управления Azure]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/ru-ru/sharepoint/
[Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
