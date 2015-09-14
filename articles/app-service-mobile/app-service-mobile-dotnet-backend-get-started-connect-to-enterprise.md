<properties
	pageTitle="Подключение мобильного приложения к корпоративному решению SaaS | Microsoft Azure"
	description="Выполнение вызовов в корпоративные ресурсы, такие как SharePoint Online"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="06/19/2015"
	ms.author="mahender"/>

# Подключение мобильного приложения к API SaaS

В этом учебнике вы подключите мобильное приложение к корпоративному решению SaaS. Вы будете обновлять приложение из раздела [Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Azure Active Directory], чтобы создавать документ Microsoft Word в SharePoint Online при каждом добавлении нового элемента TodoItem.

Для работы с данным учебником требуется следующее:

* Пакет Visual Studio 2013, работающий в среде Windows 8.1.
* Активная подписка на [SharePoint Online]
* Успешное ознакомление с учебником Выполнение [Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory]. Необходимо использовать клиент, предоставленный подпиской на SharePoint.

## <a name="configure-permissions"></a>Регистрация своего приложения для делегированного доступа к SharePoint
По умолчанию, токен, который вы получаете из AAD, имеет ограниченные права. Для того, чтобы получить доступ к сторонним ресурсам или приложению SaaS, например, SharePoint Online, такой доступ необходимо явным образом разрешить.

1. В разделе **Active Directory** на [портале управления Azure] выберите свой клиент. Перейдите к веб-приложению, созданному для службы приложений.

2. На вкладке **Настройка** прокрутите страницу вниз до раздела прав для других приложений. Выберите **Office 365 SharePoint Online** и назначьте делегированное разрешение **Редактирование или удаление файлов пользователей**. Нажмите кнопку **Сохранить**.

    ![][1]

Теперь вы настроили в Azure AD выдачу маркера доступа SharePoint службе приложений.

## <a name="store-credentials"></a>Добавление сведений SharePoint в мобильное приложение

Чтобы выполнить вызов в SharePoint, необходимо указать конечные точки, к которым должно обращаться мобильное приложение. Также требуется возможность подтвердить удостоверение службы приложений. Это выполняется с помощью пары «идентификатор клиента - секрет клиента». Вы уже получили и сохранили идентификатор клиента службы приложений во время настройки входа AAD. Так как это конфиденциальные учетные данные, не храните их в виде открытого текста в своем коде. Вместо этого настройте данные значения как параметры приложения для сайта кода мобильного приложения.

1. Вернитесь на вкладку «Приложения AAD» клиента и выберите веб-приложение для службы приложений.

2. В разделе «Настройка» прокрутите вниз до раздела «Ключи». Секрет клиента вы получите, сформировав новый ключ. Следует отметить, что после создания ключа и выхода с данной страницы вы больше никак не сможете снова получить данную информацию из портала. После создания секрета это значение необходимо скопировать и сохранить в надежном месте. Выберите срок действия для своего ключа, а затем щелкните «Сохранить» и скопируйте результирующее значение.

3. В разделе «Код мобильного приложения» портала управления перейдите на вкладку «Настройка» и прокрутите экран вниз, к параметрам приложения. Здесь можно ввести пару «ключ - значение», которая позволит ссылаться на необходимые учетные данные.

* Введите SP\_Authority в качестве конечной точки центра для своего клиента AAD. Это значение должно совпадать со значением центра, используемым для вашего клиентского приложения. Значение будет представлено в формате `https://login.windows.net/contoso.onmicrosoft.com`.

* Введите SP\_ClientSecret в качестве значения секрета клиента, которое вы получили ранее.

* Введите SP\_SharePointURL в качестве URL-адреса для своего сайта SharePoint. Это значение должно быть представлено в формате `https://contoso-my.sharepoint.com`.

Вы сможете повторно получить эти значения в коде с помощью ApiServices.Settings.

## <a name="obtain-token"></a>Получение маркера доступа и вызов API SharePoint

Для доступа к SharePoint необходимо иметь специальный токен доступа с SharePoint в качестве целевой аудитории. Чтобы получить этот маркер, потребуется выполнить обратный вызов Azure AD с удостоверением службы приложений и маркером, выданным пользователю.

1. Откройте проект кода мобильного приложения в Visual Studio.

[AZURE.INCLUDE [app-service-mobile-dotnet-adal-install-nuget](../../includes/app-service-mobile-dotnet-adal-install-nuget.md)]

2. В диспетчере пакетов NuGet щелкните **В сети**. Введите условие поиска **Microsoft.Azure.Mobile.Server.AppService**. Нажмите кнопку **Установить**, чтобы установить пакет [Расширение службы приложений серверной части .NET мобильных приложений]. Этот пакет содержит методы расширения для работы со сведениями о пользователе, в текущий момент вошедшем в систему.

2. В проекте кода мобильного приложения создайте новый класс с именем SharePointUploadContext. Добавьте в этот файл инструкцию `using Microsoft.Azure.Mobile.Server.AppService;`. Затем добавьте в класс следующий код:

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
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new ClientCredential(clientId, clientSecret), new UserAssertion(userToken));
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

## <a name="create-document"></a>Создание и добавление документа Word

Для создания документа Word необходимо использовать пакет OpenXML NuGet. Для установки этого пакета откройте диспетчер NuGet и найдите DocumentFormat.OpenXml.

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

1. Опубликуйте изменения для серверной части, а затем запустите свое клиентское приложение. После запроса системы войдите в нее и введите новый TodoItem.

2. Перейдите на свой сайт SharePoint и войдите с тем же именем пользователем.

3. Перейдите на вкладку «OneDrive». В папке документов должен появиться документ Word с названием GUID. При его открытии вы должны увидеть текст для TodoItem.

<!-- Images. -->

[1]: ./media/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise/aad-sharepoint-permissions.png

<!-- URLs. -->

[Preview Azure Management Portal]: https://portal.azure.com/
[портале управления Azure]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/ru-RU/sharepoint/
[Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory]: app-service-mobile-dotnet-backend-ios-aad-sso-preview.md
[Расширение службы приложений серверной части .NET мобильных приложений]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.AppService/

<!---HONumber=September15_HO1-->