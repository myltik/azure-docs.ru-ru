<properties 
	pageTitle="Использование приложения API в службе приложений Azure из клиента .NET" 
	description="Узнайте, как использовать приложение API из клиента .NET с помощью пакета SDK службы приложений." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Использование приложения API в службе приложений Azure из клиента .NET 

## Обзор

В этом учебнике показано, как использовать пакет SDK службы приложений для написания кода, который вызывает [приложение API](app-service-api-apps-why-best-platform.md), настроенное для уровня доступа **Общедоступный (анонимный)** или **Общедоступный (с проверкой подлинности)**. В статье описаны следующие примеры сценариев:

- вызов приложения API с уровнем доступа **Общедоступный (анонимный)** из консольного приложения;
- вызов приложения API с уровнем доступа **Общедоступный (с проверкой подлинности)** из классического приложения Windows. 

Каждый из этих разделов учебника независим — можно выполнять инструкции для второго сценария без выполнения действий из первого.

Информацию о том, как вызвать **внутреннее** приложение API, см. в статье [Использование внутреннего приложения API из клиента .NET](app-service-api-dotnet-consume-internal.md).

## Предварительные требования

В этом учебнике предполагается, что вы знакомы с созданием проектов и добавлением к ним кода в Visual Studio, а также с тем, как [управлять приложениями API на портале предварительной версии Azure](app-service-api-apps-manage-in-portal.md).

Проект и примеры кода в этой статье основаны на проекте приложения API, который был создан, развернут и защищен с использованием сведений из таких статей:

