<properties 
	pageTitle="Как создать веб-приложение с использованием кэша Redis | Microsoft Azure" 
	description="Узнайте, как создать веб-приложение с использованием кэша Redis" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="04/28/2016" 
	ms.author="sdanie"/>

# Как создать веб-приложение с использованием кэша Redis

В этом руководстве описано, как создать и развернуть веб-приложение ASP.NET в веб-приложение службы приложений Azure с помощью Visual Studio 2015. В примере приложения отображается список статистических данных команды из базы данных и различные способы использования кэша Redis для Azure для хранения и извлечения данных из кэша. Завершив работу с руководством, вы получите рабочее веб-приложение, которое выполняет чтение и запись в базе данных, оптимизировано для работы с кэшем Redis для Azure и размещено в Azure.

Вы узнаете следующее:

-	как создать веб-приложение ASP.NET MVC 5 в Visual Studio;
-	как получить доступ к данным из базы данных с использованием Entity Framework;
-	как улучшить пропускную способность данных и снизить нагрузку на базу данных за счет хранения и извлечения данных с помощью кэша Redis для Azure;
-	как получить 5 лучших команд с помощью отсортированного набора Redis;
-	как подготовить ресурсы Azure к работе для приложения с помощью шаблона ARM;
-	как опубликовать приложение в Azure с помощью Visual Studio.

## Предварительные требования

Для работы с этим руководством необходимо следующее:

