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
	ms.date="09/18/2015"
	ms.author="glenga"/>

# Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure

В этом разделе показано, как использовать пакет SDK для внутреннего сервера .NET в основных сценариях применения мобильных приложений службы приложений Azure. Пакет SDK для мобильных приложений Azure помогает работать с мобильными клиентами из своего приложения ASP.NET.

>[AZURE.TIP] [Пакет SDK сервера .NET для мобильных приложений Azure](https://github.com/Azure/azure-mobile-apps-net-server) предлагается в виде открытого исходного кода на сайте GitHub. Репозиторий содержит полный набор модульных тестов пакета SDK для сервера, а также некоторые примеры проектов.

## Практическое руководство. Создание серверной части .NET для мобильного приложения

При запуске нового проекта можно создать приложение службы приложений с помощью [портала предварительной версии Azure] или программы Visual Studio. В этом разделе описано, как с помощью одного из этих вариантов создать новую серверную часть мобильного приложения, содержащую простой API-интерфейс списка дел. Вы сможете запустить его локально или опубликовать проект в мобильном приложении облачной службы приложений.

Если вы добавляете мобильные возможности к существующему проекту, см. раздел [Загрузка и инициализация пакета SDK](#install-sdk) ниже.

### Создание серверной части .NET с помощью портала Azure

Новое мобильное приложение можно создать прямо на [портале управления предварительной версии Azure]. Можно либо выполнить приведенные ниже действия, либо создать новый клиент и сервер одновременно, следуя инструкциям из руководства [Создание мобильного приложения](app-service-mobile-ios-get-started.md).

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

## <a name="install-sdk"></a>Практическое руководство. Загрузка и инициализация пакета SDK

Пакет SDK доступен на сайте [NuGet.org]. Этот пакет включает в себя базовые функции, необходимые для начала работы пакетом SDK. Для инициализации пакета SDK необходимо выполнить действия с объектом **HttpConfiguration**.

###Установка пакета SDK

Чтобы установить пакет SDK, щелкните правой кнопкой мыши серверный проект в Visual Studio, выберите **Управление пакетами NuGet**, найдите пакет [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/), а затем нажмите кнопку **Установить**.

###<a name="server-project-setup"></a> Инициализация серверного проекта

Инициализацию проекта внутреннего сервера .NET можно выполнить аналогично инициализации проектов ASP.NET — включив класс запуска OWIN. Чтобы добавить этот класс в Visual Studio, щелкните серверный проект правой кнопкой мыши и последовательно выберите пункты **Добавить** -> **Новый элемент** -> **Интернет** -> **Общие** -> **Класс запуска OWIN**.

После этого будет создан класс с указанным ниже атрибутом.

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

В методе `Configuration()` класса запуска OWIN настройте серверный проект с помощью объекта **HttpConfiguration**, представляющего параметры конфигурации для службы. Следующий пример инициализирует серверный проект без дополнительных функций:

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();
	   
	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);  
	    
	    app.UseWebApi(config);
	}

Чтобы включить отдельные функции, перед вызовом **ApplyTo** необходимо вызвать методы расширения для объекта **MobileAppConfiguration**. Например, следующий код во время инициализации добавляет маршруты по умолчанию для всех контроллеров API:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Многие методы расширения компонентов доступны через дополнительные пакеты NuGet, которые можно добавить. Это описано в следующем разделе. При быстром запуске сервера на портале Azure будет выполнен вызов **UseDefaultConfiguration()**. Это эквивалентно указанной ниже настройке.
    
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

Следующие пакеты расширений на основе NuGet предоставляют различные возможности мобильных устройств, которые может использовать ваше приложение. Во время инициализации вы можете включить расширения с помощью объекта **MobileAppConfiguration**.

