<properties 
	pageTitle="Вызов приложения API из прошедшего проверку подлинности клиента" 
	description="Узнайте о том, как вызвать приложение API Azure из клиента веб-приложения, прошедшего проверку подлинности Azure Active Directory." 
	keywords="служба приложения, служба приложения azure, проверка подлинности, проверка подлинности azure, api, api проверки подлинности"
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
	ms.date="10/20/2015" 
	ms.author="tdykstra"/>

# Вызов приложения Azure API из клиента веб-приложения, прошедшего проверку подлинности Azure Active Directory

## Обзор

В этом учебнике показано, как вызвать приложение API, которое защищено Azure Active Directory (AAD), из веб-приложения, которое также защищено AAD. После завершения этого учебника у вас будут веб-приложение и приложение API, запущенные в вашей подписке Azure. Веб-приложение будет отображать данные, которое оно получает путем вызова приложения API, как показано на следующем снимке экрана.

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

Вы узнаете, как передать учетные данные AAD от веб-приложения приложению API, чтобы пользователю не пришлось вновь указывать учетные данные для входа в приложение API.

Вы выполните следующие действия.

- Создание приложения API и его настройка на использование проверки подлинности AAD.
- Создание веб-приложения и его настройка на использование проверки подлинности AAD.
- Добавление в веб-приложение кода для вызова защищенного приложения API.
- Развертывание веб-приложения и приложения API в Azure.
- Проверка того, что веб-приложение может вызывать приложение API.

### Проверка подлинности клиентского потока