- [Создание приложения API](app-service-dotnet-create-api-app.md)
- [Развертывание приложения API](app-service-dotnet-deploy-api-app.md)
- [Защита приложения API](../app-service-api-dotnet-add-authentication.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Для этого учебника требуется пакет SDK Azure для .NET версии 2.6 или более поздней.

## Вызовов из консольного приложения без проверки подлинности

В этом разделе создается проект консольного приложения и добавляется к нему код, вызывающий приложение API, которое не требует проверки подлинности.

### Настройка приложения API и создание проекта

1. Если это еще не сделано, выполните действия, описанные в статье [Развертывание приложения API](app-service-dotnet-deploy-api-app.md), чтобы развернуть образец проекта ContactsList в приложение API в вашей подписке Azure.

	В этом учебнике указано, что для диалогового окна публикации в Visual Studio необходимо установить уровень доступа **Доступен всем**, что соответствует значению **Общедоступный (анонимный)** на портале. Но если после этого вы выполнили действия из учебника [Защита приложения API](../app-service-dotnet-add-authentication.md), то для уровня доступа было присвоено значение **Общедоступный (с проверкой подлинности)**. В этом случае необходимо изменить его, как описано в следующем шаге.

2. На [портале предварительной версии Azure](https://portal.azure.com/) в колонке **Приложение API** для вызываемого приложения API перейдите к разделу **Параметры > Параметры приложения** и установите для параметра **Уровень доступа** значение **Общедоступный (анонимный)**.

	![](./media/app-service-api-dotnet-consume/setpublicanon.png)
 
2. В Visual Studio создайте проект консольного приложения.
 
### <a id="addclient"></a>Добавление клиентского кода, созданного пакетом SDK службы приложений

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### Добавление кода для вызова приложения API

Для вызова приложения API необходимо всего лишь создать объект клиента и методы вызова для него, как описано в следующем примере.

        var client = new ContactList();
        var contacts = client.Contacts.Get();

1. Откройте *Program.cs* и добавьте следующий код в метод `Main`.

		var client = new ContactsList();
		
		// Send GET request.
		var contacts = client.Contacts.Get();
		foreach (var c in contacts)
		{
		    Console.WriteLine("{0}: {1} {2}",
		        c.Id, c.Name, c.EmailAddress);
		}
		
		// Send POST request.
		client.Contacts.Post(new Models.Contact
		{
		    EmailAddress = "lkahn@contoso.com",
		    Name = "Loretta Kahn",
		    Id = 4
		});
		Console.WriteLine("Finished");
		Console.ReadLine();

3. Для запуска приложения нажмите сочетание клавиш CTRL+F5.

	![Создание завершено](./media/app-service-api-dotnet-consume/consoleappoutput.png)

## Вызов с проверкой подлинности из классического приложения для Windows

В этом разделе создается проект классического приложения для Windows и добавляется к нему код, вызывающий приложение API, для которого необходима проверка подлинности.

### Настройка приложения API и создание проекта

1. Выполните действия, описанные в учебнике [Защита приложения API](../app-service-dotnet-add-authentication.md), чтобы настроить приложение API с уровнем доступа **Общедоступный (с проверкой подлинности)**.

1. В Visual Studio создайте проект рабочего стола Windows Forms.

2. В конструкторе форм добавьте следующие элементы управления:

	* элемент управления «кнопка»;
	* элемент управления «текстовое поле»;
	* элемент управления веб-браузера.

3. Для элемента управления «текстовое поле» используйте параметр «многострочный».

	Форма должна выглядеть следующим образом:

	![](./media/app-service-api-dotnet-consume/form.png)

### Добавление кода клиента, созданного пакетом SDK службы приложений

3. В **Обозревателе решений** щелкните правой кнопкой мыши проект (не решение) и выберите команду **Добавить > Клиент приложения API Azure**. 

3. В диалоговом окне **Добавление клиента приложения API Azure ** установите переключатель **Загрузить из приложения API Azure**.

5. В раскрывающемся списке выберите приложение API, которое необходимо вызвать, а затем нажмите кнопку **OK**.

### Добавление кода для вызова приложения API

5. На портале предварительной версии Azure скопируйте URL-адрес для шлюза приложения API. Это значение потребуются вам на следующем шаге.

	![](./media/app-service-api-dotnet-consume/gatewayurl.png)

4. В исходный код *Form1.cs* добавьте следующий код перед конструктором `Form1()`, заменив значение для GATEWAY\_URL значением, скопированным на предыдущем шаге. Убедитесь, что адрес содержит завершающую косую черту (/).

		private const string GATEWAY_URL = "https://resourcegroupnameb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string URL_TOKEN = "#token=";

4. В конструкторе форм дважды щелкните кнопку, чтобы добавить обработчик щелчков, а затем в методе обработчика добавьте код для перехода к URL-адресу входа для шлюза, например:

		webBrowser1.Navigate(string.Format(@"{0}login/[authprovider]", GATEWAY_URL));

	Замените элемент [authprovider] кодом поставщика службы идентификации, который настроен в шлюзе, например aad, twitter, google, microsoftaccount или facebook. Например:

		webBrowser1.Navigate(string.Format(@"{0}login/aad", GATEWAY_URL));

7. Добавьте обработчик событий `DocumentCompleted` для элемента управления «веб-браузер» и добавьте следующий код в метод обработчика.

		if (e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		{
		    var encodedJson = e.Url.AbsoluteUri.Substring(e.Url.AbsoluteUri.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		    var decodedJson = Uri.UnescapeDataString(encodedJson);
		    var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		    string userId = result.user.userId;
		    string userToken = result.authenticationToken;
		
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    appServiceClient.SetCurrentUser(userId, userToken);
		
		    var contactsListClient = appServiceClient.CreateContactsList();
		    var contacts = contactsListClient.Contacts.Get();
		    foreach (Contact contact in contacts)
		    {
		        textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		    }
		    //appServiceClient.Logout();
		    //webBrowser1.Navigate(string.Format(@"{0}login/aad", GW_URL));
		}

	Добавленный код запускается после входа пользователя с помощью элемента управления веб-браузера. После успешного входа URL-адрес ответа содержит идентификатор пользователя и пароль. Код извлекает эти значения из URL-адреса, предоставляет их в объект клиента службы приложений и затем использует этот объект для создания объекта клиента приложения API. Затем можно вызвать API, вызывая методы на этом объекте клиента приложения API.

8. Для запуска приложения нажмите сочетание клавиш CTRL+F5.

9. Нажмите кнопку, а когда элемент управления браузера отобразит страницу входа, введите учетные данные пользователя с правами на вызов приложения API.

	Служба Azure выполнит проверку подлинности, а приложение вызывает приложение API и отображает ответ.

	![](./media/app-service-api-dotnet-consume/formaftercall.png)

### <a id="client-flow"></a>Сравнение клиентского потока и серверного потока

Этот пример приложения иллюстрирует [серверный поток](../app-service/app-service-authentication-overview.md#server-flow). Это значит, что шлюз получает маркер доступа поставщика удостоверений. В рамках [клиентского потока](../app-service/app-service-authentication-overview.md#client-flow), когда клиентское приложение получает маркер доступа непосредственно от поставщика удостоверений и отправляет его шлюзу, следует вызвать `LoginAsync` вместо `SetCurrentUser`.

В следующем примере кода предполагается, что у вас есть маркер доступа поставщика удостоверений в строковой переменной с именем `providerAccessToken`, а также индикатор поставщика удостоверений («aad», «microsoftaccount», «google», «twitter» или «facebook») в строковой переменной с именем `idProvider`.

		var appServiceClient = new AppServiceClient(GATEWAY_URL);
		var providerAccessTokenJSON = new JObject();
		providerAccessTokenJSON["access_token"] = providerAccessToken;
		var appServiceUser = await appServiceClient.LoginAsync(idProvider, providerAccessTokenJSON);

		var contactsListClient = appServiceClient.CreateContactsList();
		var contacts = contactsListClient.Contacts.Get();
		foreach (Contact contact in contacts)
		{
		    textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		}

## Дальнейшие действия

В этой статье показано, как использовать приложение API из клиента .NET для приложений API с уровнем доступа **Общедоступный (с проверкой подлинности)** и **Общедоступный (анонимный)**.

Для получения дополнительных примеров кода, который вызывает приложение API из клиентов .NET, загрузите образец приложения [Карты Azure](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample).

Сведения об использовании проверки подлинности в приложениях API см. в статье [Проверка подлинности для приложений API и мобильных приложений в службе приложений Azure](../app-service/app-service-authentication-overview.md).
 

<!---HONumber=Oct15_HO1-->