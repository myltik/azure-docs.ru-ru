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
	ms.date="08/12/2015"
	ms.author="glenga"/>

# Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure

В этом разделе показано, как использовать пакет SDK для внутреннего сервера .NET в основных сценариях применения мобильных приложений службы приложений Azure. Пакет SDK для мобильных приложений Azure помогает работать с мобильными клиентами из своего приложения ASP.NET.

## Практическое руководство. Скачивание и инициализация пакета SDK

Пакет SDK доступен на сайте [NuGet.org]. Этот пакет включает в себя базовые функции, необходимые для начала работы пакетом SDK. Для инициализации пакета SDK необходимо выполнить действия с объектом **HttpConfiguration**.

###Установка пакета SDK

Чтобы установить пакет SDK, щелкните правой кнопкой мыши серверный проект в Visual Studio, выберите **Управление пакетами NuGet**, найдите пакет [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/), а затем нажмите кнопку **Установить**.

###Инициализация серверного проекта

Проекте внутреннего сервера .NETинициализируется в методе **Register** класса **WebApiConfig**, который обычно находится в папке App\_Start. Объект **HttpConfiguration**, который представляет параметры конфигурации для службы, используется для инициализации серверного проекта. Следующий пример инициализирует серверный проект без дополнительных функций:

    new MobileAppConfiguration()
        .ApplyTo(config);

Чтобы включить отдельные функции, необходимо вызвать методы расширения для объекта **MobileAppConfiguration** до вызова **ApplyTo**. Например, следующий код во время инициализации добавляет маршруты по умолчанию для всех контроллеров API:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Многие методы расширения компонентов доступны через дополнительные пакеты NuGet, которые можно добавить. Это описано в следующем разделе.

### Расширения пакета SDK

Следующие пакеты расширений на основе NuGet предоставляют различные возможности мобильных устройств, которые может использовать ваше приложение. Расширения включаются во время инициализации с помощью объекта **MobileAppConfiguration**.

- [Microsoft.Azure.Mobile.Server.Quickstart] поддерживает базовую настройку мобильных приложений. Добавляется в конфигурацию вызовом метода расширения **UseDefaultConfiguration** во время инициализации. Это расширение включает в себя следующие расширения: пакеты Notifications, Authentication, Entity, Tables, Crossdomain и Home. Это эквивалент серверного проекта быстрого запуска, который можно скачать с портала Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) добавляет простую домашнюю страницу в корневой каталог веб-сайта. Добавьте в конфигурацию, вызвав метод расширения **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) содержит классы для работы с данными и настраивает конвейер данных. Добавьте в конфигурацию, вызвав метод расширения **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) позволяет платформе Entity Framework осуществлять доступ к данным в базе данных SQL. Добавьте в конфигурацию, вызвав метод расширения **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] включает аутентификацию и настраивает ПО промежуточного слоя OWIN, используемое для проверки токенов. Добавьте в конфигурацию, вызвав методы расширения **AddAppServiceAuthentication** и **IAppBuilder**.**UseMobileAppAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] включает push-уведомления и определяет конечную точку регистрации отправки. Добавьте в конфигурацию, вызвав метод расширения **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) создает контроллер, который обслуживает данные для устаревших веб-браузеров из вашего мобильного приложения. Добавьте в конфигурацию, вызвав метод расширения **MapLegacyCrossDomainController**.

## Практическое руководство. Определение настраиваемого контроллера API

Настраиваемый контроллер API обеспечивает большинство базовых функций для серверной части мобильного приложения, предоставляя конечную точку. Настраиваемый контроллер API

1. В Visual Studio щелкните правой кнопкой мыши папку Controllers, а затем щелкните **Добавить** > **Контроллер**, выберите **Контроллер веб-API 2&mdash;Пустой** и нажмите кнопку **Добавить**.

2. Укажите **имя контроллера**, например `CustomController`, и нажмите кнопку **Добавить**. Будет создан новый класс **CustomController**, наследующий от **ApiController**.

3. В файле нового класса контроллера добавьте следующую инструкцию using:

		using Microsoft.Azure.Mobile.Server.Config;

4. Примените **MobileAppControllerAttribute** к определению класса контроллера API, как показано в следующем примере:

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. Перейдите к папке App\_Startup, откройте файл проекта WebApiConfig.cs и добавьте вызов метода расширения **MapApiControllers**, как показано в следующем примере:

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);

	Обратите внимание, что нет необходимости вызывать **MapApiControllers** при вызове **UseDefaultConfiguration**, который инициализирует все функции.