-	[Учетная запись Azure](#azure-account)
-	[Visual Studio 2015 с пакетом Azure SDK для .NET](#visual-studio-2015-with-the-azure-sdk-for-net).

### Учетная запись Azure

Для работы с этим руководством требуется учетная запись Azure. Вы можете:

* [Открыть бесплатную учетную запись Azure](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Вы получаете кредиты, которые можно использовать, чтобы попробовать платные службы Azure. После израсходования кредитов ваша учетная запись не исчезнет. Вы сможете использовать ее для работы с бесплатными службами и функциями Azure.
* [Активировать преимущества подписчика Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). ваша подписка MSDN каждый месяц приносит вам кредиты, которые можно использовать для оплаты за службы Azure.

### Visual Studio 2015 с пакетом Azure SDK для .NET

Это руководство написано для Visual Studio 2015 с пакетом [Azure SDK для .NET](../dotnet-sdk.md) 2.8.2 или более поздней версии. Скачайте последний пакет Azure SDK для Visual Studio 2015 [отсюда](http://go.microsoft.com/fwlink/?linkid=518003). Будет автоматически установлена программа Visual Studio с пакетом SDK (если она еще не установлена).

Если на вашем компьютере установлена версия Visual Studio 2013, можно [скачать последнюю версию пакета Azure SDK для Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Некоторые снимки экранов, приведенные в этом руководстве, могут отличаться от реальных.

>[AZURE.NOTE] В зависимости от того, сколько зависимостей пакета SDK уже имеется на компьютере, установка пакета SDK может занять определенное время, от нескольких минут до получаса или более.

## Создание проекта Visual Studio

1. Откройте Visual Studio и последовательно выберите **Файл**, **Создать** и **Проект**.

2. Разверните узел **Visual C#** в списке **Шаблоны**, выберите пункт **Облако** и щелкните **Веб-приложение ASP.NET**. Убедитесь, что выбрана платформа **.NET Framework 4.5.2**. В текстовом поле **Имя** введите **ContosoTeamStats** и нажмите кнопку **ОК**.
 
    ![Создание проекта][cache-create-project]

3. Выберите **MVC** в качестве типа проекта. Снимите флажок **Разместить в облаке**. На следующих шагах руководства вы [подготовите ресурсы Azure к работе](#provision-the-azure-resources) и [опубликуете приложение в Azure](#publish-the-application-to-azure). Пример подготовки веб-приложения службы приложений к работе в Visual Studio с установленным флажком **Разместить в облаке** см. в статье [Начало работы с веб-приложениями в службе приложений Azure с помощью ASP.NET и Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).

    ![Выбор шаблона проекта][cache-select-template]

4. Нажмите кнопку **ОК**, чтобы создать проект.

## Создание приложения ASP.NET MVC

В этом разделе руководства описывается создание базового приложения, которое выполняет чтение статистики команды из базы данных и отображает ее.

-	[Добавление модели](#add-the-model)
-	[Добавление контроллера](#add-the-controller)
-	[Настройка представлений](#configure-the-views)

### Добавление модели

1. В **обозревателе решений** щелкните правой кнопкой мыши папку **Модели** и последовательно выберите **Добавить** и **Класс**. 

    ![Добавление модели][cache-model-add-class]

2. Введите `Team` в качестве имени класса и нажмите кнопку **Добавить**.

    ![Добавление класса модели][cache-model-add-class-dialog]

3. В начале файла `Team.cs` замените операторы `using` на такие:


		using System;
		using System.Collections.Generic;
		using System.Data.Entity;
		using System.Data.Entity.SqlServer;


4. Замените определение класса `Team` на следующий фрагмент кода, содержащий обновленное определение класса `Team`, а также некоторые другие вспомогательные классы Entity Framework. Дополнительные сведения о подходе Code First в Entity Framework, использованном в этом руководстве, см. в статье [Использование Code First для создания базы данных](https://msdn.microsoft.com/data/jj193542).


		public class Team
		{
		    public int ID { get; set; }
		    public string Name { get; set; }
		    public int Wins { get; set; }
		    public int Losses { get; set; }
		    public int Ties { get; set; }
		
		    static public void PlayGames(IEnumerable<Team> teams)
		    {
		        // Simple random generation of statistics.
		        Random r = new Random();
		
		        foreach (var t in teams)
		        {
		            t.Wins = r.Next(33);
		            t.Losses = r.Next(33);
		            t.Ties = r.Next(0, 5);
		        }
		    }
		}
		
		public class TeamContext : DbContext
		{
		    public TeamContext()
		        : base("TeamContext")
		    {
		    }
		
		    public DbSet<Team> Teams { get; set; }
		}
		
		public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
		{
		    protected override void Seed(TeamContext context)
		    {
		        var teams = new List<Team>
		        {
		            new Team{Name="Adventure Works Cycles"},
		            new Team{Name="Alpine Ski House"},
		            new Team{Name="Blue Yonder Airlines"},
		            new Team{Name="Coho Vineyard"},
		            new Team{Name="Contoso, Ltd."},
		            new Team{Name="Fabrikam, Inc."},
		            new Team{Name="Lucerne Publishing"},
		            new Team{Name="Northwind Traders"},
		            new Team{Name="Consolidated Messenger"},
		            new Team{Name="Fourth Coffee"},
		            new Team{Name="Graphic Design Institute"},
		            new Team{Name="Nod Publishers"}
		        };
		
		        Team.PlayGames(teams);
		
		        teams.ForEach(t => context.Teams.Add(t));
		        context.SaveChanges();
		    }
		}
		
		public class TeamConfiguration : DbConfiguration
		{
		    public TeamConfiguration()
		    {
		        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
		    }
		}


2. В **обозревателе решений** дважды щелкните файл **web.config**, чтобы открыть его.

    ![Web.config][cache-web-config]

3.  Добавьте строку подключения, указанную ниже, в раздел `connectionStrings`. Имя строки подключения должно соответствовать имени класса контекста базы данных Entity Framework (`TeamContext`).

		<add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    После добавления строки подключения раздел `connectionStrings` должен выглядеть следующим образом:


		<connectionStrings>
			<add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
				providerName="System.Data.SqlClient" />
			<add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" 	providerName="System.Data.SqlClient" />
		</connectionStrings>

### Добавление контроллера

1. Нажмите клавишу **F6**, чтобы скомпилировать проект. 
2. В **обозревателе решений** щелкните правой кнопкой мыши папку **Контроллеры** и последовательно выберите пункты **Добавить** и **Контроллер**.

    ![Добавление контролера][cache-add-controller]

3. Выберите **Контроллер MVC 5 с представлениями, использующий Entity Framework** и нажмите кнопку **Добавить**. Если после нажатия кнопки **Добавить** появилась ошибка, убедитесь, что вы сначала скомпилировали проект.

    ![Добавление класса контроллера][cache-add-controller-class]

5. В раскрывающемся списке **Класс модели** выберите **Team (ContosoTeamStats.Models)**. В раскрывающемся списке **Класс контекста данных** выберите **TeamContext (ContosoTeamStats.Models)**. Введите `TeamsController` в текстовом поле **Имя контроллера** (если оно не заполнено автоматически). Нажмите кнопку **Добавить**, чтобы создать класс контроллера и добавить представления по умолчанию.

    ![Настройка контроллера][cache-configure-controller]

4. В **обозревателе решений** разверните узел **Global.asax** и дважды щелкните файл **Global.asax.cs**, чтобы открыть его.

    ![Global.asax.cs][cache-global-asax]

5. В начале файла добавьте такие два оператора после остальных операторов using:


        using System.Data.Entity;
		using ContosoTeamStats.Models;


6. В конце метода `Application_Start` добавьте следующую строку кода:


	    Database.SetInitializer<TeamContext>(new TeamInitializer());


7. В **обозревателе решений** разверните узел `App_Start` и дважды щелкните `RouteConfig.cs`.

    ![RouteConfig.cs.][cache-RouteConfig-cs]

8. В коде ниже замените `controller = "Home"` в методе `RegisterRoutes` на `controller = "Teams"`, как показано в следующем примере.


	    routes.MapRoute(
	        name: "Default",
	        url: "{controller}/{action}/{id}",
	        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
	    );


### Настройка представлений

1. В **обозревателе решений** разверните папку **Представления**, а затем — папку **Общее** и дважды щелкните файл **_Layout.cshtml**.

    ![_Layout.cshtml][cache-layout-cshtml]

2. Измените содержимое элемента `title` и замените `My ASP.NET Application` на `Contoso Team Stats`, как показано в следующем примере.


	    <title>@ViewBag.Title - Contoso Team Stats</title>


3. В разделе `body` обновите первый оператор `Html.ActionLink`, а также замените `Application name` на `Contoso Team Stats` и `Home` на `Teams`.
	-	До: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
	-	После: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![Изменения в коде][cache-layout-cshtml-code]

4. Нажмите клавиши **CTRL+F5**, чтобы скомпилировать и запустить приложение. В этой версии приложения результаты считываются непосредственно из базы данных. Обратите внимание на действия **Создать**, **Изменить**, **Сведения** и **Удалить**, автоматически добавленные в приложение с помощью шаблона **Контроллер MVC 5 с представлениями, использующий Entity Framework**. В следующем разделе руководства будет добавлен кэш Redis для оптимизации доступа к данным и добавления дополнительных функций в приложение.

![Начальное приложение][cache-starter-application]

## Настройка приложения для использования кэша Redis

В этом разделе руководства будет настроен пример приложения для хранения и извлечения статистики команды Contoso из экземпляра кэша Redis для Azure с помощью клиента кэша [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

-	[Настройка приложения для использования StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
-	[Обновление класса TeamsController для возвращения результатов из кэша или базы данных](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
-	[Обновление методов создания, изменения и удаления для работы с кэшем](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
-	[Обновление представления индекса команд для работы с кэшем](#update-the-teams-index-view-to-work-with-the-cache)


### Настройка приложения для использования StackExchange.Redis

1. Чтобы настроить клиентское приложение в Visual Studio, используя пакет StackExchange.Redis из NuGet, щелкните правой кнопкой мыши **обозреватель решений** и выберите **Управление пакетами NuGet**. 

    ![Управление пакетами NuGet][redis-cache-manage-nuget-menu]

2. Введите **StackExchange.Redis** в текстовое поле поиска, выберите нужную версию в результатах и нажмите кнопку **Установить**.

    ![Пакет NuGet StackExchange.Redis][redis-cache-stack-exchange-nuget]

    Пакет NuGet загружает и добавляет необходимые ссылки на сборки в клиентском приложении для доступа к кэшу Azure Redis из клиента кэша StackExchange.Redis. При желании использовать версию клиентской библиотеки **StackExchange.Redis** со строгими именами выберите **StackExchange.Redis.StrongName**, в противном случае выберите **StackExchange.Redis**.

3. В **обозревателе решений** разверните папку **Контроллеры** и дважды щелкните файл **TodoItemController.cs**, чтобы открыть его.

    ![Контроллер команд][cache-teamscontroller]

4. Добавьте в файл **TeamsController.cs** следующие два оператора using:

	    using System.Configuration;
        using StackExchange.Redis;

5. Добавьте в класс `TeamsController` следующие два свойства:

	    // Redis Connection string info
	    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	    {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
	    });
	
	    public static ConnectionMultiplexer Connection
	    {
	        get
	        {
	            return lazyConnection.Value;
	        }
	    }
  
1. Создайте на компьютере файл с именем `WebAppPlusCacheAppSecrets.config` и поместите его в расположение, которое не будет записано после изменения с исходным кодом примера приложения, если его нужно будет записать где-нибудь после изменения. В этом примере файл `AppSettingsSecrets.config` находится в папке `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

    Измените файл `WebAppPlusCacheAppSecrets.config` и добавьте содержимое, приведенное ниже. При локальном запуске приложения эти сведения используются для подключения к экземпляру кэша Redis для Azure. Далее в этом руководстве будет подготовлен к работе экземпляр кэша Redis для Azure и обновлены имя и пароль для него. Если пример приложения не планируется запускать локально, создавать этот файл необязательно. В таком случае можно пропустить последующие шаги, в которых он указан, так как при развертывании в Azure приложение получает сведения о подключении кэша из параметра приложения для веб-приложения, а не из этого файла. Так как файл `WebAppPlusCacheAppSecrets.config` не развертывается в Azure с приложением, он не требуется при условии, что приложение не будет запускаться локально.


		<appSettings>
		  <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
		</appSettings>


2. В **обозревателе решений** дважды щелкните файл **web.config**, чтобы открыть его.

    ![Web.config][cache-web-config]

3. Добавьте следующий атрибут `file` в элемент `appSettings`. Если использовалось другое имя файла или расположение, замените эти значения на представленные в примере.
	-	До: `<appSettings>`
	-	После: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    Среда выполнения ASP.NET объединяет содержимое внешнего файла с разметкой в элементе `<appSettings>`. Если указанный файл не удается найти, среда выполнения игнорирует атрибут файла. Секреты (строка подключения к вашему кэшу) не включаются в исходный код приложения. При развертывании веб-приложения в Azure файл `WebAppPlusCacheAppSecrests.config` не будет развернут (именно это и нужно). Существует несколько способов указать эти секреты в Azure. В этом руководстве они настраиваются автоматически при [подготовке ресурсов Azure к работе](#provision-the-azure-resources) на следующем шаге. Дополнительные сведения о работе с секретами см. в статье [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) (Рекомендации по развертыванию паролей и других конфиденциальных данных в ASP.NET и в службе приложений Azure).


### Обновление класса TeamsController для возвращения результатов из кэша или базы данных

В этом примере статистику команды можно получить из базы данных или из кэша. Статистика команды сохраняется в кэше в качестве сериализованного элемента `List<Team>`, а также в качестве отсортированного набора с помощью типов данных Redis. Из отсортированного набора можно извлечь все или некоторые элементы или же запросить определенные элементы. В этом примере из отсортированного набора будут запрашиваться 5 лучших команд, упорядоченных по количеству положительных результатов.

>[AZURE.NOTE] Чтобы использовать кэш Redis для Azure, не обязательно хранить статистику команды в нескольких форматах в кэше. В этом руководстве несколько форматов используется для демонстрации различных способов кэширования данных и различных типов данных, используемых для этой операции.



1. В начале файла `TeamsController.cs` добавьте такие операторы к остальным операторам using:

		using System.Diagnostics;
		using Newtonsoft.Json;

2. Замените текущий метод `public ActionResult Index()` на следующую реализацию:


		// GET: Teams
		public ActionResult Index(string actionType, string resultType)
		{
		    List<Team> teams = null;
		
		    switch(actionType)
		    {
		        case "playGames": // Play a new season of games.
		            PlayGames();
		            break;
		
		        case "clearCache": // Clear the results from the cache.
		            ClearCachedTeams();
		            break;
		
		        case "rebuildDB": // Rebuild the database with sample data.
		            RebuildDB();
		            break;
		    }
		
		    // Measure the time it takes to retrieve the results.
		    Stopwatch sw = Stopwatch.StartNew();
		
		    switch(resultType)
		    {
		        case "teamsSortedSet": // Retrieve teams from sorted set.
		            teams = GetFromSortedSet();
		            break;
		
		        case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
		            teams = GetFromSortedSetTop5();
		            break;
		
		        case "teamsList": // Retrieve teams from the cached List<Team>.
		            teams = GetFromList();
		            break;
		
		        case "fromDB": // Retrieve results from the database.
		        default:
		            teams = GetFromDB();
		            break;
		    }
		
		    sw.Stop();
		    double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
		    ViewBag.msg += " MS: " + ms.ToString();
		
		    return View(teams);
		}


3. Добавьте следующие три метода из оператора switch, добавленного в предыдущем фрагменте кода, в класс `TeamsController` для реализации типов действий `playGames`, `clearCache` и `rebuildDB`.

    Метод `PlayGames` обновляет статистику команды, имитируя сезон игр, сохраняет результаты в базу данных и удаляет устаревшие данные из кэша.


	    void PlayGames()
	    {
	        ViewBag.msg += "Updating team statistics. ";
	        // Play a "season" of games.
	        var teams = from t in db.Teams
	                    select t;
	
	        Team.PlayGames(teams);
	
	        db.SaveChanges();
	
	        // Clear any cached results
	        ClearCachedTeams();
	    }


    Метод `RebuildDB` повторно инициализирует базу данных с набором команд по умолчанию, создает для них статистику и удаляет устаревшие данные из кэша.
	
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

	        // Clear any cached results
	        ClearCachedTeams();
        }


    Метод `ClearCachedTeams` удаляет любую кэшированную статистику команды из кэша.

	
	    void ClearCachedTeams()
	    {
	        IDatabase cache = Connection.GetDatabase();
	        cache.KeyDelete("teamsList");
	        cache.KeyDelete("teamsSortedSet");
	        ViewBag.msg += "Team data removed from cache. ";
	    } 


4. Добавьте следующие четыре метода в класс `TeamsController`, чтобы реализовать различные способы получения статистики команды из кэша и базы данных. Каждый из этих методов возвращает элемент `List<Team>`, который затем отображается в представлении.

    Метод `GetFromDB` считывает статистику команды из базы данных.

	    List<Team> GetFromDB()
	    {
	        ViewBag.msg += "Results read from DB. ";
	        var results = from t in db.Teams
	            orderby t.Wins descending
	            select t; 
	
	        return results.ToList<Team>();
	    }


    Метод `GetFromList` считывает статистику команды из кэша в качестве сериализованного элемента `List<Team>`. В случае промаха кэша статистика команды считывается из базы данных и сохраняется в кэше для последующего использования. В этом примере мы сериализуем объекты .NET из кэша и в кэш, используя сериализацию JSON.NET. Дополнительные сведения см. в разделе [Работа с объектами .NET в кэше](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    Метод `GetFromSortedSet` считывает статистику команды из кэшированного отсортированного набора. В случае промаха кэша статистика команды считывается из базы данных и сохраняется в кэше в качестве отсортированного набора.


	    List<Team> GetFromSortedSet()
	    {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
	        else
	        {
	            ViewBag.msg += "Teams sorted set cache miss. ";
	
	            // Read from DB
	            teams = GetFromDB();
	
	            ViewBag.msg += "Storing results to cache. ";
	            foreach (var t in teams)
	            {
	                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
	                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
	            }
	        }
	        return teams;
	    }


    Метод `GetFromSortedSetTop5` считывает 5 лучших команд из кэшированного отсортированного набора. Сначала проверяется наличие ключа `teamsSortedSet` в кэше. Если этот ключ не указан, вызывается метод `GetFromSortedSet` для считывания статистики команды и ее сохранения в кэше. Затем в кэшированном отсортированном наборе запрашиваются 5 лучших команд, которые возвращаются.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### Обновление методов создания, изменения и удаления для работы с кэшем

Код для формирования шаблонов, созданный в этом примере, содержит методы для добавления, изменения и удаления команд. При каждом добавлении, изменении или удалении команды данные в кэше устаревают. В этом разделе приведенные метода будут изменены, чтобы очистить кэшированные команды. Это поможет синхронизировать кэш с базой данных.

1. Перейдите к методу `Create(Team team)` в классе `TeamsController`. Добавьте вызов в метод `ClearCachedTeams`, как показано в следующем примере.


	    // POST: Teams/Create
	    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
	    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
	    [HttpPost]
	    [ValidateAntiForgeryToken]
	    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
	    {
	        if (ModelState.IsValid)
	        {
	            db.Teams.Add(team);
	            db.SaveChanges();
	            // When a team is added, the cache is out of date.
	            // Clear the cached teams.
	            ClearCachedTeams();
	            return RedirectToAction("Index");
	        }
	
	        return View(team);
	    }


2. Перейдите к методу `Edit(Team team)` в классе `TeamsController`. Добавьте вызов в метод `ClearCachedTeams`, как показано в следующем примере.


	    // POST: Teams/Edit/5
	    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
	    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
	    [HttpPost]
	    [ValidateAntiForgeryToken]
	    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
	    {
	        if (ModelState.IsValid)
	        {
	            db.Entry(team).State = EntityState.Modified;
	            db.SaveChanges();
	            // When a team is edited, the cache is out of date.
	            // Clear the cached teams.
	            ClearCachedTeams();
	            return RedirectToAction("Index");
	        }
	        return View(team);
		}


3. Перейдите к методу `DeleteConfirmed(int id)` в классе `TeamsController`. Добавьте вызов в метод `ClearCachedTeams`, как показано в следующем примере.


	    // POST: Teams/Delete/5
	    [HttpPost, ActionName("Delete")]
	    [ValidateAntiForgeryToken]
	    public ActionResult DeleteConfirmed(int id)
	    {
	        Team team = db.Teams.Find(id);
	        db.Teams.Remove(team);
	        db.SaveChanges();
	        // When a team is deleted, the cache is out of date.
	        // Clear the cached teams.
	        ClearCachedTeams();
	        return RedirectToAction("Index");
	    }


### Обновление представления индекса команд для работы с кэшем

1. В **обозревателе решений** разверните папку **Представления**, а затем — папку **Команды** и дважды щелкните файл **Layout.cshtml**.

    ![Index.cshtml][cache-views-teams-index-cshtml]

2. Найдите следующий элемент абзаца в начале файла.

    ![Таблица действий][cache-teams-index-table]

    Это ссылка, позволяющая создать команду. Замените элемент абзаца таблицей, приведенной ниже. Эта таблица содержит ссылки на действия для создания новой команды, воспроизведения нового сезона игр, очистки кэша, получения команды из кэша в нескольких форматах, получения команд из базы данных и повторной сборки базы данных с использованием нового примера данных.


		<table class="table">
		    <tr>
		        <td>
		            @Html.ActionLink("Create New", "Create")
		        </td>
		        <td>
		            @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
		        </td>
		        <td>
		            @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
		        </td>
		        <td>
		            @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
		        </td>
		        <td>
		            @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
		        </td>
		        <td>
		            @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
		        </td>
		        <td>
		            @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
		        </td>
		        <td>
		            @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
		        </td>
		    </tr>    
		</table>


3. Перейдите в конец файла **Index.cshtml** и добавьте элемент `tr` в последнюю строку последней таблицы.

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    В этой строке отображается значение файла `ViewBag.Msg`, содержащего отчет о состоянии текущей операции, которое устанавливается при выборе ссылки на действие из предыдущего шага.

    ![Сообщение о состоянии][cache-status-message]

4. Нажмите клавишу **F6**, чтобы скомпилировать проект.

## Подготовка ресурсов Azure к работе

Чтобы разместить приложение в Azure, сначала нужно подготовить к работе службы Azure, требуемые для приложения. В примере приложения в этом руководстве используются следующие службы Azure:

-	кэш Azure Redis
-	веб-приложение службы приложений;
-	База данных SQL

Чтобы развернуть эти службы в выбранной новой или существующей группе ресурсов, нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

[![Развертывание в Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Кнопка **Deploy to Azure** (Развернуть в Azure) использует шаблон [быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates) [Создание веб-приложения, кэша Redis и базы данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database), чтобы подготовить эти службы к работе, а также задать строку подключения для базы данных SQL и параметр приложения для строки подключения кэша Redis для Azure.

>[AZURE.NOTE] Если у вас нет учетной записи Azure, можно создать [бесплатную учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) всего за несколько минут.

Нажав кнопку **Deploy to Azure** (Развернуть в Azure), вы перейдете на портал Azure, где запустится создание ресурсов, описанных в шаблоне.

![Развертывание в Azure][cache-deploy-to-azure-step-1]

1. В колонке **Настраиваемое развертывание** выберите подписку Azure, которую следует использовать, и существующую группу ресурсов или создайте группу ресурсов и укажите ее расположение.
2. В колонке **Параметры** укажите имя учетной записи администратора (**ADMINISTRATORLOGIN**, не используйте **admin**), пароль для входа администратора (**ADMINISTRATORLOGINPASSWORD**) и имя базы данных (**DATABASENAME**). Другие параметры настраиваются для плана размещения службы приложений (цен. категория "Бесплатный") и более экономичных компонентов для базы данных SQL и кэша Redis для Azure, которые не предусмотрены на уровне "Бесплатный".
3. При необходимости измените любые другие параметры или оставьте значения по умолчанию и нажмите кнопку **ОК**.
4. После настройки параметров щелкните **Просмотреть юридические условия**.

![Развертывание в Azure][cache-deploy-to-azure-step-2]

1. Ознакомьтесь с условиями в колонке **Создание** и нажмите кнопку **Создать**.
2. Чтобы начать подготовку ресурсов к работе, нажмите кнопку **Создать** в колонке **Настраиваемое развертывание**.

Чтобы просмотреть ход выполнения развертывания, щелкните значок уведомления и сообщение **Развертывание начато**.

![Развертывание начато][cache-deployment-started]

Состояние развертывания можно просмотреть в колонке **Microsoft.Template**.

![Развертывание в Azure][cache-deploy-to-azure-step-3]

После завершения подготовки к работе можно опубликовать приложение Azure из Visual Studio.

>[AZURE.NOTE] Все ошибки, возникающие при подготовке к работе, отображаются в колонке **Microsoft.Template**. Распространенные ошибки — это превышение количества серверов SQL Server или планов размещения службы приложений (цен. категория "Бесплатный") на подписку. Устраните ошибки и перезапустите подготовку к работе, нажав кнопку **Повторить развертывание** в колонке **Microsoft.Template** или **Deploy to Azure** (Развернуть в Azure) в этом руководстве.

## Публикация приложения в Azure

На этом шаге руководства приложение будет опубликовано в Azure и запущено в облаке.

1. Щелкните правой кнопкой мыши проект **ContosoTeamStats** в Visual Studio и выберите пункт **Опубликовать**.

    ![Опубликовать][cache-publish-app]

2. Щелкните **Служба приложений Microsoft Azure**.

    ![Опубликовать][cache-publish-to-app-service]

3. Выберите подписку, использованную при создании ресурсов Azure, разверните группу ресурсов, содержащую ресурсы, выберите нужное веб-приложение и нажмите кнопку **ОК**. Если использовалась кнопка **Deploy to Azure** (Развернуть в Azure), имя веб-приложения будет начинаться с **webSite**, а также содержать некоторые дополнительные символы.

    ![Выбор веб-приложения][cache-select-web-app]

4. Нажмите кнопку **Проверить подключение**, чтобы проверить параметры, а затем — кнопку **Опубликовать**.

    ![Опубликовать][cache-publish]

    Через некоторое время публикация завершится, после чего будет запущен браузер с выполняющимся примером приложения. Если при проверке или публикации возникает ошибка DNS, а подготовка ресурсов Azure к работе для приложения недавно завершилась, подождите немного и повторите попытку.

    ![Кэш добавлен][cache-added-to-application]

В следующей таблице описана каждая ссылка на действие из примера приложения.

| Действие | Описание |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Создать | Создание команды. |
| Воспроизвести сезон | Воспроизведение сезона игр, обновление статистики команды и удаление всех устаревших данных из кэша команды. |
| Очистить кэш | Удаление статистики команды из кэша. |
| List from Cache (Перечислить из кэша) | Получение статистики команды из кэша. В случае промаха кэша статистика загружается из базы данных и сохраняется в кэше для последующего использования. |
| Sorted Set from Cache (Отсортированный набор из кэша) | Получение статистики команды из кэша с использованием отсортированного набора. В случае промаха кэша статистика загружается из базы данных и сохраняется в кэше с использованием отсортированного набора. |
| Top 5 Teams from Cache (5 лучших команд из кэша) | Получение 5 лучших команд из кэша с использованием отсортированного набора. В случае промаха кэша статистика загружается из базы данных и сохраняется в кэше с использованием отсортированного набора. |
| Load from DB (Загрузить из базы данных) | Получение статистики команды из базы данных. |
| Rebuild DB (Перестроить базу данных) | Повторная сборка базы данных и ее перезагрузка с использованием примера данных команды. |
| Изменить; Сведения; Удалить | Изменение команды, просмотр сведений о команде, удаление команды. |


Выполните некоторые действия и попробуйте получить данные из различных источников. Обратите внимание на разницу во времени при получении данных из базы данных и кэша разными способами.

## Удаление ресурсов после завершения работы с приложением

По окончании работы с примером приложения в руководстве можно удалить использованные ресурсы Azure, чтобы сократить затраты и сэкономить ресурсы. Если при работе с разделом [Подготовка ресурсов Azure к работе](#provision-the-azure-resources) использовалась кнопка **Deploy to Azure** (Развернуть в Azure) и все ресурсы находятся в одной группе ресурсов, их можно полностью удалить, удалив группу ресурсов.

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните **Группы ресурсов**.
2. Введите имя группы ресурсов в текстовом поле **Фильтровать элементы…**.
3. Щелкните **…** справа от группы ресурсов.
4. Нажмите кнопку **Delete** (Удалить).

    ![Удалить][cache-delete-resource-group]

5. Введите имя группы ресурсов и нажмите кнопку **Удалить**.

    ![Подтверждение удаления][cache-delete-confirm]

Через некоторое время группа ресурсов и все ее ресурсы будут удалены.

>[AZURE.IMPORTANT] Обратите внимание, что удаление группы ресурсов — необратимая операция, и все соответствующие ресурсы удаляются окончательно. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если ресурсы для размещения этого примера созданы в существующей группе ресурсов, можно удалить каждый ресурс отдельно в соответствующих колонках.

## Запуск примера приложения на локальном компьютере

Чтобы запустить приложение локально на компьютере, необходим экземпляр кэша Redis для Azure, в котором следует кэшировать данные.

-	Если приложение опубликовано в Azure, как описано в предыдущем разделе, можно использовать экземпляр кэша Redis для Azure, подготовленный к работе на этом шаге.
-	При наличии другого существующего экземпляра кэша Redis для Azure его можно использовать для локального запуска этого примера.
-	Если необходимо создать экземпляр кэша Redis для Azure, следует выполнить действия, описанные в разделе [Создание кэша](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

После выбора или создания кэша перейдите к нему на портале Azure и скопируйте соответствующие [имя узла](cache-configure.md#properties) и [ключи доступа](cache-configure.md#access-keys). Инструкции см. в разделе [Настройка параметров кэша Redis](cache-configure.md#configure-redis-cache-settings).

1. Откройте файл `WebAppPlusCacheAppSecrets.config`, созданный на шаге [Настройка приложения для использования кэша Redis](#configure-the-application-to-use-redis-cache) этого руководства при помощи выбранного редактора.

2. Измените атрибут `value`, замените `MyCache.redis.cache.windows.net` на [имя узла](cache-configure.md#properties) кэша и укажите [первичный или вторичный ключ](cache-configure.md#access-keys) кэша в качестве пароля.


		<appSettings>
		  <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
		</appSettings>


3. Для запуска приложения нажмите сочетание клавиш **Ctrl+F5**.

>[AZURE.NOTE] Обратите внимание, что так как приложение, включая базу данных, выполняется локально, а кэш Redis размещен в Azure, производительность кэша может быть ниже производительности базы данных. Чтобы повысить производительность, следует разместить клиентское приложение и экземпляр кэша Redis для Azure в одном расположении.

## Дальнейшие действия

-	Дополнительные сведения о [начале работы с ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) можно получить на сайте [ASP.NET](http://asp.net/).
-	Узнайте подробнее о [подходе Code First для создания базы данных](https://msdn.microsoft.com/data/jj193542) в Entity Framework, использованном в этом руководстве.
-	Узнайте больше о [веб-приложениях в службе приложений Azure](../app-service-web/app-service-web-overview.md).
-	Узнайте, как [выполнять мониторинг](cache-how-to-monitor.md) кэша на портале Azure.

-	Изучите возможности кэша Redis для Azure уровня Premium:
	-	[Настройка сохраняемости для кэша Redis для Azure уровня Премиум](cache-how-to-premium-persistence.md)
	-	[Настройка кластеризации для кэша Redis для Azure уровня Премиум](cache-how-to-premium-clustering.md)
	-	[Настройка поддержки виртуальной сети для кэша Redis для Azure уровня Премиум](cache-how-to-premium-vnet.md)
	-	Дополнительные сведения о размере и пропускной способности для кэшей уровня Premium см. в статье [Кэш Redis для Azure. Вопросы и ответы](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).


<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

<!---HONumber=AcomDC_0504_2016-->