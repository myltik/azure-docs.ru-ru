<properties
	pageTitle="Как работать с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure | Служба приложений Azure"
	description="Узнайте, как работать с пакетом SDK для внутреннего сервера .NET для мобильных приложений службы приложений Azure."
	keywords="служба приложений, служба приложений azure, мобильное приложение, мобильная служба, масштабировать, масштабируемый, разработка приложений, разработка приложений azure"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/24/2016"
	ms.author="glenga"/>

# Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

В этом разделе показано, как использовать пакет SDK для внутреннего сервера .NET в основных сценариях применения мобильных приложений службы приложений Azure. Пакет SDK для мобильных приложений Azure помогает работать с мобильными клиентами из своего приложения ASP.NET.

>[AZURE.TIP] [Пакет SDK сервера .NET для мобильных приложений Azure](https://github.com/Azure/azure-mobile-apps-net-server) предлагается в виде открытого исходного кода на сайте GitHub. Репозиторий содержит полный набор модульных тестов пакета SDK для сервера, а также некоторые примеры проектов.

## Справочная документация

Справочная документация по пакету SDK для сервера находится здесь: [Справочник по .NET для мобильных приложений Azure](https://msdn.microsoft.com/library/azure/dn961176.aspx).

## <a name="create-app"></a>Практическое руководство. Создание серверной части .NET для мобильного приложения

Если вы начинаете новый проект, приложение службы приложений можно создать с помощью [портала Azure] или программы Visual Studio. В этом разделе описано, как с помощью одного из этих вариантов создать новую серверную часть мобильного приложения, содержащую простой API-интерфейс списка дел. Вы сможете запустить его локально или опубликовать проект в мобильном приложении облачной службы приложений.

Если вы добавляете мобильные возможности к уже существующему проекту, переходите к разделу [Скачивание и инициализация пакета SDK](#install-sdk) ниже.

### Создание серверной части .NET с помощью портала Azure

Новое мобильное приложение можно создать прямо на [портале Azure]. Для этого выполните описанные ниже действия либо инструкции из руководства [Создание мобильного приложения](app-service-mobile-ios-get-started.md), где описано одновременное создание клиента и сервера.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9. Вернитесь в колонку _Приступая к работе_ и в разделе **Создание API таблицы** выберите в поле **Язык серверной части** значение **C#**.

&nbsp;&nbsp;10. Щелкните «Загрузить», извлеките сжатые файлы проекта на локальный компьютер и откройте решение в Visual Studio.

### Создание серверной части .NET с помощью Visual Studio 2013 и Visual Studio 2015

Чтобы создать проект мобильных приложений в Visual Studio, необходимо установить [пакет Azure SDK для .NET](https://azure.microsoft.com/downloads/) версии 2.8.1 или более поздней. После установки пакета SDK создайте новое приложение ASP.NET:

1. Откройте диалоговое окно **Новый проект** (последовательно выберите *Файл* > **Создать** > **Проект**).

2. Разверните раздел **Шаблоны** > **Visual C#** и выберите **Интернет**.

3. Выберите **Веб-приложение ASP.NET**.

4. Введите имя проекта. Нажмите кнопку **ОК**.

5. В разделе _Шаблоны ASP.NET 4.5.2_ выберите **Мобильное приложение Azure**. Установите флажок **Разместить в облаке**, чтобы создать новое мобильное приложение в облаке, в котором можно опубликовать этот проект.

6. Нажмите кнопку **ОК**. Созданное приложение появится в обозревателе решений.

## <a name="install-sdk"></a>Практическое руководство. Скачивание и инициализация пакета SDK

Пакет SDK доступен на сайте [NuGet.org]. Этот пакет включает в себя базовые функции, необходимые для начала работы пакетом SDK. Для инициализации пакета SDK необходимо выполнить действия с объектом **HttpConfiguration**.

###Установка пакета SDK

Чтобы установить пакет SDK, щелкните правой кнопкой мыши проект сервера в Visual Studio, выберите **Управление пакетами NuGet**, найдите пакет [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/), а затем нажмите кнопку **Установить**.

###<a name="server-project-setup"></a> Инициализация серверного проекта

Инициализацию проекта внутреннего сервера .NET можно выполнить аналогично инициализации проектов ASP.NET — включив класс запуска OWIN. Убедитесь, что указана ссылка на пакет NuGet `Microsoft.Owin.Host.SystemWeb`. Для добавления этого класса в Visual Studio щелкните проект сервера правой кнопкой мыши и последовательно выберите пункты **Добавить**, **Создать элемент**, **Интернет**, **Общие** и **Класс запуска OWIN**.

После этого будет создан класс с указанным ниже атрибутом.

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

В методе `Configuration()` класса запуска OWIN настройте проект сервера с помощью объекта **HttpConfiguration**, представляющего параметры конфигурации для службы. Следующий пример инициализирует серверный проект без дополнительных функций:

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();
	   
	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);  
	    
	    app.UseWebApi(config);
	}

Чтобы включить отдельные функции, перед вызовом **ApplyTo** необходимо вызвать методы расширения для объекта **MobileAppConfiguration**. Например, следующий код добавляет во время инициализации маршруты по умолчанию для всех контроллеров API, имеющих атрибут `[MobileAppController]`:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Обратите внимание, что `MapApiControllers` обозначает только контроллеры с атрибутом `[MobileAppController]`.

Многие методы расширения компонентов доступны через дополнительные пакеты NuGet, которые можно добавить. Это описано в следующем разделе.

При быстром запуске сервера на портале Azure выполняется вызов **UseDefaultConfiguration()**. Это эквивалентно указанной ниже настройке.
    
		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);


### Расширения пакета SDK

Следующие пакеты расширений на основе NuGet предоставляют различные возможности мобильных устройств, которые может использовать ваше приложение. Вы можете включить расширения во время инициализации с помощью объекта **MobileAppConfiguration**.

- Пакет [Microsoft.Azure.Mobile.Server.Quickstart] поддерживает базовую настройку мобильных приложений. Добавляется в конфигурацию вызовом метода расширения **UseDefaultConfiguration** во время инициализации. Это расширение включает в себя следующие расширения: пакеты Notifications, Authentication, Entity, Tables, Crossdomain и Home. Это эквивалент серверного проекта быстрого запуска, который можно скачать с портала Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) реализует стандартную *страницу «Это мобильное приложение запущено и работает»* для корневого каталога веб-сайта. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) содержит классы для работы с данными и настраивает конвейер данных. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddTables**.