- [Microsoft.Azure.Mobile.Server.Quickstart] поддерживает базовую настройку мобильных приложений. Чтобы добавить его в конфигурацию, во время инициализации вызовите метод расширения **UseDefaultConfiguration**. Это расширение включает в себя следующие расширения: пакеты Notifications, Authentication, Entity, Tables, Crossdomain и Home. Это эквивалент серверного проекта быстрого запуска, который можно скачать с портала Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) реализует стандартную страницу *Это мобильное приложение запущено и работает* для корневого каталога веб-сайта. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) содержит классы для работы с данными и настраивает конвейер данных. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) позволяет платформе Entity Framework получать доступ к данным в базе данных SQL. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] включает функцию проверки подлинности и настраивает ПО промежуточного слоя OWIN, используемое для проверки маркеров. Чтобы добавить его в конфигурацию, вызовите методы расширения **AddAppServiceAuthentication** и **IAppBuilder**.**UseMobileAppAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] включает push-уведомления и определяет их конечную точку регистрации. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) создает контроллер, который предоставляет данные устаревшим веб-браузерам из вашего мобильного приложения. Чтобы добавить его в конфигурацию, вызовите метод расширения **MapLegacyCrossDomainController**.

- [Microsoft.Azure.Mobile.Server.Login] обеспечивает поддержку предварительного просмотра для нестандартной проверки подлинности с использованием метода MobileAppLoginHandler.CreateToken(). Это статический метод. Его не нужно включать в конфигурации.

## Практическое руководство. Публикация серверного проекта

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

Также можно использовать любой из методов, описанных в [документации по развертыванию службы приложений Azure](../app-service-web/web-site-deploy.md).

## Практическое руководство. Определение контроллера таблиц

Контроллер таблиц предоставляет доступ к данным сущностей в хранилище данных на основе таблиц, например в хранилище базы данных SQL или хранилище таблиц Azure. Контроллеры таблиц наследуются из универсального класса **TableController**, где универсальный тип является сущностью в модели, которая представляет схему таблицы, как показано ниже.

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Для инициализации контроллеров таблиц используется метод расширения **AddTables**. В следующем примере инициализируется контроллер таблиц, который использует Entity Framework для доступа к данным:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Пример контроллера таблиц, использующего платформу Entity Framework для доступа к данным в базе данных SQL Azure, см. в классе **TodoItemController** в серверном проекте быстрого запуска, который можно загрузить с портала Azure.


## Практическое руководство. Определение настраиваемого контроллера API

Настраиваемый контроллер API обеспечивает большинство базовых функций для серверной части мобильного приложения, предоставляя конечную точку. Настраиваемый контроллер API

1. В Visual Studio щелкните правой кнопкой мыши папку «Контроллеры», а затем последовательно выберите **Добавить** > **Контроллер** > **Контроллер веб-API 2 — пустой** и нажмите кнопку **Добавить**.

2. Укажите **имя контроллера**, например `CustomController`, и нажмите кнопку **Добавить**. Будет создан класс **CustomController**, который наследуется из **ApiController**.

3. В файле нового класса контроллера добавьте следующую инструкцию using:

		using Microsoft.Azure.Mobile.Server.Config;

4. Примените атрибут **MobileAppControllerAttribute** к определению класса контроллера API, как показано в примере ниже.

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. Добавьте в файл App\_Start/Startup.MobileApp.cs вызов метода расширения **MapApiControllers**, как показано в примере ниже.

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	Обратите внимание, что нет необходимости вызывать метод **MapApiControllers**, если вы вызываете метод **UseDefaultConfiguration**, инициализирующий все функции.

Клиенты могут получить доступ к любому контроллеру, к которому не применен атрибут **MobileAppControllerAttribute**. Тем не менее клиенты, в которых применяется любой пакет SDK для клиента мобильного приложения, не смогут правильно использовать этот контроллер.


## Практическое руководство. Добавление аутентификации в серверный проект

Вы можете добавить функцию проверки подлинности в проект сервера, расширив объект **MobileAppConfiguration** и настроив ПО промежуточного слоя OWIN. При установке пакета [Microsoft.Azure.Mobile.Server.Quickstart] и вызове метода расширения **UseDefaultConfiguration** можно перейти к шагу 3.