Любой контроллер, к которому не применен **MobileAppControllerAttribute**, может быть доступен клиентам, но он не будет правильно использоваться клиентами с помощью любого клиентского пакета SDK для мобильных приложений.

## Практическое руководство. Определение контроллера таблиц

Контроллер таблиц предоставляет доступ к данным сущностей в хранилище данных на основе таблиц, например в хранилище базы данных SQL или хранилище таблиц Azure. Контроллеры таблиц наследуют у универсального класса **TableController**, где универсальным типом является сущность в модели, которая представляет схему таблицы следующим образом:

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Контроллеры таблиц инициализируются с помощью метода расширения **AddTables**. В следующем примере инициализируется контроллер таблиц, который использует Entity Framework для доступа к данным:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Пример контроллера таблиц, который использует Entity Framework для доступа к данным в базе данных SQL Azure, см. в классе **TodoItemController** в серверном проекте быстрого запуска, который можно скачать с портала Azure.

## Практическое руководство. Добавление аутентификации в серверный проект

Вы можете добавить аутентификацию в серверный проект, расширив объект **MobileAppConfiguration** и настроив ПО промежуточного слоя OWIN. При установке пакета [Microsoft.Azure.Mobile.Server.Quickstart] и вызове метода расширения **UseDefaultConfiguration** можно перейти к шагу 4.

1. В Visual Studio установите пакет [Microsoft.Azure.Mobile.Server.Authentication]. 

2. Перейдите к папке App\_Startup, откройте файл проекта WebApiConfig.cs и добавьте вызов метода расширения **AddAppServiceAuthentication** во время инициализации, который выглядит следующим образом:

		new MobileAppConfiguration()
			// other features...
			.AddAppServiceAuthentication()
			.ApplyTo(config);

3. В файле проекта Startup.cs добавьте следующую строку кода в начало метода **Configuration**:

		app.UseMobileAppAuthentication(config);

	Это добавляет компонент ПО промежуточного слоя OWIN, который позволяет мобильному приложению Azure проверять токены, выданные связанным шлюзом службы приложений.

4. Добавьте атрибут `[Authorize]` в любой контроллер или метод, который требует аутентификации. Теперь пользователи должны пройти аутентификацию для доступа к этой конечной точке или определенным API.

Чтобы узнать о том, как аутентифицировать клиенты в серверной части мобильного приложения, см. раздел [Добавление проверки подлинности в приложение](app-service-mobile-dotnet-backend-ios-get-started-users-preview.md).

## Практическое руководство. Добавление push-уведомлений в серверный проект

Вы можете добавить push-уведомления в серверный проект, расширив объект **MobileAppConfiguration** и создав клиент центров уведомлений. При установке пакета [Microsoft.Azure.Mobile.Server.Quickstart] и вызове метода расширения **UseDefaultConfiguration** можно перейти к шагу 3.

1. В Visual Studio щелкните правой кнопкой мыши серверный проект и щелкните **Управление пакетами NuGet**, найдите пакет Microsoft.Azure.Mobile.Server.Notifications и нажмите кнопку **Установить**. Будет установлен пакет [Microsoft.Azure.Mobile.Server.Notifications].
 
3. Повторите этот шаг, чтобы установить пакет `Microsoft.Azure.NotificationHubs`, который включает в себя клиентскую библиотеку центров уведомлений.

2. Перейдите к папке App\_Startup, откройте файл проекта WebApiConfig.cs и добавьте вызов метода расширения **AddPushNotifications** во время инициализации, который выглядит следующим образом:

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
        ServiceSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

На этом этапе можно использовать клиент центров уведомлений для отправки push-уведомлений на зарегистрированные устройства. Дополнительные сведения см. в разделе [Добавление push-уведомлений в приложение](app-service-mobile-dotnet-backend-ios-get-started-push-preview.md). Чтобы узнать больше обо всем, что можно сделать с помощью центров уведомлений, см. раздел [Общая информация о центрах уведомлений Azure](../notification-hubs/notification-hubs-overview.md).

## Практическое руководство. Публикация серверного проекта

Чтобы опубликовать серверный проект в Azure, выполните следующие действия:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=August15_HO8-->