- Пакет [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) позволяет платформе Entity Framework получать доступ к данным в базе данных SQL. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] включает функцию проверки подлинности и настраивает ПО промежуточного слоя OWIN, используемое для проверки токенов. Добавьте его в конфигурацию, вызвав методы расширения **AddAppServiceAuthentication** и **IAppBuilder**.**UseAppServiceAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] включает push-уведомления и определяет их конечную точку регистрации. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddPushNotifications**.

- Пакет [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) создает контроллер, который передает устаревшим веб-браузерам данные из вашего мобильного приложения. Чтобы добавить его в конфигурацию, вызовите метод расширения **MapLegacyCrossDomainController**.

- Пакет [Microsoft.Azure.Mobile.Server.Login] обеспечивает поддержку предварительного просмотра для пользовательской системы проверки подлинности с использованием метода AppServiceLoginHandler.CreateToken(). Это статический метод. Его не нужно включать в конфигурации.

## <a name="publish-server-project"></a>Практическое руководство. Публикация серверного проекта

В этом разделе показано, как опубликовать серверный проект .NET из Visual Studio. Кроме того, для развертывания серверного проекта вы можете использовать Git или любой из методов, описанных в [документации по развертыванию службы приложений Azure](../app-service-web/web-site-deploy.md).

1. В Visual Studio выполните повторную сборку проекта, чтобы восстановить пакеты NuGet.