1. В Visual Studio установите пакет [Microsoft.Azure.Mobile.Server.Authentication]. 

2. В файле проекта Startup.cs добавьте следующую строку кода в начало метода **Configuration**:

		app.UseMobileAppAuthentication(config);

	Это добавляет компонент ПО промежуточного слоя OWIN, который позволяет мобильному приложению Azure проверять токены, выданные связанным шлюзом службы приложений.

3. Добавьте атрибут `[Authorize]` в контроллеры и методы, требующие проверки подлинности. Теперь пользователи должны пройти проверку подлинности для доступа к этой конечной точке или определенным API.

Сведения о том, как проверять подлинность клиентов в серверной части мобильного приложения, см. в статье [Добавление проверки подлинности в приложение](app-service-mobile-ios-get-started-users.md).

## <a name="custom-auth"></a>Практическое руководство. Использование нестандартной проверки подлинности для приложения

Если вы не хотите использовать один из поставщиков проверки подлинности и авторизации службы приложений, можете предоставить свою собственную систему входа. Для этого нужно установить пакет [Microsoft.Azure.Mobile.Server.Login].

Вам потребуется указать собственную логику для определения того, может ли пользователь войти. Например, можно выполнять проверку с помощью дополненных случайными данными и хэшированных паролей в базе данных. Метод `isValidAssertion()` в следующем примере отвечает за эти проверки и определен в другом месте.

Нестандартная проверка подлинности выполняется после создания нового объекта ApiController и предоставления действий регистрации и входа, как показано ниже. Клиент может выполнить попытку входа, собрав соответствующую информацию от пользователя и отправив API HTTPS-запрос POST, в тексте которого указаны данные пользователя. После проверки этой информации выдается маркер с помощью метода `MobileAppLoginHandler.CreateToken()`.

