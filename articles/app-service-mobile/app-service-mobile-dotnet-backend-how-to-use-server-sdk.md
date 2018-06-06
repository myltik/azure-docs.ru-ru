---
title: Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений | Документация Майкрософт
description: Узнайте, как работать с пакетом SDK для внутреннего сервера .NET для мобильных приложений службы приложений Azure.
keywords: служба приложений, служба приложений azure, мобильное приложение, мобильная служба, масштабировать, масштабируемый, разработка приложений, разработка приложений azure
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 8216dafdd846f10ca1c8fc33b710a093aca20c7b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "31589634"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

В этом разделе показано, как использовать пакет SDK для внутреннего сервера .NET в основных сценариях применения мобильных приложений службы приложений Azure. Пакет SDK для мобильных приложений Azure помогает работать с мобильными клиентами из своего приложения ASP.NET.

> [!TIP]
> [Пакет SDK сервера .NET для мобильных приложений Azure][2] предлагается в виде открытого исходного кода на сайте GitHub. Репозиторий содержит весь исходный код, а также полный набор модульных тестов пакета SDK для сервера и некоторые примеры проектов.
>
>

## <a name="reference-documentation"></a>Справочная документация
Справочную документацию по пакету SDK для сервера см. здесь: [Справочник по .NET для мобильных приложений Azure][1].

## <a name="create-app"></a>Практическое руководство. Создание серверной части мобильного приложения .NET
Если вы начинаете новый проект, приложение службы приложений можно создать с помощью [портал Azure] или программы Visual Studio. Вы можете запустить приложение службы приложений локально или опубликовать проект в мобильном приложении облачной службы приложений.