2. В Обозревателе решений щелкните проект правой кнопкой мыши и выберите **Опубликовать**. При первой публикации будет необходимо определить профиль публикации. Если профиль уже определен, вы можете просто выделить его и нажать кнопку **Опубликовать**.

2. Если появится запрос на выбор цели публикации, щелкните **служба приложений Microsoft Azure** > **Далее**, а затем (при необходимости) войдите с учетными данными Azure. Visual Studio загрузит параметры публикации из Azure и безопасно сохранит их.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Выберите свою **Подписку**, затем выберите **Тип ресурса** в разделе **Представление**, разверните **Мобильное приложение** и щелкните серверную часть мобильного приложения, затем щелкните **ОК**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Проверьте сведения в профиле публикации и нажмите кнопку **Опубликовать**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	При успешной публикации серверной части мобильного приложения отобразится соответствующая целевая страница.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## Практическое руководство. Определение контроллера таблиц

Контроллер таблиц предоставляет доступ к данным сущностей в хранилище данных на основе таблиц, например в хранилище базы данных SQL или хранилище таблиц Azure. Контроллеры таблиц наследуются из универсального класса **TableController**, где универсальный тип является сущностью в модели, которая представляет схему таблицы, как показано ниже.

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Для инициализации контроллеров таблиц используется метод расширения **AddTables**. Он добавляет маршруты с префиксом `/tables/` для всех подклассов `TableController`.

В следующем примере инициализируется контроллер таблиц, который использует Entity Framework для доступа к данным:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
В качестве примера контроллера таблиц, который использует платформу Entity Framework для доступа к данным в базе данных SQL Azure, рекомендуем изучить класс **TodoItemController** в серверном проекте быстрого запуска, который вы можете скачать с портала Azure.

## Практическое руководство. Определение настраиваемого контроллера API

Настраиваемый контроллер API обеспечивает большинство базовых функций для серверной части мобильного приложения, предоставляя конечную точку. Вы можете зарегистрировать контроллер API для мобильных приложений с помощью атрибута [MobileAppController]. Этот атрибут регистрирует маршрут, а также настраивает сериализатор JSON мобильных приложений.

1. В Visual Studio щелкните папку "Контроллеры" правой кнопкой мыши, а затем последовательно выберите **Добавить** > **Контроллер** > **Контроллер веб-API 2 — пустой** и нажмите кнопку **Добавить**.

2. Укажите **имя контроллера**, например `CustomController`, и нажмите кнопку **Добавить**. Будет создан класс **CustomController**, который наследуется от **ApiController**.

3. В файле нового класса контроллера добавьте следующую инструкцию using:

		using Microsoft.Azure.Mobile.Server.Config;

4. Примените атрибут **[MobileAppController]** к определению класса контроллера API, как показано в примере ниже.

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. Добавьте в файл App\_Start/Startup.MobileApp.cs вызов метода расширения **MapApiControllers**, как показано в примере ниже.

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	Обратите внимание, что нет необходимости вызывать метод **MapApiControllers**, если вы вызываете метод **UseDefaultConfiguration**, который инициализирует все функции.

Клиенты могут получить доступ к любому контроллеру, к которому не применен атрибут **MobileAppControllerAttribute**. Однако клиенты, использующие любой пакет SDK для клиента мобильного приложения, не смогут правильно использовать этот контроллер.

## Практическое руководство: работа с проверкой подлинности

Мобильные приложения используют средства проверки подлинности службы приложений и ASP.NET для упрощения проверки подлинности приложений. В этом разделе показано, как выполнять следующие задачи проверки подлинности в серверном проекте .NET:

