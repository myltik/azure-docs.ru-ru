<properties
	pageTitle="Как работать с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure | Служба приложений Azure"
	description="Узнайте, как работать с пакетом SDK для внутреннего сервера .NET для мобильных приложений службы приложений Azure."
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

## Практическое руководство. Скачивание и инициализация пакета SDK

Пакет SDK доступен на сайте [NuGet.org]. Этот пакет включает в себя базовые функции, необходимые для начала работы пакетом SDK. Для инициализации пакета SDK необходимо выполнить действия с объектом **HttpConfiguration**.

###Установка пакета SDK

Чтобы установить пакет SDK, щелкните правой кнопкой мыши серверный проект в Visual Studio, выберите **Управление пакетами NuGet**, найдите пакет [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/), а затем нажмите кнопку **Установить**.

###<a name="server-project-setup"></a> Инициализация серверного проекта

Инициализацию проекта внутреннего сервера .NET можно выполнить аналогично инициализации проектов ASP.NET — включив класс запуска OWIN. Для добавления этого класса в Visual Studio щелкните правой кнопкой мыши серверный проект и последовательно выберите пункты **Добавить**, **Создать элемент**, **Интернет**, **Общие** и **Класс запуска OWIN**.

После этого будет создан класс с указанным ниже атрибутом.

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

В методе `Configuration()` класса запуска OWIN настройте серверный проект сервера с помощью объекта **HttpConfiguration**, представляющего параметры конфигурации для службы. Следующий пример инициализирует серверный проект без дополнительных функций:

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

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) реализует стандартную *страницу "Это мобильное приложение запущено и работает"* для корневого каталога веб-узла. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) содержит классы для работы с данными и настраивает конвейер данных. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) позволяет платформе Entity Framework получать доступ к данным в базе данных SQL. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] включает функцию проверки подлинности и настраивает ПО промежуточного слоя OWIN, используемое для проверки маркеров. Чтобы добавить его в конфигурацию, вызовите методы расширения **AddAppServiceAuthentication** и **IAppBuilder**.**UseMobileAppAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] включает push-уведомления и определяет их конечную точку регистрации. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) создает контроллер, который предоставляет данные устаревшим веб-браузерам из вашего мобильного приложения. Чтобы добавить его в конфигурацию, вызовите метод расширения **MapLegacyCrossDomainController**.

## Практическое руководство. Определение настраиваемого контроллера API

Настраиваемый контроллер API обеспечивает большинство базовых функций для серверной части мобильного приложения, предоставляя конечную точку. Настраиваемый контроллер API

1. В Visual Studio щелкните правой кнопкой мыши папку "Контроллеры", а затем последовательно выберите пункты **Добавить**, **Контроллер**, **Контроллер веб-API 2 — пустой** и нажмите кнопку **Добавить**.

2. Укажите **имя контроллера**, например `CustomController`, и нажмите кнопку **Добавить**. Будет создан класс **CustomController**, наследующий от **ApiController**.

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

## Практическое руководство. Определение контроллера таблиц

Контроллер таблиц предоставляет доступ к данным сущностей в хранилище данных на основе таблиц, например в хранилище базы данных SQL или хранилище таблиц Azure. Контроллеры таблиц наследуют от универсального класса **TableController**, где универсальный тип является объектом в модели, которая представляет схему таблицы, как показано ниже.

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Для инициализации контроллеров таблиц используется метод расширения **AddTables**. В следующем примере инициализируется контроллер таблиц, который использует Entity Framework для доступа к данным:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Пример контроллера таблиц, использующего платформу Entity Framework для доступа к данным в базе данных SQL Azure, см. в классе **TodoItemController** в серверном проекте быстрого запуска, который можно скачать с портала Azure.

## Практическое руководство. Добавление аутентификации в серверный проект

Вы можете добавить функцию проверки подлинности в проект сервера, расширив объект **MobileAppConfiguration** и настроив ПО промежуточного слоя OWIN. При установке пакета [Microsoft.Azure.Mobile.Server.Quickstart] и вызове метода расширения **UseDefaultConfiguration** можно перейти к шагу 3.

1. В Visual Studio установите пакет [Microsoft.Azure.Mobile.Server.Authentication]. 

2. В файле проекта Startup.cs добавьте следующую строку кода в начало метода **Configuration**:

		app.UseMobileAppAuthentication(config);

	Это добавляет компонент ПО промежуточного слоя OWIN, который позволяет мобильному приложению Azure проверять токены, выданные связанным шлюзом службы приложений.

3. Добавьте атрибут `[Authorize]` в контроллеры и методы, требующие аутентификации. Теперь пользователи должны пройти аутентификацию для доступа к этой конечной точке или определенным API.

Сведения о том, как аутентифицировать клиентов в серверной части мобильного приложения, см. в статье [Добавление проверки подлинности в приложение](app-service-mobile-ios-get-started-users.md).

## Практическое руководство. Добавление push-уведомлений в серверный проект

Вы можете добавить push-уведомления в серверный проект, расширив объект **MobileAppConfiguration** и создав клиент концентраторов уведомлений. При установке пакета [Microsoft.Azure.Mobile.Server.Quickstart] и вызове метода расширения **UseDefaultConfiguration** можно перейти к шагу 3.

1. В Visual Studio щелкните правой кнопкой мыши серверный проект, затем щелкните **Управление пакетами NuGet**, найдите пакет Microsoft.Azure.Mobile.Server.Notifications и нажмите кнопку **Установить**. Будет установлен пакет [Microsoft.Azure.Mobile.Server.Notifications].
 
3. Повторите это действие, чтобы установить пакет `Microsoft.Azure.NotificationHubs`, который включает в себя клиентскую библиотеку концентраторов уведомлений.

2. Во время инициализации перейдите к папке App\_Startup, откройте файл проекта Startup.MobileApp.cs и добавьте в него вызов метода расширения **AddPushNotifications**, который выглядит указанным ниже образом.

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

## Практическое руководство. Добавление тегов в установку устройства для принудительной отправки тегов

Выполнив приведенное выше **Практическое руководство. Определение настраиваемого контроллера API**, потребуется настроить пользовательский API на серверной стороне для работы с концентраторами уведомлений, чтобы добавлять теги в конкретную установку устройства. Убедитесь, что передан идентификатор установки, хранящиеся в локальном хранилище клиента, и теги, которые требуется добавить (необязательно, поскольку теги можно указать непосредственно в серверной части). Следующий фрагмент кода следует добавить в контроллер для работы с концентраторами уведомлений для добавления тега к ИД установки устройства.

Использование [пакета NuGet концентраторов уведомлений Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) ([справка](https://msdn.microsoft.com/library/azure/mt414893.aspx))

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

## Практическое руководство. Публикация серверного проекта

Чтобы опубликовать серверный проект в Azure, выполните следующие действия:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=Nov15_HO4-->