Пример действия входа:

		public HttpResponseMessage Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = MobileAppLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return this.Request.CreateResponse(HttpStatusCode.OK, new LoginResult()
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

Метод `MobileAppLoginHandler.CreateToken()` включает параметры _audience_ и _issuer_. Для обоих этих параметров обычно устанавливаются значения, равные URL-адресу корня приложения, с помощью схемы HTTPS. Точно так же можно задать для параметра _secretKey_ значение ключа, который подписывает приложение. Это конфиденциальное значение, которое ни в коему случае не следует распространять или включать в клиент. Это значение можно получить при размещении в службе приложений с помощью ссылки на переменную среды _WEBSITE\_AUTH\_SIGNING\_KEY_. Если это необходимо в контексте локальной отладки, следуйте инструкциям из раздела [Локальная отладка с проверкой подлинности](#local-debug), чтобы получить ключ и сохранить его как параметр приложения.

Кроме того, необходимо указать время существования выданного маркера, а также утверждения, которые вы хотите включить. Обязательно укажите утверждение субъекта, как показано в примере кода.

## Практическое руководство. Добавление push-уведомлений в серверный проект

Вы можете добавить push-уведомления в серверный проект, расширив объект **MobileAppConfiguration** и создав клиент концентраторов уведомлений. При установке пакета [Microsoft.Azure.Mobile.Server.Quickstart] и вызове метода расширения **UseDefaultConfiguration** можно перейти к шагу 3.

1. В Visual Studio щелкните правой кнопкой мыши серверный проект, затем щелкните **Управление пакетами NuGet**, найдите пакет Microsoft.Azure.Mobile.Server.Notifications и нажмите кнопку **Установить**. Будет установлен пакет [Microsoft.Azure.Mobile.Server.Notifications].
 
3. Повторите это действие, чтобы установить пакет `Microsoft.Azure.NotificationHubs`, который включает в себя клиентскую библиотеку концентраторов уведомлений.

2. Во время инициализации перейдите к папке App\_Startup, откройте файл Startup.MobileApp.cs и добавьте в него вызов метода расширения **AddPushNotifications**, который выглядит примерно так:

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

На этом этапе можно использовать клиент центров уведомлений для отправки push-уведомлений на зарегистрированные устройства. Дополнительные сведения см. в статье [Добавление push-уведомлений в приложение](app-service-mobile-ios-get-started-push.md). Дополнительные сведения обо всем, что можно сделать с помощью концентраторов уведомлений, см. в статье [Общие сведения о концентраторах уведомлений](../notification-hubs/notification-hubs-overview.md).

## Практическое руководство. Добавление тегов в установку устройства для принудительной отправки тегов

Во время выполнения инструкций руководства **Определение настраиваемого контроллера API** потребуется настроить пользовательский API в серверной части для работы с концентраторами уведомлений, чтобы добавить теги в конкретную установку устройства. Убедитесь, что передан идентификатор установки, хранящиеся в локальном хранилище клиента, и теги, которые требуется добавить (необязательно, поскольку теги можно указать непосредственно в серверной части). Следующий фрагмент кода следует добавить в контроллер для работы с концентраторами уведомлений для добавления тега к ИД установки устройства.

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

Для отправки этих тегов нужно использовать [API концентраторов уведомлений](https://msdn.microsoft.com/library/azure/dn495101.aspx).

Также можно добавить настраиваемый API для регистрации установок устройств в концентраторах уведомлений непосредственно на серверной стороне.

## Практическое руководство. Отладка и устранение неполадок пакета SDK для сервера .NET

Служба приложений Azure предоставляет несколько методов отладки и устранения неполадок в приложениях ASP.NET.

- [Мониторинг службы приложений Azure](../app-service-web/web-sites-monitor.md)
- [Включение ведения журналов диагностики в службе приложений Azure](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Диагностика службы приложений Azure в Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Ведение журналов

Журналы диагностики службы приложений можно вести с помощью стандартной записи трассировки ASP.NET:

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");  

### <a name="local-debug"></a>Локальная отладка с помощью проверки подлинности

Вы можете запустить приложение локально, чтобы проверить изменения перед их публикацией в облаке. Во многих приложениях это можно сделать, просто нажав клавишу *F5* в программе Visual Studio. Однако при проверке подлинности следует учитывать некоторые дополнительные рекомендации.

Необходимо облачное мобильное приложение, в котором настроены проверка подлинности и авторизация службы приложений, а в клиенте должна быть конечная точка облака, указанная как узел для альтернативного входа. Конкретные инструкции можно найти в документации для выбранной клиентской платформы ([iOS](app-service-mobile-ios-how-to-use-client-library.md), [Windows или Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)).

Убедитесь, что в приложении установлен пакет [Microsoft.Azure.Mobile.Server.Authentication]. После применения объекта `MobileAppConfiguration` к `HttpConfiguration` добавьте следующий код в класс запуска OWIN своего приложения:
		
		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetMobileAppTokenHandler()
		});

В приведенном выше примере необходимо настроить параметры приложения _authAudience_ и _authIssuer_ в файле Web.config, задав для каждого из них значение URL-адреса корня приложения с помощью схемы HTTPS. Точно так же можно задать для параметра _authSigningKey_ значение ключа, который подписывает приложение. Это конфиденциальное значение, которое ни в коему случае не следует распространять или включать в клиент. Чтобы получить это значение, перейдите в свое приложение на [портале управления предварительной версии Azure] и щелкните **Сервис**. Выберите **Kudu** и щелкните **Перейти**. Вы перейдете к конечной точке управления Kudu для вашего сайта. Щелкните **Среда** и найдите значение в разделе _WEBSITE\_AUTH\_SIGNING\_KEY_. Это значение следует использовать для параметра _authSigningKey_ в файле конфигурации вашего локального приложения.

Теперь на вашем локальном сервере есть все необходимое для проверки маркеров, которые клиент получает от конечной точки облака.


[портала предварительной версии Azure]: https://portal.azure.com
[портале управления предварительной версии Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_1125_2015-->