+ [Практическое руководство. Добавление аутентификации в серверный проект](#add-auth) 
+ [Практическое руководство. Использование пользовательской проверки подлинности для приложения](#custom-auth) 
+ [Практическое руководство. Получение сведений о пользователе, прошедшем проверку подлинности](#user-info)

### <a name="add-auth"></a>Практическое руководство. Добавление проверки подлинности в серверный проект

Вы можете добавить проверку подлинности в серверный проект, унаследовав от объекта **MobileAppConfiguration** и настроив ПО промежуточного слоя OWIN. Когда вы установите пакет [Microsoft.Azure.Mobile.Server.Quickstart] и вызовете метод расширения **UseDefaultConfiguration**, переходите к шагу 3.

1. В Visual Studio установите пакет [Microsoft.Azure.Mobile.Server.Authentication]. 

2. В файле проекта Startup.cs добавьте следующую строку кода в начало метода **Configuration**:

		app.UseAppServiceAuthentication(config);

	Это добавляет компонент ПО промежуточного слоя OWIN, который позволяет мобильному приложению Azure проверять токены, выданные связанным шлюзом службы приложений.

3. Добавьте атрибут `[Authorize]` во все контроллеры и методы, в которых нужна проверка подлинности. Теперь пользователи должны пройти проверку подлинности для доступа к этой конечной точке или определенным API.

Сведения о том, как проверять подлинность клиентов в серверных мобильных приложениях, см. в статье [Добавление проверки подлинности в приложение](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Практическое руководство. Использование пользовательской проверки подлинности для приложения

Если вы не хотите использовать один из поставщиков проверки подлинности и авторизации службы приложений, можете предоставить свою собственную систему входа. Для этого нужно установить пакет [Microsoft.Azure.Mobile.Server.Login].

Вам потребуется указать собственную логику для определения того, может ли пользователь войти. Например, можно выполнять проверку с помощью дополненных случайными данными и хэшированных паролей в базе данных. В следующем примере за эти проверки отвечает метод `isValidAssertion()`, который определен в другом месте.

Нестандартная проверка подлинности выполняется после создания нового объекта ApiController и предоставления действий регистрации и входа, как показано ниже. Клиент может выполнить попытку входа, собрав соответствующую информацию от пользователя и отправив API HTTPS-запрос POST, в тексте которого указаны данные пользователя. После проверки утверждения сервером можно выпустить маркер с помощью метода `AppServiceLoginHandler.CreateToken()`.

Пример действия входа:

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

В приведенном выше примере LoginResult и LoginResultUser являются простыми объектами, предоставляющими соответствующие свойства. Клиент ожидает возвращения ответов входа в виде объектов JSON в следующем формате:

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

Метод `MobileAppLoginHAppServiceLoginHandlerandler.CreateToken()` включает в себя параметры _audience_ и _issuer_. Для обоих этих параметров обычно устанавливаются значения, равные URL-адресу корня приложения, с помощью схемы HTTPS. Точно так же вы можете задать для параметра _secretKey_ значение ключа для подписи приложения. Это конфиденциальное значение, которое ни в коему случае не следует распространять или включать в клиент. Если приложение размещено в службе приложений, это значение вы можете получить из переменной среды _WEBSITE\_AUTH\_SIGNING\_KEY_. Если это необходимо для отладки в локальной среде, можно получить ключ и сохранить его как параметр приложения, выполнив инструкции из раздела [Локальная отладка с проверкой подлинности](#local-debug).

Кроме того, необходимо указать время существования выданного маркера, а также утверждения, которые вы хотите включить. Обязательно укажите утверждение субъекта, как показано в примере кода.

Вы также можете упростить код клиента, воспользовавшись методом `loginAsync()` (название может отличаться для разных платформ) вместо ручной отправки запроса HTTP POST. В этом случае используется перегруженный метод, который принимает дополнительный параметр маркера, соответствующий объекту утверждения, который был бы отправлен в запросе POST. В качестве поставщика в этом случае нужно указать любое имя по своему выбору. На сервере действие входа должно располагаться по пути _/.auth/login/{пользовательское\_имя\_поставщика}_, который включает это имя. Чтобы разместить свой контроллер по этому пути, добавьте маршрут к HttpConfiguration перед применением конфигурации MobileAppConfiguration.

		config.Routes.MapHttpRoute("CustomAuth", ".auth/login/CustomAuth", new { controller = "CustomAuth" }); 
		
Замените строку "CustomAuth" выше именем контроллера, на котором размещено действие входа.

>[AZURE.TIP] Подход с использованием LoginAsync() гарантирует, что маркер проверки подлинности прикрепляется к каждому последующему вызову службы.

###<a name="user-info"></a>Практическое руководство. Получение сведений о пользователе, прошедшем проверку подлинности

При проверке подлинности пользователя у службы приложений в коде серверной части .NET вы можете получить присвоенный идентификатор пользователя и другие сведения. Они необходимы для принятия решения об авторизации для данного пользователя в серверной части, например о том, может ли конкретный пользователь получить доступ к строке таблицы или другому ресурсу. В следующем коде показано, как получить идентификатор пользователя, вошедшего в систему:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID является производным от идентификатора пользователя, предоставленного поставщиком, и является статическим для данного пользователя и поставщика входа в систему.

Кроме того, служба приложений позволяет запрашивать конкретные утверждения от поставщика входа в систему. Это позволяет запрашивать дополнительные сведения от поставщика, например с помощью интерфейсов API Graph Facebook. Вы можете задать утверждения в колонке поставщика на портале. Некоторые утверждения требуют дополнительной настройки поставщика.

Следующий код вызывает метод расширения **GetAppServiceIdentityAsync** для получения учетных данных входа, которые включают маркер доступа, необходимый для выполнения запросов к API Graph Facebook:

    // Get the credentials for the logged-in user.
    var credentials = 
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token=" 
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Обратите внимание, что для работы метода расширения **GetAppServiceIdentityAsync** необходимо добавить выражение для `System.Security.Principal`.


## Практическое руководство. Добавление push-уведомлений в серверный проект

Вы можете добавить push-уведомления в проект сервера, расширив объект **MobileAppConfiguration** и создав клиент центров уведомлений. После установки пакета [Microsoft.Azure.Mobile.Server.Quickstart] и вызова метода расширения **UseDefaultConfiguration** переходите к шагу 3.

1. В Visual Studio щелкните правой кнопкой мыши серверный проект, затем щелкните **Управление пакетами NuGet**, найдите пакет Microsoft.Azure.Mobile.Server.Notifications и нажмите кнопку **Установить**. Будет установлен пакет [Microsoft.Azure.Mobile.Server.Notifications].
 
3. Повторите это действие, чтобы установить пакет `Microsoft.Azure.NotificationHubs`, который включает в себя клиентскую библиотеку центров уведомлений.

2. Перейдите к папке App\_Start, откройте файл Startup.MobileApp.cs и добавьте в него вызов метода расширения **AddPushNotifications** на этапе инициализации, то есть примерно так:

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	Это создает конечную точку регистрации push-уведомлений в серверном проекте. Эта конечная точка используется клиентами для регистрации в связанном центре уведомлений. Теперь необходимо клиент центра уведомлений, используемый для отправки уведомлений.

3. В контроллер, из которой требуется отправлять push-уведомления, добавьте следующую инструкцию using:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

4. Добавьте следующий код, который создает клиент центров уведомлений:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

На этом этапе можно использовать клиент центров уведомлений для отправки push-уведомлений на зарегистрированные устройства. Дополнительные сведения см. в разделе [Добавление push-уведомлений в приложение](app-service-mobile-ios-get-started-push.md). Дополнительные сведения обо всем, что можно сделать с помощью концентраторов уведомлений, см. в статье [Общие сведения о концентраторах уведомлений](../notification-hubs/notification-hubs-overview.md).

##<a name="tags"></a>Практическое руководство. Добавление тегов в установку устройства для принудительной отправки тегов

Во время выполнения инструкций из приведенного выше раздела **Определение настраиваемого контроллера API** потребуется настроить пользовательский API-интерфейс в серверной части для работы с центрами уведомлений, чтобы добавить теги в конкретную установку устройства. Убедитесь, что передан идентификатор установки, хранящиеся в локальном хранилище клиента, и теги, которые требуется добавить (необязательно, поскольку теги можно указать непосредственно в серверной части). Следующий фрагмент кода следует добавить в контроллер для работы с концентраторами уведомлений для добавления тега к ИД установки устройства.

Использование [пакета NuGet концентраторов уведомлений Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) ([справка](https://msdn.microsoft.com/library/azure/mt414893.aspx)):

		var hub = NotificationHubClient.CreateClientFromConnectionString("my-connection-string", "my-hub");

		hub.PatchInstallation("my-installation-id", new[]
		{
		    new PartialUpdateOperation
		    {
		        Operation = UpdateOperationType.Add,
		        Path = "/tags",
		        Value = "{my-tag}"
		    }
		});

Для принудительной отправки этих тегов нужно использовать [API центров уведомлений](https://msdn.microsoft.com/library/azure/dn495101.aspx).

Также можно добавить настраиваемый API для регистрации установок устройств в концентраторах уведомлений непосредственно на серверной стороне.

## Практическое руководство. Отладка и устранение неполадок пакета SDK для сервера .NET

Служба приложений Azure предоставляет несколько методов отладки и устранения неполадок в приложениях ASP.NET.

- [Мониторинг службы приложений Azure](../app-service-web/web-sites-monitor.md)
- [Включение ведения журналов диагностики в службе приложений Azure](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Диагностика службы приложений Azure в Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Ведение журналов

Журналы диагностики службы приложений вы можете вести с помощью стандартной записи трассировки ASP.NET. Перед записью в журналы необходимо включить диагностику в серверной части мобильного приложения.

Для выключения диагностики и записи в журналы выполните следующие действия.

1. Следуйте указаниям в разделе [Включение диагностики](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Добавьте в файл с кодом следующую инструкцию using:

		using System.Web.Http.Tracing;

3. Создайте модуль записи трассировки для записи из серверного приложения .NET в журналы диагностики следующим образом:

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");  

4. Повторно опубликуйте серверный проект и запустите серверную часть мобильного приложения с ведением журнала.

5. Загрузите и оцените журналы, как описано в разделе [Практическое руководство. Загрузка журналов](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Локальная отладка с проверкой подлинности

Вы можете запустить приложение локально, чтобы проверить изменения перед их публикацией в облаке. Для многих приложений это можно сделать, просто нажав клавишу *F5* в программе Visual Studio. Однако при проверке подлинности следует учитывать некоторые дополнительные рекомендации.

Необходимо облачное мобильное приложение, в котором настроены проверка подлинности и авторизация службы приложений, а в клиенте должна быть конечная точка облака, указанная как узел для альтернативного входа. Конкретные инструкции можно найти в документации для выбранной клиентской платформы ([iOS](app-service-mobile-ios-how-to-use-client-library.md), [Windows или Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)).

Убедитесь, что в приложении установлен пакет [Microsoft.Azure.Mobile.Server.Authentication]. В приложении добавьте следующий код в класс запуска OWIN после применения `MobileAppConfiguration` к объекту `HttpConfiguration`:
		
		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetMobileAppTokenHandler()
		});

В приведенном выше примере необходимо настроить параметры приложения _authAudience_ и _authIssuer_ в файле Web.config, задав для каждого из них значение URL-адреса корня приложения в формате HTTPS. Также для параметра _authSigningKey_ нужно указать значение ключа, который подписывает приложение. Это конфиденциальное значение, которое ни в коему случае не следует распространять или включать в клиент. Чтобы получить это значение, перейдите в свое приложение на [портале Azure] и щелкните **Инструменты**. Выберите **Kudu** и щелкните **Перейти**. Вы перейдете к конечной точке управления Kudu для вашего сайта. Щелкните **Среда** и найдите значение в разделе _WEBSITE\_AUTH\_SIGNING\_KEY_. Это значение следует использовать для параметра _authSigningKey_ в файле конфигурации вашего локального приложения.

Теперь на вашем локальном сервере есть все необходимое для проверки маркеров, которые клиент получает от конечной точки облака.


[портала Azure]: https://portal.azure.com
[портале Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_0128_2016-->