Код, который мы добавим в веб-приложение, реализует процесс, называемый проверкой подлинности [клиентского потока](../app-service/app-service-authentication-overview.md#client-flow). На следующей схеме показан процесс получения маркера доступа AAD и его обмен на маркер приложения API (Zumo).

![](./media/app-service-api-authentication-client-flow/clientflow.png)

Если вы не знакомы с ролью шлюза приложения API в проверке подлинности приложений API, обратитесь к разделу [Проверка подлинности для приложений API и мобильных приложений](../app-service/app-service-authentication-overview.md).

## Предварительные требования

Перед тем как начать, убедитесь, что у вас установлены [Visual Studio 2015](https://www.visualstudio.com/) и [пакет Azure SDK для .NET](http://go.microsoft.com/fwlink/?linkid=518003). Те же инструкции подойдут и для Visual Studio 2013.

В учебнике предполагается, что вы имеете навыки работы с проектами в Visual Studio.

## Создание и защита приложения API с помощью AAD

1. Создайте или загрузите проект приложения API.
 
	* Чтобы создать проект, следуйте инструкциям из раздела [Создание приложения API](app-service-dotnet-create-api-app.md).

	* Загрузить проект можно из [репозитория ContactsList GitHub](https://github.com/tdykstra/ContactsList).

	Теперь у вас есть проект веб-интерфейса API, который возвращает контактные данные.

	![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. Разверните проект приложения API в новое приложение API в Azure.

	Следуйте инструкциям из раздела [Развертывание приложения API](app-service-dotnet-deploy-api-app.md).

	В [портале предварительной версии Azure] в колонке**Определение API** для приложения API теперь отображаются методы Get и Post проекта веб-интерфейса API, который был развернут.

	![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. Защитите приложение API с помощью Azure Active Directory (AAD) в качестве поставщика удостоверений.
 
	Для защиты приложения AAD следуйте инструкциям в руководстве [Защита приложения API](app-service-api-dotnet-add-authentication.md). Выполнять действия из раздела **Использование Postman для отправки запроса Post** руководства не нужно.

	После завершения работы в колонке **Azure Active Directory** приложения API в [портале предварительной версии Azure] отобразятся идентификатор клиента приложения AAD и клиент AAD.

	![](./media/app-service-api-authentication-client-flow/aadblade.png)

	А на вкладке **Настройка** приложения AAD в [портале Azure] появятся URL-адрес идентификатора приложения API и URL-адрес ответа.

	![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## Создание и защита веб-приложения с помощью AAD

В этом разделе вы загрузите и настроите проект веб-приложения для проверки подлинности AAD. В проекте есть страница **О проекте**, на которой отображается информация об утверждениях для пользователя, вошедшего в систему.

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

Вы измените эту страницу позже, добавив раздел для отображения сведений о контактах, полученных от приложения API.

1. Загрузите веб-проект из [репозитория WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/).
 
2. Следуйте инструкциям раздела **Запуск примера** в [файле Readme](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md), за исключением следующего:
 
	* Несмотря на то что в файле Readme рекомендовано использовать Visual Studio 2013, вы также можете использовать Visual Studio 2015. 

	* Воспользуйтесь уже созданным приложением AAD вместо создания нового.
 
	* Сохраните тот же **URI идентификатора приложения**, который уже использовался для приложения AAD. (Не изменяйте его в соответствии с форматом, который указан в файле Readme.)
	
	* Измените другие настройки приложения AAD, как указано в инструкциях. В частности, в качестве URL-адресов входа и ответа задайте базовый URL-адрес для примера приложения.

Сохранение того же URI идентификатора приложения, который был создан для приложения API, позволит использовать одинаковые маркеры доступа AAD для веб-приложения и приложения API. Если изменить URI идентификатора приложения в соответствии с форматом, описанным в файле Readme, мы получим доступ к веб-приложению, но не к приложению API. Вы не сможете передать маркер AAD шлюзу приложения API для получения маркера Zumo, так как шлюз ожидает маркер для URI идентификатора приложения, который должен состоять из URL-адреса шлюза, к которому добавлена строка "/login/aad".

## Добавление кода, созданного клиентом, для приложения API

В этом разделе вы добавите автоматически созданный код для типизированного интерфейса для вызова приложения API.

8.	В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши проект WebApp-GroupClaims-DotNet и выберите **Добавить > Клиент приложения API Azure**.

9.	В диалоговом окне **Добавить клиент приложения API Microsoft Azure** выберите приложение API, которое было защищено с помощью AAD.

	После завершения создания кода в **Обозревателе решений** появится новая папка с именем приложения API. Эта папка содержит код, который реализует клиентские классы и модели данных.

10. Исправьте неоднозначные ссылки, вызванные созданным кодом в *ContactsList/ContactsExtensions.cs*: замените два вхождения `Task.Factory.StartNew` на `System.Threading.Tasks.Task.Factory.StartNew`.
 
## Добавьте код для обмена маркера AAD на маркер Zumo.

1.	В HomeController.cs добавьте выражения `using` для пакета SDK службы приложений и сериализатора JSON.

		using Microsoft.Azure.AppService;
		using Newtonsoft.Json.Linq;

2. Добавьте константы для URL-адреса шлюза и URI идентификатора приложения AAD. Убедитесь, что для доступа к URL-адресу шлюза используется протокол https, а не http.

		private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.	Добавьте метод, который возвращает объект клиента для вызова приложения API.

		public async Task<AppServiceClient> GetAppServiceClient()
		{
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    string userObjectID = ClaimsPrincipal.Current.FindFirst
		        ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		
		    var authContext = new AuthenticationContext
		        (ConfigHelper.Authority, new TokenDbCache(userObjectID));
		
		    ClientCredential credential = new ClientCredential
		        (ConfigHelper.ClientId, ConfigHelper.AppKey);
		
		    // Get the AAD token.
		    AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
		    var aadToken = new JObject();
		    aadToken["access_token"] = result.AccessToken;
		
		    // Send the AAD token to the gateway and get a Zumo token
		    var appServiceUser = await appServiceClient.LoginAsync
		        ("aad", aadToken).ConfigureAwait(false);
		
		    return appServiceClient;
		}

	В этом коде `ConfigHelper.Authority` соответствует https://login.microsoftonline.com/{клиент}, например: https://login.microsoftonline.com/contoso.onmicrosoft.com.

2.	Добавьте код вызова приложения API непосредственно перед выражением `return View()` в конце метода `About`. (На следующем шаге вы добавите код для отображения возвращаемых данных в представление `About`.)

		var appServiceClient = await GetAppServiceClient();
		var client = appServiceClient.CreateContactsList();
		var contacts = client.Contacts.Get();
		ViewData["contacts"] = contacts;

3. В *Views/Home/About.cshtml* добавьте код для отображения контактных данных сразу после заголовка `h2`.

		<h3>Contacts</h3>
		<table class="table table-striped table-bordered table-condensed table-hover">
		    <tr>
		        <th>Name</th>
		        <th>Email</th>
		    </tr>
		
		    @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
		    {
		        <tr>
		            <td>@contact.Name</td>
		            <td>@contact.EmailAddress</td>
		        </tr>
		    }
		
		</table>


3. В конфигурационный файл приложения Web.config добавьте следующее перенаправление привязки после открывающего тега `<assemblyBinding>`.

		<dependentAssembly>
		  <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
		  <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
		</dependentAssembly>

	Без этого перенаправления привязки приложение завершится с ошибкой, так как пакет SDK службы приложений включает зависимость от System.Net.Http.Primitives версии 1.5.0.0, но в проекте используется версия 4.2.28.0.
 
3. Следуйте указаниям из раздела **Развертывание этого примера в Azure** в [файле сведений](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/).

5. Запустите приложение.

	![](./media/app-service-api-authentication-client-flow/homepage.png)

6. Щелкните **Вход**, а затем введите учетные данные пользователя в домене AAD, который используется для этого приложения.

	![](./media/app-service-api-authentication-client-flow/signedin.png)

7. Щелкните **О проекте**.

	Код, который был добавлен в контроллер и представление About, выполняется и показывает, что вы успешно прошли проверку подлинности для приложения API и вызвали его метод Get.

	![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## Устранение неполадок

### Ошибки HTTP 400 

Убедитесь, что в URL-адресе шлюза используется протокол https, а не http. При копировании непосредственно из портала предварительной версии Azure можно указать URL-адрес с протоколом http.
 
### Ошибки SQL Server

Приложению требуется доступ к базе данных SQL Server, определяемый строкой подключения GroupClaimContext. Убедитесь, что строка подключения развернутого веб-сайта указывает на экземпляр базы данных SQL. Правильную строку подключения можно поместить в развернутый файл Web.config или в параметры среды выполнения Azure.

## Благодарности

Благодарим Говинда С. Йадава (Govind S. Yadav, [@govindsyadav](https://twitter.com/govindsyadav)) за помощь в разработке этого учебника.

## Дальнейшие действия

Вы узнали, как создать клиентский поток проверки подлинности для приложений API службы приложений. Сведения о других способах реализации проверки подлинности в приложениях API см. в статье [Проверка подлинности для приложений API и мобильных приложений](../app-service/app-service-authentication-overview.md).

[портале Azure]: https://manage.windowsazure.com/
[портале предварительной версии Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_1125_2015-->