Если вы добавляете мобильные возможности к имеющемуся проекту, см. раздел [Практическое руководство. Скачивание и инициализация пакета SDK](#install-sdk).

### <a name="create-a-net-backend-using-the-azure-portal"></a>Создание серверной части .NET с помощью портала Azure
Чтобы создать мобильный внутренний сервер службы приложений, следуйте указаниям из этого [краткого руководства][3] или сделайте следующее:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Вернитесь в колонку *Начало работы* и в разделе **Create a table API** (Создание API таблицы) в поле **Backend language** (Язык серверной части) выберите значение **C#**. Щелкните **Скачать**, извлеките сжатые файлы проекта на локальный компьютер и откройте решение в Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Создание серверной части .NET с помощью Visual Studio 2017

Установите рабочую нагрузку Azure с помощью установщика Visual Studio для публикации в проект мобильных приложений Azure из Visual Studio. После установки пакета SDK создайте приложение ASP.NET, сделав следующее:

1. Откройте диалоговое окно **Новый проект** (последовательно выберите **Файл** > **Создать** > **Проект**).
2. Разверните раздел **Visual C#** и выберите **Интернет**.
3. Выберите **Веб-приложение ASP.NET (.NET Framework)**.
4. Введите имя проекта. Нажмите кнопку **ОК**.
5. Выберите **Мобильное приложение Azure** из списка шаблонов.
6. Нажмите кнопку **ОК**, чтобы создать решение.
7. Правой кнопкой мыши щелкните проект в **обозревателе решений** и выберите **Опубликовать…**, затем в качестве цели публикации выберите **Служба приложений**.
8. Следуйте инструкциям на экране для прохождения аутентификации и выберите новую или существующую службу приложений Azure для публикации.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Создание серверной части .NET с помощью Visual Studio 2015

Чтобы создать проект мобильных приложений Azure в Visual Studio, необходимо установить [пакет Azure SDK для .NET][4] версии 2.9.0 или выше. После установки пакета SDK создайте приложение ASP.NET, сделав следующее:

1. Откройте диалоговое окно **Новый проект** (последовательно выберите **Файл** > **Создать** > **Проект**).
2. Разверните раздел **Шаблоны** > **Visual C#** и выберите **Интернет**.
3. Выберите **Веб-приложение ASP.NET**.
4. Введите имя проекта. Нажмите кнопку **ОК**.
5. В разделе *ASP.NET 4.5.2 Templates* (Шаблоны ASP.NET 4.5.2) выберите **Мобильное приложение Azure**. Установите флажок **Разместить в облаке** , чтобы создать мобильный внутренний сервер в облаке, в котором можно опубликовать этот проект.
6. Последовательно выберите **ОК**.

## <a name="install-sdk"></a>Практическое руководство. Скачивание и инициализация пакета SDK
Пакет SDK доступен на сайте [NuGet.org]. Этот пакет включает в себя базовые функции, необходимые для начала работы пакетом SDK. Для инициализации пакета SDK необходимо выполнить действия с объектом **HttpConfiguration** .

### <a name="install-the-sdk"></a>Установка пакета SDK
Чтобы установить пакет SDK, щелкните правой кнопкой мыши проект сервера в Visual Studio, выберите **Управление пакетами NuGet**, найдите пакет [Microsoft.Azure.Mobile.Server], а затем щелкните **Установить**.

### <a name="server-project-setup"></a> Инициализация серверного проекта
Инициализацию проекта внутреннего сервера .NET можно выполнить аналогично инициализации проектов ASP.NET — включив класс запуска OWIN. Убедитесь, что указана ссылка на пакет NuGet `Microsoft.Owin.Host.SystemWeb`. Чтобы добавить этот класс в Visual Studio, щелкните правой кнопкой мыши проект сервера и выберите **Добавить** >
**Создать элемент**, а затем — **Интернет** > **Общие** > **Класс Startup OWIN**.  Будет создан класс с указанным ниже атрибутом.

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

В методе `Configuration()` класса запуска OWIN используйте объект **HttpConfiguration** , чтобы настроить среду мобильных приложений Azure.
В следующем примере выполняется инициализация серверного проекта без дополнительных функций.

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Чтобы включить отдельные функции, перед вызовом **ApplyTo** необходимо вызвать методы расширения для объекта **MobileAppConfiguration**. Например, следующий код добавляет во время инициализации маршруты по умолчанию для всех контроллеров API, имеющих атрибут `[MobileAppController]` :

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

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

Используются следующие методы расширения:

* `AddMobileAppHomeController()` предоставляет домашнюю страницу мобильных приложений Azure по умолчанию.
* `MapApiControllers()` предоставляет возможности настраиваемого интерфейса API для контроллеров веб-API с атрибутом `[MobileAppController]`.
* `AddTables()` обеспечивает сопоставление конечных точек `/tables` контроллерами таблиц.
* `AddTablesWithEntityFramework()` — это сокращение для сопоставления конечных точек `/tables` с помощью контроллеров на основе Entity Framework.
* `AddPushNotifications()` предоставляет простой способ регистрации устройств в концентраторах уведомлений.
* `MapLegacyCrossDomainController()` предоставляет стандартные заголовки CORS для локальной разработки.

### <a name="sdk-extensions"></a>Расширения пакета SDK
Следующие пакеты расширений на основе NuGet предоставляют различные возможности мобильных устройств, которые может использовать ваше приложение. Вы можете включить расширения во время инициализации с помощью объекта **MobileAppConfiguration** .

* [Microsoft.Azure.Mobile.Server.Quickstart] поддерживает базовую настройку мобильных приложений. Чтобы добавить его в конфигурацию, во время инициализации необходимо вызвать метод расширения **UseDefaultConfiguration**. Это расширение включает в себя следующие расширения: пакеты Notifications, Authentication, Entity, Tables, Crossdomain и Home. Этот пакет используется в кратком руководстве по созданию мобильных приложений, доступном на портале Azure.
* [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) реализует стандартную *страницу «Это мобильное приложение запущено и работает»* для корневого каталога веб-сайта. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddMobileAppHomeController** .
* [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) содержит классы для работы с данными и настраивает конвейер данных. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddTables** .
* [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) позволяет платформе Entity Framework получать доступ к данным в базе данных SQL. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddTablesWithEntityFramework** .
* [Microsoft.Azure.Mobile.Server.Authentication] включает функцию проверки подлинности и настраивает ПО промежуточного слоя OWIN, используемое для проверки токенов. Чтобы добавить его в конфигурацию, вызовите методы расширения **AddAppServiceAuthentication** и **IAppBuilder**.**UseAppServiceAuthentication**.
* [Microsoft.Azure.Mobile.Server.Notifications] включает push-уведомления и определяет их конечную точку регистрации. Чтобы добавить его в конфигурацию, вызовите метод расширения **AddPushNotifications** .
* [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) создает контроллер, который передает устаревшим веб-браузерам данные из вашего мобильного приложения. Чтобы добавить его в конфигурацию, вызовите метод расширения **MapLegacyCrossDomainController** .
* [Microsoft.Azure.Mobile.Server.Login] предоставляет статический метод AppServiceLoginHandler.CreateToken(), используемый во время пользовательской проверки подлинности.

## <a name="publish-server-project"></a>Практическое руководство. Публикация серверного проекта
В этом разделе показано, как опубликовать серверный проект .NET из Visual Studio. Вы также можете развернуть проект серверной части с помощью [Git](../app-service/app-service-deploy-local-git.md) или любых других доступных методов.

1. В Visual Studio выполните повторную сборку проекта, чтобы восстановить пакеты NuGet.
2. В Обозревателе решений щелкните проект правой кнопкой мыши и выберите **Опубликовать**. При первой публикации необходимо определить профиль публикации. Если профиль уже определен, выделите его и нажмите кнопку **Опубликовать**.
3. Если появится запрос на выбор цели публикации, щелкните **Служба приложений Microsoft Azure** > **Далее**, а затем (при необходимости) войдите, используя учетные данные Azure.
   Visual Studio загрузит параметры публикации из Azure и безопасно сохранит их.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Выберите свою **подписку**, а затем в раскрывающемся списке **Представление** выберите **Тип ресурса**, разверните **Мобильное приложение** и щелкните серверную часть мобильного приложения. После этого нажмите кнопку **ОК**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Проверьте сведения в профиле публикации и нажмите кнопку **Опубликовать**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    При успешной публикации серверной части мобильного приложения отобразится соответствующая целевая страница.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Практическое руководство. Определение контроллера таблиц
Определите контроллер таблиц, чтобы предоставить доступ к таблице SQL мобильным клиентам.  Настройка контроллера таблиц состоит из трех шагов.

1. Создание класса объекта передачи данных.
2. Настройка ссылки на таблицу в мобильном классе DbContext.
3. Создание контроллера таблиц.

Объект передачи данных — это обычный объект C#, наследуемый от `EntityData`.  Например: 

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

Объект передачи данных используется для определения таблиц в Базе данных SQL.  Чтобы создать запись в базе данных, добавьте свойство `DbSet<>` в используемый объект DbContext.  В шаблоне проекта по умолчанию для мобильных приложений Azure DbContext называется `Models\MobileServiceContext.cs`.

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Если пакет Azure SDK установлен, можно создать контроллер таблиц шаблонов следующим образом.

1. Щелкните правой кнопкой мыши папку "Контроллеры" и выберите **Добавить** > **Контроллер...**.
2. Выберите параметр **Контроллер таблиц мобильных приложений Azure**, а затем щелкните **Добавить**.
3. В диалоговом окне **Добавление контроллера** :
   * В раскрывающемся списке **Класс модели** выберите новый объект передачи данных.
   * В раскрывающемся списке **DbContext** выберите класс мобильных служб DbContext.
   * Будет автоматически создано имя контроллера.
4. Щелкните **Добавить**.

Серверный проект быстрого запуска содержит пример для простого класса **TodoItemController**.

### <a name="adjust-pagesize"></a>Практическое руководство: изменение размера постраничного представления таблиц
По умолчанию мобильные приложения Azure выдают по 50 записей на запрос.  Разбивка на страницы гарантирует, что клиент не связывает ни поток пользовательского интерфейса, ни сервер надолго, обеспечивая таким образом удобство работы пользователей. Чтобы изменить размер постраничного представления таблиц, увеличьте допустимый размер запроса на серверной стороне и размер страниц на стороне клиента. Допустимый размер запроса на серверной стороне настраивается с помощью атрибута `EnableQuery`.

    [EnableQuery(PageSize = 500)]

Значение параметра PageSize должно быть больше или равно размеру, запрошенному клиентом.  Сведения об изменении размера страницы для изменения клиента см. в методических указаниях по соответствующему клиенту.

## <a name="how-to-define-a-custom-api-controller"></a>Практическое руководство. Определение настраиваемого контроллера API
Настраиваемый контроллер API обеспечивает большинство базовых функций для серверной части мобильного приложения, предоставляя конечную точку. Вы можете зарегистрировать контроллер API для мобильных приложений с помощью атрибута [MobileAppController]. Атрибут `MobileAppController` регистрирует маршрут, настраивает сериализатор JSON мобильных приложений, а также включает [проверку версии клиента](app-service-mobile-client-and-server-versioning.md).

1. В Visual Studio щелкните правой кнопкой мыши папку "Контроллеры", щелкните **Добавить** > **Контроллер**, выберите **Контроллер Web API 2&mdash;пустой**, а затем щелкните **Добавить**.
2. Укажите **имя контроллера**, например `CustomController`, и щелкните **Добавить**.
3. В файле нового класса контроллера добавьте следующую инструкцию using:

        using Microsoft.Azure.Mobile.Server.Config;
4. Примените атрибут **[MobileAppController]** к определению класса контроллера API, как показано в примере ниже.

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. Добавьте в файл App_Start/Startup.MobileApp.cs вызов метода расширения **MapApiControllers**, как показано в примере ниже.

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Кроме того, можно использовать метод расширения `UseDefaultConfiguration()` вместо `MapApiControllers()`. Клиенты могут получить доступ к любому контроллеру, к которому не применен атрибут **MobileAppControllerAttribute**. Однако клиенты, использующие любой пакет SDK для клиента мобильного приложения, не смогут правильно использовать этот контроллер.

## <a name="how-to-work-with-authentication"></a>Практическое руководство: работа с проверкой подлинности
В мобильных приложениях Azure для защиты мобильного внутреннего сервера используется проверка подлинности или авторизация службы приложений.  В этом разделе показано, как выполнять следующие задачи проверки подлинности в серверном проекте .NET:

* [Практическое руководство. Добавление аутентификации в серверный проект](#add-auth)
* [Практическое руководство. Использование пользовательской проверки подлинности для приложения](#custom-auth)
* [Практическое руководство. Получение сведений о пользователе, прошедшем проверку подлинности](#user-info)
* [Практическое руководство. Ограничение доступа к данным для авторизованных пользователей](#authorize)

### <a name="add-auth"></a>Практическое руководство. Добавление аутентификации в серверный проект
Вы можете добавить проверку подлинности в серверный проект, унаследовав от объекта **MobileAppConfiguration** и настроив ПО промежуточного слоя OWIN. Когда вы установите пакет [Microsoft.Azure.Mobile.Server.Quickstart] и вызовете метод расширения **UseDefaultConfiguration** , переходите к шагу 3.

1. В Visual Studio установите пакет [Microsoft.Azure.Mobile.Server.Authentication] .
2. В файле проекта Startup.cs добавьте следующую строку кода в начало метода **Configuration** :

        app.UseAppServiceAuthentication(config);

    Этот компонент ПО промежуточного слоя OWIN проверяет маркеры, выданные соответствующим шлюзом службы приложений.
3. Добавьте атрибут `[Authorize]` во все контроллеры и методы, в которых нужна проверка подлинности.

Сведения о том, как проверять подлинность клиентов в серверных мобильных приложениях, см. в статье [Добавление проверки подлинности в приложение iOS](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Практическое руководство. Использование пользовательской проверки подлинности для приложения
> [!IMPORTANT]
> Чтобы включить настраиваемую аутентификацию, необходимо сперва включить на портале Azure проверку подлинности службы приложений без выбора поставщика вашей службы приложений. Это активирует переменную среды WEBSITE_AUTH_SIGNING_KEY при размещении.
> 
> 
Если вы не хотите использовать один из поставщиков проверки подлинности и авторизации службы приложений, можно реализовать свою собственную систему входа. Установите пакет [Microsoft.Azure.Mobile.Server.Login] , чтобы упростить создание маркеров проверки подлинности.  Предоставьте свой собственный код для проверки учетных данных пользователя. Например, можно выполнять проверку с помощью дополненных случайными данными и хэшированных паролей в базе данных. В следующем примере за эти проверки отвечает метод `isValidAssertion()` (определенный в другом месте).

Пользовательская проверка подлинности осуществляется путем создания ApiController и использования действий `register` и `login`. Для сбора данных пользователя клиент должен использовать настраиваемый пользовательский интерфейс.  Затем эти данные отправляются в API с помощью стандартного вызова HTTP POST. После проверки утверждения сервером выпускается маркер с помощью метода `AppServiceLoginHandler.CreateToken()` .  В ApiController **нельзя** использовать атрибут `[MobileAppController]`.

Пример действия `login` :

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

В предыдущем примере LoginResult и LoginResultUser — это сериализуемые объекты, содержащие необходимые свойства. Клиент ожидает возвращения ответов входа в виде объектов JSON в следующем формате.

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Метод `AppServiceLoginHandler.CreateToken()` включает в себя параметры *audience* и *issuer*. Для обоих этих параметров обычно устанавливаются значения, равные URL-адресу корня приложения, с помощью схемы HTTPS. Точно так же для параметра *secretKey* нужно задать значение ключа подписывания приложения. Не распространяйте ключ подписывания в клиенте, так как он может быть использован для создания ключей и олицетворения пользователей. Если приложение размещено в службе приложений, этот ключ подписывания можно получить из переменной среды *WEBSITE\_AUTH\_SIGNING\_KEY*. Если это необходимо для отладки в локальной среде, можно получить ключ и сохранить его как параметр приложения, выполнив инструкции из раздела [Локальная отладка с проверкой подлинности](#local-debug) .

Выданный маркер может содержать другие утверждения и иметь другую дату окончания срока действия.  Выданный маркер должен содержать по крайней мере утверждение субъекта (**sub**).

Чтобы обеспечить поддержку стандартного клиентского метода `loginAsync()` , можно перегрузить маршрут проверки подлинности.  Если клиент вызывает `client.loginAsync('custom');` для входа, ваш маршрут должен быть следующим: `/.auth/login/custom`.  Вы можете задать маршрут для контроллера пользовательской проверки подлинности с помощью `MapHttpRoute()`.

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Подход с использованием `loginAsync()` гарантирует, что маркер проверки подлинности прикрепляется к каждому последующему вызову службы.
>
>

### <a name="user-info"></a>Практическое руководство. Получение сведений о пользователе, прошедшем проверку подлинности
При проверке подлинности пользователя у службы приложений в коде серверной части .NET вы можете получить присвоенный идентификатор пользователя и другие сведения. Сведения о пользователе можно использовать для принятия решений об авторизации в серверной части. Следующий код получает идентификатор пользователя, связанный с запросом.

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID является производным от идентификатора пользователя, предоставленного поставщиком, и является статическим для данного пользователя и поставщика входа в систему.  Идентификатор безопасности (SID) имеет значение NULL для недопустимых маркеров проверки подлинности.

Кроме того, служба приложений позволяет запрашивать конкретные утверждения от поставщика входа в систему. Каждый поставщик удостоверений может предоставлять больше сведений с помощью пакета SDK поставщика удостоверений.  Например, можно использовать Graph API Facebook для получения сведений о друзьях.  Вы можете задать утверждения, запрошенные в колонке поставщика на портале Azure. Некоторые утверждения требуют дополнительной настройки поставщика удостоверений.

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

Чтобы предоставить метод расширения **GetAppServiceIdentityAsync**, необходимо добавить выражение для `System.Security.Principal`.

### <a name="authorize"></a>Практическое руководство. Ограничение доступа к данным для авторизованных пользователей
В предыдущем разделе мы показали, как получить идентификатор пользователя, прошедшего проверку подлинности. По этому значению можно ограничить доступ к данным и другим ресурсам. Например, добавив в таблицы столбец userId и отфильтровав результаты запросов по идентификатору пользователя, вы получите простой способ ограничения возвращаемых данных только данными для авторизованных пользователей. Следующий код возвращает строки данных только в том случае, если SID совпадает со значением в столбце UserId таблицы TodoItem.

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Метод `Query()` возвращает `IQueryable`, которым можно управлять с помощью LINQ для обработки фильтрации.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Практическое руководство. Добавление push-уведомлений в серверный проект
Добавьте push-уведомления в проект сервера, расширив объект **MobileAppConfiguration** и создав клиент концентраторов уведомлений.

1. В Visual Studio щелкните правой кнопкой мыши серверный проект, выберите пункт **Управление пакетами NuGet**, найдите `Microsoft.Azure.Mobile.Server.Notifications` и нажмите кнопку **Установить**.
2. Повторите это действие, чтобы установить пакет `Microsoft.Azure.NotificationHubs` , который включает в себя клиентскую библиотеку центров уведомлений.
3. Перейдите к папке App_Start, откройте файл Startup.MobileApp.cs и добавьте вызов метода расширения **AddPushNotifications()** на этапе инициализации.

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
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

Теперь вы можете использовать клиент концентраторов уведомлений для отправки push-уведомлений на зарегистрированные устройства. Дополнительные сведения см. в статье [Добавление push-уведомлений в приложение iOS](app-service-mobile-ios-get-started-push.md). Дополнительные сведения о Центрах уведомлений см. в статье [Концентраторы уведомлений Azure](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Практическое руководство. Включение принудительной отправки push-уведомлений с использованием тегов
Центры уведомлений позволяют отправлять целевые уведомления в определенные регистрации с помощью тегов. Несколько тегов создаются автоматически.

* Идентификатор установки идентифицирует определенное устройство.
* Идентификатор пользователя идентифицирует определенного пользователя на основе прошедшего проверку подлинности SID.

Доступ к идентификатору установки можно получить из свойства **installationId** в **MobileServiceClient**.  В примере ниже показано, как использовать идентификатор установки для добавления тега в конкретную установку в центрах уведомлений.

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Все теги, предоставленные клиентом во время регистрации push-уведомлений, игнорируются серверной частью при создании установки. Чтобы включить клиент для добавления тегов в установку, необходимо создать настраиваемый API, который добавляет теги, с помощью шаблона выше.

В качестве примера см. [добавленные клиентом теги push-уведомлений][5] в полном примере краткого руководства по созданию мобильных приложений службы приложений.

## <a name="push-user"></a>Практическое руководство. Отправка push-уведомлений аутентифицированному пользователю
Когда прошедший проверку пользователь регистрируется для работы с push-уведомлениями, в регистрацию автоматически добавляется тег с идентификатором пользователя. С помощью этого тега можно отправлять push-уведомления на все устройства, зарегистрированные этим пользователем. Следующий код получает идентификатор SID пользователя, выполняющего запрос, и отправляет шаблонное push-уведомление в каждую регистрацию устройства для этого пользователя.

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

При регистрации для работы с push-уведомлениями на клиенте, прошедшем проверку подлинности, убедитесь, что проверка подлинности завершена, и только после этого начинайте регистрацию. Дополнительные сведения см. в разделе [Отправка push-уведомлений пользователям][6] в полном примере краткого руководства по мобильным приложениям службы приложений для серверной части .NET.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Практическое руководство. Отладка и устранение неполадок пакета SDK для сервера .NET
Служба приложений Azure предоставляет несколько методов отладки и устранения неполадок в приложениях ASP.NET.

* [Мониторинг службы приложений Azure](../app-service/web-sites-monitor.md)
* [Включение ведения журналов диагностики в службе приложений Azure](../app-service/web-sites-enable-diagnostic-log.md)
* [Диагностика службы приложений Azure в Visual Studio](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Ведение журналов
Журналы диагностики службы приложений вы можете вести с помощью стандартной записи трассировки ASP.NET. Перед записью в журналы необходимо включить диагностику в серверной части мобильного приложения.

Для включения диагностики и записи в журналы сделайте следующее:

1. Следуйте указаниям в разделе [Включение диагностики](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Добавьте в файл с кодом следующую инструкцию using:

        using System.Web.Http.Tracing;
3. Создайте модуль записи трассировки для записи из серверного приложения .NET в журналы диагностики следующим образом:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Повторно опубликуйте серверный проект и запустите серверную часть мобильного приложения с ведением журнала.
5. Загрузите и оцените журналы, как описано в разделе [Практическое руководство. Загрузка журналов](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Локальная отладка с проверкой подлинности
Вы можете запустить приложение локально, чтобы проверить изменения перед их публикацией в облаке. Для большинства серверных систем мобильных приложений Azure нажмите клавишу *F5* во время работы в Visual Studio. Однако при проверке подлинности следует учитывать некоторые дополнительные рекомендации.

Необходимо облачное мобильное приложение, в котором настроены проверка подлинности и авторизация службы приложений, а в клиенте должна быть конечная точка облака, указанная как узел для альтернативного входа. Конкретные указания можно найти в документации для клиентской платформы.

Убедитесь, что в мобильном внутреннем сервере установлен пакет [Microsoft.Azure.Mobile.Server.Authentication] . В приложении добавьте следующий код в класс запуска OWIN после применения `MobileAppConfiguration` к объекту `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

В приведенном выше примере необходимо настроить параметры приложения *authAudience* и *authIssuer* в файле Web.config, задав для каждого из них значение URL-адреса корня приложения в формате HTTPS. Точно так же для параметра *authSigningKey* нужно задать значение ключа подписывания приложения.
Чтобы получить ключ подписывания, сделайте следующее:

1. Перейдите в свое приложение на [портал Azure]
2. Щелкните **Инструменты**, **Kudu**, **Перейти**.
3. На сайте управления Kudu щелкните **Среда**.
4. Найдите значение для *WEBSITE\_AUTH\_SIGNING\_KEY*.

Укажите ключ подписывания для параметра *authSigningKey* в файле конфигурации локального приложения.  Теперь на мобильном внутреннем сервере при работе локально есть все необходимое для проверки маркеров, которые клиент получает от конечной точки облака.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[портал Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
