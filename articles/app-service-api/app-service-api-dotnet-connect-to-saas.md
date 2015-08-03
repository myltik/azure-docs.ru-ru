<properties 
	pageTitle="Пользовательские приложения API ASP.NET соединителя SaaS в службе приложений Azure" 
	description="Узнайте, как написать код для подключения к платформе SaaS из приложения API, и как вызывать приложение API из клиента .NET." 
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
	ms.date="07/01/2015" 
	ms.author="tdykstra"/>

# Подключение к платформе SaaS из приложения API ASP.NET в службе приложений Azure

## Обзор

В настоящем учебнике показаны примеры кода и конфигурации для [приложения API](app-service-api-apps-why-best-platform.md), которое подключается к [платформе программного обеспечения как услуги (SaaS)](../app-service/app-service-authentication-overview.md#obotosaas) с помощью [пакета SDK для .NET приложения API службы приложений](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/). Кроме того, в учебнике показано, как вызывать приложение API из клиента .NET с помощью [пакета SDK для .NET службы приложений](http://www.nuget.org/packages/Microsoft.Azure.AppService). Пройдя учебник, вы получите клиент с консольным приложением .NET, которое вызывает приложение API, выполняемое в службе приложений Azure. Приложение API вызывает Dropbox API, возвращая затем список файлов и папок в учетную запись пользователя Dropbox.

Альтернатива написанию кода, который вызывает каталог API SaaS непосредственно из пользовательского интерфейса API, — это вызов предварительного пакета [приложения API соединителя](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md). Сведения о том, как это сделать, см. в разделе [Развертывание и настройка приложения API соединителя SaaS](app-service-api-connnect-your-app-to-saas-connector.md).

Данный учебник описывает следующие шаги.

* Создание проекта приложения API в Visual Studio. 
* Настройка файла *apiapp.json*. Благодаря этому приложение API может подключаться к службе Dropbox.
* Добавление кода, который вызывает Dropbox и возвращает результаты.
* Создание нового приложения API в Azure.
* Развертывание проекта в приложении API.
* Настройка приложения API.
* Настройка шлюза.
* Создание тестового клиента.
* Запуск тестового клиента.

## Предварительные требования

Для работы с этим учебником необходимо следующее.

* Вы прошли учебники [Создание приложения API](app-service-dotnet-create-api-app.md) и [Развертывание приложения API](app-service-dotnet-deploy-api-app.md).
  
* У вас есть базовые знания об архитектуре шлюза службы приложений Azure для проверки подлинности. Сведения об этом приведены в разделе [Проверка подлинности для приложений API и мобильных приложений](app-service-authentication-overview.md).

* Вы умеете работать с приложениями API на портале предварительной версии Azure. Сведения об этом приведены в разделе [Переход к колонкам приложения API и шлюза](app-service-api-manage-in-portal.md#navigate).

## Создание проекта приложения API
 
Если согласно инструкциям вам нужно ввести название проекта, укажите *SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

## Настройка файла *apiapp.json*

Чтобы приложение API обращалось при вызове к платформе SaaS, эта платформа должна быть указана в файле *apiapp.json*.

1. Откройте файл *apiapp.json* и добавьте свойство `authentication`, как показано ниже (вам также нужно добавить запятую после предыдущего свойства).

		"authentication": [
		  {
		    "type": "dropbox"
		  }
		]

	Полный файл apiapp.json будет выглядеть следующим образом.

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "SimpleDropBox",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "SimpleDropBox",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [
		      {
		        "type": "dropbox"
		      }
		    ]
		}

2. Сохраните файл .

Настройка свойства `authentication` вызовет следующие изменения.

* Элементы пользовательского интерфейса на портале будут отображены в колонке приложения API. Таким образом, вы сможете указать значения для идентификатора клиента платформы SaaS и секрета клиента.

	![](./media/app-service-api-dotnet-connect-to-saas/authblade.png)

* Приложение API получит маркер доступа поставщика SaaS из шлюза для использования при вызове API поставщика SaaS.

Свойство `authentication` — это массив; в данной предварительной версии не поддерживается возможность выбора нескольких поставщиков.

Список поддерживаемых платформ см. в разделе [Получение согласия пользователя на доступ к другим платформам SaaS](../app-service/app-service-authentication-overview.md#obotosaas).

Вы также можете указывать области, как показано в следующем примере.

		"authentication": [
		  {
		    "type": "google",
		    "scopes": ["https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"]
		  }
		]

Доступные области определяются каждым поставщиком SaaS; они доступны на портале разработчиков поставщика.

## Добавление кода для вызова Dropbox

1. Установите пакет NuGet [DropboxRestAPI](https://www.nuget.org/packages/DropboxRestAPI) в проекте SimpleDropbox.

	* В меню **Средства** щелкните **Диспетчер пакетов Nuget > Консоль диспетчера пакетов**.

	* В окне **Консоль диспетчера пакетов** введите следующую команду.
	 
			install-package DropboxRestAPI  

1. Откройте файл *Controllers\ValuesController.cs* и замените весь код в файле следующим кодом.

		using DropboxRestAPI;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace SimpleDropBox2.Controllers
		{
		    public class ValuesController : ApiController
		    {
		        public async Task<IEnumerable<string>> Get()
		        {
		            // Retrieve the token from the gateway
		            var runtime = Runtime.FromAppSettings(Request);
		            var dropboxTokenResult = await runtime.CurrentUser.GetRawTokenAsync("dropbox");
		
		            // Create a Dropbox client object that will send the token
		            // with REST API calls to Dropbox.
		            var dropboxClient = new Client(
		                new Options
		                {
		                    AccessToken = dropboxTokenResult.Properties["AccessToken"]
		                }
		            );
		
		            // Call the Dropbox API
		            var metadata = await dropboxClient.Core.Metadata.MetadataAsync("/");
		
		            // Return a list of files and folders.
		            return metadata.contents.Select(md => md.path);
		        }
		    }
		}

	Прежде чем клиент вызовет этот метод, пользователю необходимо войти в Dropbox и разрешить приложению API доступ к своей учетной записи Dropbox. Dropbox подтверждает разрешение, предоставляя маркер доступа для шлюза службы приложений. Этот код получает маркер от шлюза и включает его в вызов API Dropbox, как показано в шагах 6 и 7 в приведенной ниже схеме.

	![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

2. Создайте проект.

## Создание приложения API в Azure

В этом разделе вы создадите приложение API в Azure с помощью мастера **Публикация веб-проекта** среды Visual Studio. Если согласно инструкциям вам нужно ввести название приложения API, укажите*SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## Развертывание кода

Для развертывания кода в новом приложении API используйте тот же мастер **публикации веб-проекта**.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Настройка проверки подлинности для входящих вызовов

Чтобы служба приложений Azure не блокировала прошедшие проверку подлинности исходящие вызовы из приложения API, приложение API также должно требовать, чтобы вызовы исходили от прошедших проверку подлинности пользователей. Это не общее требование OAuth 2.0, но это требование архитектуры шлюза службы приложений, применяемое в настоящее время.

Несмотря на то, что приведенные в этом разделе снимки экрана относятся к приложению ContactsList API, в приложении API SimpleDropbox процесс создания выглядит аналогично.

### Настройка требования обязательного прохождения проверки подлинности для приложения API

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

### Настройка поставщика удостоверений в шлюзе

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## Настройка проверки подлинности для исходящих вызовов

Чтобы разрешить приложению API вызывать API Dropbox, необходимо настроить обмен параметрами между приложением API и приложением Dropbox, созданным на сайте разработчика Dropbox.

### Создание приложения Dropbox на сайте Dropbox.com

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### Настройка обмена параметрами между приложением API и Dropbox

Несмотря на то, что следующие действия относятся к приложению API соединителя Dropbox, процедуры и элементы пользовательского интерфейса для создаваемого приложения API SimpleDropbox аналогичны.

> **Примечание.** Если в колонке **Проверка подлинности** не отображаются поля идентификатора клиента Dropbox и секрета клиента, как показано на снимке экрана, перезагрузите шлюз после развертывания проекта приложения API в приложении API в соответствии с инструкциями. Значение переменной «dropbox» в свойстве `authentication` файла *apiapp.json*, который был развернут ранее, запускает отображение этих полей на портале.

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

## Создание тестового клиента

В этом разделе вы создаете проект консольного приложения, в котором используется клиентский код, созданный средой Visual Studio для вызова приложения API SimpleDropbox. Консольное приложение создает экземпляр элемента управления браузера Windows Forms для управления взаимодействием пользователя со шлюзом и веб-страницами входа в Dropbox.

### Создание проекта

1. В Visual Studio создайте проект консольного приложения с именем *SimpleDropboxTest*.

2. Создайте ссылку на пространство имен System.Windows.Forms.
 
	* В **обозревателе решений** щелкните правой кнопкой мыши папку **Ссылки** и выберите пункт **Добавить ссылку**.

	* Установите флажок слева от параметра **System.Windows.Forms**, а затем нажмите кнопку **ОК**.
	 
	![](./media/app-service-api-dotnet-connect-to-saas/setref.png)

	Если пользователю необходимо разрешить вход в шлюз и Dropbox, консольное приложение воспользуется сборкой Windows Forms для создания элемента управления браузера.

### Добавление кода, созданного клиентом

Хотя на снимках экрана в этом разделе показан проект ContactsList и приложение API, выберите для работы с этим учебником проект SimpleDropboxTest и приложение API SimpleDropbox.

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### Добавление кода для вызова приложения API

3. Откройте файл *Program.cs* и замените существующий код следующим кодом.
		
		using Microsoft.Azure.AppService;
		using Newtonsoft.Json;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Windows.Forms;
		
		namespace SimpleDropboxTest
		{
		    enum Step
		    {
		        GatewayLogin,
		        GetSaaSConsentLink,
		        GetUserConsent
		    }
		
		    class Program
		    {
		        private const string GATEWAY_URL = @"{gateway url}";
		        private const string URL_TOKEN = "#token=";
		        private const string SAAS_URL = "dropbox.com";
		        private static Form frm = new Form();
		        private static string responseURL = "";
		        private static Step step;
		        [STAThread]
		        static void Main(string[] args)
		        {
		            // Create the web browser control
		            WebBrowser browser = new WebBrowser();
		            browser.Dock = DockStyle.Fill;
		            browser.Navigated += CheckResponseURL;
		            frm.Controls.Add(browser);
		            frm.Width = 640;
		            frm.Height = 480;
		
		            // Create the gateway and API app clients.
		            AppServiceClient appServiceClient = new AppServiceClient(GATEWAY_URL);
		            SimpleDropbox simpleDropboxClient = appServiceClient.CreateSimpleDropbox();
		
		            // Navigate browser to gateway login URL for configured identity provider.
		            // Identity provider for this example is Azure Active Directory.
		            step = Step.GatewayLogin;
		            browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));
		            frm.ShowDialog();
		            Console.WriteLine("Logged in to gateway, response URL=" + responseURL);
		
		            // Get user ID and Zumo token from return URL, then call 
		            // the gateway URL to log in the gateway client.
		            var encodedJson = responseURL.Substring(responseURL.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		            var decodedJson = Uri.UnescapeDataString(encodedJson);
		            var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		            string userId = result.user.userId;
		            string userToken = result.authenticationToken;
		            appServiceClient.SetCurrentUser(userId, userToken);
		
		            // Call gateway API to get consent link URL for target SaaS platform.
		            // SaaS platform for this example is Dropbox.
		            // See the tutorial for an explanation of
		            // the redirectURL parameter for GetConsentLinkAsync
		            var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;
		            Console.WriteLine("\nGot gateway consent link, URL=" + gatewayConsentLink);
		
		            // Navigate browser to consent link URL returned from gateway.
		            // Response URL will be the SaaS logon link
		            step = Step.GetSaaSConsentLink;
		            browser.Navigate(gatewayConsentLink);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot SaaS consent link response, URL=" + responseURL);
		
		            // Navigate browser to login/consent link for SaaS platform.
		            step = Step.GetUserConsent;
		            browser.Navigate(responseURL);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot Dropbox login response, URL=" + responseURL);
		
		            Console.WriteLine("\nResponse from SaaS Provider");
		            var response = simpleDropboxClient.Values.Get();
		            foreach (string s in response)
		            {
		                Console.WriteLine(s);
		            }
		            Console.Read();
		        }
		
		        static void CheckResponseURL(object sender, WebBrowserNavigatedEventArgs e)
		        {
		            if ((step == Step.GatewayLogin && e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		                || (step == Step.GetSaaSConsentLink && e.Url.AbsoluteUri.IndexOf(SAAS_URL) > -1)
		                || (step == Step.GetUserConsent && e.Url.AbsoluteUri.IndexOf(GATEWAY_URL) > -1))
		            {
		                responseURL = e.Url.AbsoluteUri;
		                frm.Close();
		            }
		        }
		
		    }
		}

1. Замените значение переменной {URL-адрес шлюза} фактическим URL-адресом шлюза.
 
	URL-адрес шлюза можно узнать в колонке **Шлюз** на портале.

	![](./media/app-service-api-dotnet-connect-to-saas/gwurl.png)

		private const string GATEWAY_URL = @"https://sd1aeb4ae60b7cb4f3d966dfa43b660.azurewebsites.net";

	> **Важно**. Убедитесь, что URL-адрес шлюза начинается с `https://`, а не с `http://`. **Если вы копируете текст «http://» с портала, при вставке в код его необходимо изменить на «https://».**

### Объяснение кода

Данное консольное приложение иллюстрирует шаги, которые должно выполнить клиентское приложение, с использованием минимального количества кода. Поскольку рабочее приложение обычно не является консольным приложением, в нем должна быть функция обработки ошибок и входа в систему.

Ниже описаны действия, инициируемые кодом.

* Открытие в браузере URL-адреса страницы входа в шлюз для заданного поставщика удостоверений, в данном случае — для Azure Active Directory. 
	 
* Обработка URL-адреса ожидаемых ответов после входа пользователя: извлечение идентификатора пользователя и маркера Zumo, передача их клиентскому объекту службы приложений.

* Использование клиентского объекта службы приложений для получения URL-адреса шлюза с перенаправлением по ссылке Dropbox для входа и предоставления разрешения. Шаг 1 на диаграмме.

* Открытие в браузере URL-адреса согласия шлюза. Браузер перенаправляется на страницу входа в Dropbox и предоставления разрешения. Шаг 2 на диаграмме.
	 
* Закрытие браузера после входа пользователя и предоставление согласия на Dropbox.com. Шаг 3 на диаграмме.
 
* Вызов приложения API. Шаг 5 на диаграмме. (Шаг 4 выполняется между сайтом Dropbox.com и шлюзом в фоновом режиме; шаги 6 и 7 выполняются из приложения API, но не клиента.)

![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

Дополнительные замечания

* Элемент управления веб-браузера требует наличие атрибута `STAThread` метода `Main`, который не относится к настройке или вызову приложения API.

* Для Azure Active Directory приведенный URL-адрес страницы входа в шлюз заканчивается значением `/aad`.

		browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));

	Ниже приведены значения, используемые для других поставщиков: * «microsoftaccount» * «facebook» * «twitter» * «google» <br/><br/>

* Второй параметр для метода `GetConsentLinkAsync()` — это URL-адрес обратного вызова. После входа пользователя в Dropbox сервер разрешения перенаправляет вызов на этот адрес, разрешая доступ к учетной записи пользователя.

		var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;

	Для этого параметра обычно указывается следующая веб-страница, на которую пользователю следует перейти в клиентском приложении. Этот демонстрационный код создан для консольного приложения. Поэтому страница приложения для перехода не указана, а сам код включает URL-адрес шлюза в качестве удобной целевой страницы.

	Клиентское приложение должно получить подтверждение о перенаправлении на этот URL-адрес и отсутствии сообщений об ошибках. Если при входе или получении разрешения произошла ошибка, строка запроса URL-адреса, на который выполняется перенаправление, может содержать сообщение об ошибке. Дополнительные сведения см. в разделе [Устранение неполадок](#troubleshooting).

## Тест

1. Запустите консольное приложение SimpleDropboxTest.

2. На первой странице входа выполните вход, используя свои учетные данные Azure Active Directory (или учетные данные для другого поставщика удостоверений, например Google или Twitter, если параметры шлюза предусматривают такую возможность).

	![](./media/app-service-api-dotnet-connect-to-saas/aadlogon.png)

3. На странице входа на сайт Dropbox.com выполните вход, используя свои учетные данные Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/dblogon.png)

4. На странице согласия Dropbox разрешите приложению осуществлять доступ к вашим данным.

	![](./media/app-service-api-dotnet-connect-to-saas/dbconsent.png)

	После этого консольное приложение вызовет приложение API, которое возвращает список файлов в учетной записи Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/testclient.png)

## Устранение неполадок

В этом разделе содержатся следующие темы

* [Ошибка HTTP 405 после входа в шлюз](#405)
* [Ошибка HTTP 400 вместо страницы входа в Dropbox](#400)
* [Ошибка HTTP 403 при вызове приложения API](#403)

### <a id="405"></a>Ошибка HTTP 405 после входа в шлюз

Если при вызове кодом GetConsentLinkAsync отображается ошибка HTTP 405, убедитесь, что в URL-адресе шлюза используется «https://», а не «http://».

![](./media/app-service-api-dotnet-connect-to-saas/http405.png)

Ошибка HTTP 405 (отсутствие разрешение для метода) отображается, когда клиент пытается сделать запрос в формате, отличном от SSL HTTP POST, после чего шлюз перенаправляет запрос на URL-адрес *https://* с отправкой запроса GET. URL-адрес для получения ссылки разрешения принимает только запросы POST.

### <a id="400"></a>Ошибка HTTP 400 вместо страницы входа в Dropbox

Убедитесь, что у вас указан правильный **идентификатор клиента** в колонке **Проверка подлинности** приложения API. Также убедитесь в отсутствии начальных и конечных пробелов.

### <a id="403"></a>Ошибка HTTP 403 при вызове приложения API

* Убедитесь, что для параметра **Уровень доступа** приложения API установлено значение **Общедоступный (с проверкой подлинности)**, а не **Внутренний**.

* Убедитесь, что указан правильный **секрет клиента** в колонке **Проверка подлинности** приложения API. Также убедитесь в отсутствии начальных и конечных пробелов.

URL-адрес перенаправления после входа в Dropbox может выглядеть как в следующем примере.

	https://sd1aeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/?error=RmFpbGVkIHRvIGV4Y2hhbmdlIGNvZGUgZm9yIHRva2VuLiBEZXRhaWxzOiB7ImVycm9yX2Rlc2NyaXB0aW9uIjogIkludmFsaWQgY2xpZW50X2lkIG9yIGNsaWVudF9zZWNyZXQiLCAiZXJyb3IiOiAiaW52YWxpZF9jbGllbnQifQ%3d%3d

Если из значения строки запроса `error` удалить конечные символы %3d%3d, она превратится в допустимую строку с кодировкой base64. Декодируйте строку, чтобы получить сообщение об ошибке.

	Failed to exchange code for token. Details: {"error_description": "Invalid client_id or client_secret", "error": "invalid_client"}

## Дальнейшие действия

Теперь вы знаете, как создавать код для подключенного к платформе SaaS приложения API, а также настраивать его параметры. Ссылки на другие учебники, описывающие способы проверки подлинности в приложениях API, см. в статье [Проверка подлинности для приложений API и мобильных приложений — дальнейшие действия](../app-service/app-service-authentication-overview.md#next-steps).

[Azure preview portal]: https://portal.azure.com/
[Azure portal]: https://manage.windowsazure.com/

<!---HONumber=July15_HO4-->