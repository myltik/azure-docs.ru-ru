---
title: "Как создать веб-приложение с использованием кэша Redis | Документация Майкрософт"
description: "Узнайте, как создать веб-приложение с использованием кэша Redis"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 05/09/2017
ms.author: wesmc
ms.openlocfilehash: 98750c4f8d2449fb4fdf68b03a00d846e636a93a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Как создать веб-приложение с использованием кэша Redis
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

В этом руководстве описано, как создать и развернуть веб-приложение ASP.NET в веб-приложение службы приложений Azure с помощью Visual Studio 2017. В примере приложения отображается список статистических данных команды из базы данных и различные способы использования кэша Redis для Azure для хранения и извлечения данных из кэша. Завершив работу с руководством, вы получите рабочее веб-приложение, которое выполняет чтение и запись в базе данных, оптимизированное для работы с кэшем Redis для Azure и размещенное в Azure.

Вы узнаете:

* как создать веб-приложение ASP.NET MVC 5 в Visual Studio;
* как получить доступ к данным из базы данных с использованием Entity Framework;
* как улучшить пропускную способность данных и снизить нагрузку на базу данных за счет хранения и извлечения данных с помощью кэша Redis для Azure;
* как получить пять лучших команд с помощью отсортированного набора Redis;
* как подготовить ресурсы Azure к работе для приложения с помощью шаблона Resource Manager;
* как опубликовать приложение в Azure с помощью Visual Studio.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством необходимо следующее:

* [Учетная запись Azure](#azure-account)
* [Visual Studio 2017 с пакетом Azure SDK для .NET](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Учетная запись Azure
Для работы с этим руководством требуется учетная запись Azure. Вы можете:

* [Открыть бесплатную учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Вы получаете кредиты, которые можно использовать, чтобы попробовать платные службы Azure. После израсходования кредитов ваша учетная запись не исчезнет. Вы сможете использовать ее для работы с бесплатными службами и функциями Azure.
* [Активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Ваша подписка MSDN каждый месяц приносит вам кредиты, которые можно использовать для оплаты использования служб Azure.

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>Visual Studio 2017 с пакетом Azure SDK для .NET
Это руководство написано для использования с Visual Studio 2017 с пакетом [Azure SDK для .NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools). Пакет Azure SDK 2.9.5 входит в состав установщика Visual Studio.

Если у вас есть Visual Studio 2015, вы можете следовать инструкциям по использованию [пакета Azure SDK для .NET](../dotnet-sdk.md) 2.8.2 или более поздней версии. [Скачайте последний пакет Azure SDK для Visual Studio 2015 отсюда](http://go.microsoft.com/fwlink/?linkid=518003). Будет автоматически установлена программа Visual Studio с пакетом SDK (если она еще не установлена). Некоторые снимки экранов, приведенные в этом руководстве, могут отличаться от реальных.

Если на вашем компьютере установлена версия Visual Studio 2013, можно [скачать последнюю версию пакета Azure SDK для Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Некоторые снимки экранов, приведенные в этом руководстве, могут отличаться от реальных.

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio
1. Откройте Visual Studio и щелкните **Файл**, **Создать**, **Проект**.
2. Разверните узел **Visual C#** в списке **Шаблоны**, выберите **Облако** и щелкните **Веб-приложение ASP.NET**. Убедитесь, что выбрана платформа **.NET Framework 4.5.2** или ее более новая версия.  В текстовом поле **Имя** введите **ContosoTeamStats** и нажмите кнопку **ОК**.
   
    ![Создание проекта][cache-create-project]
3. Выберите тип проекта **MVC**. 

    Для параметра **Проверка подлинности** обязательно укажите значение **Без проверки подлинности**. В зависимости от установленной версии Visual Studio значение по умолчанию может быть другим. Чтобы изменить его, щелкните **Изменить проверку подлинности** и выберите **Без проверки подлинности**.

    Если вы работаете в Visual Studio 2015, снимите флажок **Разместить в облаке**. На следующих шагах руководства вы [подготовите ресурсы Azure к работе](#provision-the-azure-resources) и [опубликуете приложение в Azure](#publish-the-application-to-azure). Пример подготовки веб-приложения службы приложений в Visual Studio с установленным флажком **Разместить в облаке** см. в статье [Развертывание веб-приложения ASP.NET в службе приложений Azure с помощью Visual Studio](../app-service/app-service-web-get-started-dotnet.md).
   
    ![Выбор шаблона проекта][cache-select-template]
4. Нажмите кнопку **ОК** , чтобы создать проект.

## <a name="create-the-aspnet-mvc-application"></a>Создание приложения ASP.NET MVC
В этом разделе руководства описывается создание базового приложения, которое выполняет чтение статистики команды из базы данных и отображает ее.

* [Добавление пакета Entity Framework NuGet](#add-the-entity-framework-nuget-package)
* [Добавление модели](#add-the-model)
* [Добавление контроллера](#add-the-controller)
* [Настройка представлений](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>Добавление пакета Entity Framework NuGet

1. В Visual Studio щелкните **Инструменты > Диспетчер пакетов NuGet > Консоль диспетчера пакетов.**.
2. В окне **консоли диспетчера пакетов** запустите следующую команду:
    
    ```
    Install-Package EntityFramework
    ```

Дополнительные сведения об этом пакете см. в документации [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-model"></a>Добавление модели
1. В **обозревателе решений** щелкните правой кнопкой мыши папку **Модели**, а затем выберите **Добавить** и **Класс**. 
   
    ![Добавление модели][cache-model-add-class]
2. Введите `Team` в качестве имени класса и нажмите кнопку **Добавить**.
   
    ![Добавление класса модели][cache-model-add-class-dialog]
3. В начале файла `Team.cs` замените операторы `using` следующими операторами `using`:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. Замените определение класса `Team` приведенным ниже фрагментом кода с обновлением определения класса `Team`, а также некоторыми другими вспомогательными классами Entity Framework. Для дополнительных сведений о подходе Code First в Entity Framework, использованном в этом руководстве, см. видео в статье [Использование Code First для создания базы данных](https://msdn.microsoft.com/data/jj193542).

    ```csharp
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
    ```


1. В **обозревателе решений** дважды щелкните файл **web.config**, чтобы открыть его.
   
    ![Web.config][cache-web-config]
2. Добавьте раздел `connectionStrings` в раздел `configuration`. Имя строки подключения должно соответствовать имени класса контекста базы данных Entity Framework ( `TeamContext`).

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    В следующем примере показаны разделы `connectionStrings` и `configSections` в разделе `configuration`.

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

    > [!NOTE]
    > Ваша строка подключения может отличаться. Это зависит от версии Visual Studio и выпуска SQL Server Express, используемых для завершения работы с этим руководством. Шаблон web.config необходимо настроить в соответствии с установкой. Он может содержать записи `Data Source`, например `(LocalDB)\v11.0` (из SQL Server Express 2012) или `Data Source=(LocalDB)\MSSQLLocalDB` (из SQL Server Express 2014 и более поздней версии). Дополнительные сведения о строках подключения и версиях SQL Express см. в статье о [LocalDB SQL Server 2016 Express](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-2016-express-localdb).

### <a name="add-the-controller"></a>Добавление контроллера
1. Нажмите клавишу **F6** , чтобы скомпилировать проект. 
2. В **обозревателе решений** щелкните правой кнопкой мыши папку **Контроллеры**, а затем выберите **Добавить** и **Контроллер**.
   
    ![Добавление контролера][cache-add-controller]
3. Выберите **Контроллер MVC 5 с представлениями, использующий Entity Framework** и нажмите кнопку **Добавить**. Если после нажатия кнопки **Добавить**появилась ошибка, убедитесь, что вы сначала скомпилировали проект.
   
    ![Добавление класса контроллера][cache-add-controller-class]
4. В раскрывающемся списке **Класс модели** выберите **Team (ContosoTeamStats.Models)**. В раскрывающемся списке **Класс контекста данных** выберите **TeamContext (ContosoTeamStats.Models)**. Введите `TeamsController` в текстовое поле **Имя контроллера** (если оно не будет заполнено автоматически). Нажмите кнопку **Добавить** , чтобы создать класс контроллера и добавить представления по умолчанию.
   
    ![Настройка контроллера][cache-configure-controller]
5. В **обозревателе решений** разверните **Global.asax** и дважды щелкните файл **Global.asax.cs**, чтобы открыть его.
   
    ![Global.asax.cs][cache-global-asax]
6. В начале файла добавьте следующие два оператора `using` после остальных операторов `using`.

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. В конце метода `Application_Start` добавьте следующую строку кода:

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. В **обозревателе решений** разверните `App_Start` и дважды щелкните `RouteConfig.cs`.
   
    ![RouteConfig.cs.][cache-RouteConfig-cs]
2. В приведенном ниже коде в методе `RegisterRoutes` замените `controller = "Home"` на `controller = "Teams"`, как показано в следующем примере.

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>Настройка представлений
1. В **обозревателе решений** разверните папку **Представления**, а затем — папку **Общее** и дважды щелкните файл **_Layout.cshtml**. 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. Измените содержимое элемента `title` и замените `My ASP.NET Application` на `Contoso Team Stats`, как показано в следующем примере.

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. В разделе `body` обновите первый оператор `Html.ActionLink`, а также замените `Application name` на `Contoso Team Stats` и `Home` на `Teams`.
   
   * До: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * После: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![Изменения в коде][cache-layout-cshtml-code]
2. Нажмите клавиши **CTRL+F5** , чтобы создать и запустить приложение. В этой версии приложения результаты считываются непосредственно из базы данных. Примечание. Действия **Создать**, **Изменить**, **Сведения** и **Удалить** автоматически добавлены в приложение с помощью шаблона **Контроллер MVC 5 с представлениями, использующий Entity Framework**. В следующем разделе руководства будет добавлен кэш Redis для оптимизации доступа к данным и добавления дополнительных функций в приложение.

![Начальное приложение][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Настройка приложения для использования кэша Redis
В этом разделе руководства будет настроен пример приложения для хранения и извлечения статистики команды Contoso из экземпляра кэша Redis для Azure с помощью клиента кэша [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

* [Настройка приложения для использования StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Обновление класса TeamsController для возвращения результатов из кэша или базы данных](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [Обновление методов создания, изменения и удаления для работы с кэшем](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [Обновление представления индекса команд для работы с кэшем](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Настройка приложения для использования StackExchange.Redis
1. Чтобы настроить клиентское приложение в Visual Studio, используя пакет [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) из NuGet, выберите **Инструменты > Диспетчер пакетов NuGet > Консоль диспетчера пакетов**.
2. Выполните следующую команду в окне `Package Manager Console`:
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    Пакет NuGet загружает и добавляет необходимые ссылки на сборки в клиентском приложении для доступа к кэшу Azure Redis из клиента кэша StackExchange.Redis. Если вы предпочитаете использовать версию клиентской библиотеки `StackExchange.Redis` со строгими именами, установите пакет `StackExchange.Redis.StrongName`.
3. В **обозревателе решений** разверните папку **Контроллеры** и дважды щелкните файл **TeamsController.cs**, чтобы открыть его.
   
    ![Контроллер команд][cache-teamscontroller]
4. Добавьте в файл **TeamsController.cs** следующие два оператора `using`:

    ```csharp   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. Добавьте в класс `TeamsController` следующие два свойства:

    ```csharp   
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
    ```

6. Создайте на компьютере файл с именем `WebAppPlusCacheAppSecrets.config` и поместите его в расположение, которое не будет записано после изменения с исходным кодом примера приложения, если его нужно будет записать где-нибудь после изменения. В этом примере файл `AppSettingsSecrets.config` находится в папке `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.
   
    Измените файл `WebAppPlusCacheAppSecrets.config` и добавьте содержимое, приведенное ниже.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="YourCacheName.redis.cache.windows.net,abortConnect=false,ssl=true,password=YourAccessKey"/>
    </appSettings>
    ```

    При локальном запуске приложения эти сведения используются для подключения к экземпляру кэша Redis для Azure. Далее в этом руководстве будет подготовлен к работе экземпляр кэша Redis для Azure и обновлены имя и пароль для него. Если пример приложения не планируется запускать локально, создавать этот файл необязательно. В таком случае можно пропустить последующие шаги, в которых он указан, так как при развертывании в Azure приложение получает сведения о подключении кэша из параметра приложения для веб-приложения, а не из этого файла. Так как файл `WebAppPlusCacheAppSecrets.config` не развертывается в Azure с приложением, он не нужен, если приложение не будет запускаться локально.

1. В **обозревателе решений** дважды щелкните файл **web.config**, чтобы открыть его.
   
    ![Web.config][cache-web-config]
2. Добавьте атрибут `file` в элемент `appSettings`, как показано ниже. Если использовалось другое имя файла или расположение, замените эти значения на представленные в примере.
   
   * До: `<appSettings>`
   * После: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
  
   Среда выполнения ASP.NET объединяет содержимое внешнего файла с разметкой в элементе `<appSettings>`. Если указанный файл не удается найти, среда выполнения игнорирует атрибут файла. Секреты (строка подключения к вашему кэшу) не включаются в исходный код приложения. При развертывании веб-приложения в Azure файл `WebAppPlusCacheAppSecrests.config` не будет развернут (как и нужно). Существует несколько способов указать эти секреты в Azure. В этом руководстве они настраиваются автоматически при [подготовке ресурсов Azure к работе](#provision-the-azure-resources) на следующем шаге. Дополнительные сведения о работе с секретами в Azure см. в статье [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) (Рекомендации по развертыванию паролей и других конфиденциальных данных в ASP.NET и в службе приложений Azure).

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Обновление класса TeamsController для возвращения результатов из кэша или базы данных
В этом примере статистику команды можно получить из базы данных или из кэша. Статистика команды сохраняется в кэше в качестве сериализованного элемента `List<Team>`, а также в качестве отсортированного набора с помощью типов данных Redis. Из отсортированного набора можно извлечь все или некоторые элементы или же запросить определенные элементы. В этом примере из отсортированного набора будут запрашиваться 5 лучших команд, упорядоченных по количеству положительных результатов.

> [!NOTE]
> Чтобы использовать кэш Redis для Azure, не обязательно хранить статистику команды в нескольких форматах в кэше. В этом руководстве несколько форматов используется для демонстрации различных способов кэширования данных и различных типов данных, используемых для этой операции.
> 
> 

1. В начале файла `TeamsController.cs` добавьте следующие операторы `using` к остальным операторам `using`.

    ```csharp   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. Замените текущую реализацию метода `public ActionResult Index()` следующей реализацией.

    ```csharp
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
    ```


1. Добавьте приведенные ниже три метода из оператора switch, добавленного в предыдущем фрагменте кода, в класс `TeamsController` для реализации типов действий `playGames`, `clearCache` и `rebuildDB`.
   
    Метод `PlayGames` обновляет статистику команды, имитируя сезон игр, сохраняет результаты в базу данных и удаляет устаревшие данные из кэша.

    ```csharp
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
    ```

    Метод `RebuildDB` повторно инициализирует базу данных с набором команд по умолчанию, создает для них статистику и удаляет устаревшие данные из кэша.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    Метод `ClearCachedTeams` удаляет всю кэшированную статистику команды из кэша.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. Добавьте приведенные ниже четыре метода в класс `TeamsController` , чтобы реализовать различные способы получения статистики команды из кэша и базы данных. Каждый из этих методов возвращает элемент `List<Team>` , который затем отображается в представлении.
   
    Метод `GetFromDB` считывает статистику команды из базы данных.
   
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    Метод `GetFromList` считывает статистику команды из кэша в качестве сериализованного элемента `List<Team>`. В случае промаха кэша статистика команды считывается из базы данных и сохраняется в кэше для последующего использования. В этом примере мы сериализуем объекты .NET из кэша и в кэш, используя сериализацию JSON.NET. Дополнительные сведения см. в разделе [Работа с объектами .NET в кэше](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
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
    ```

    Метод `GetFromSortedSet` считывает статистику команды из кэшированного отсортированного набора. В случае промаха кэша статистика команды считывается из базы данных и сохраняется в кэше в качестве отсортированного набора.

    ```csharp
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
    ```

    Метод `GetFromSortedSetTop5` считывает 5 лучших команд из кэшированного отсортированного набора. Сначала проверяется наличие ключа `teamsSortedSet` в кэше. Если этот ключ не указан, вызывается метод `GetFromSortedSet` для считывания статистики команды и ее сохранения в кэше. Затем в кэшированном отсортированном наборе запрашивается 5 первых команд, которые возвращаются.

    ```csharp
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
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Обновление методов создания, изменения и удаления для работы с кэшем
Код для формирования шаблонов, созданный в этом примере, содержит методы для добавления, изменения и удаления команд. При каждом добавлении, изменении или удалении команды данные в кэше устаревают. В этом разделе приведенные метода будут изменены, чтобы очистить кэшированные команды. Это поможет синхронизировать кэш с базой данных.

1. Перейдите к методу `Create(Team team)` в классе `TeamsController`. Добавьте вызов в метод `ClearCachedTeams`, как показано в следующем примере.

    ```csharp
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
    ```


1. Перейдите к методу `Edit(Team team)` в классе `TeamsController`. Добавьте вызов в метод `ClearCachedTeams`, как показано в следующем примере.

    ```csharp
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
    ```


1. Перейдите к методу `DeleteConfirmed(int id)` в классе `TeamsController`. Добавьте вызов в метод `ClearCachedTeams`, как показано в следующем примере.

    ```csharp
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
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Обновление представления индекса команд для работы с кэшем
1. В **обозревателе решений** разверните папку **Представления**, а затем — папку **Команды** и дважды щелкните файл **Index.cshtml**.
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. Найдите следующий элемент абзаца в начале файла.
   
    ![Таблица действий][cache-teams-index-table]
   
    Это ссылка, позволяющая создать команду. Замените элемент абзаца таблицей, приведенной ниже. Эта таблица содержит ссылки на действия для создания новой команды, воспроизведения нового сезона игр, очистки кэша, получения команды из кэша в нескольких форматах, получения команд из базы данных и повторной сборки базы данных с использованием нового примера данных.

    ```html
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
    ```


1. Перейдите в конец файла **Index.cshtml** и добавьте элемент `tr` в последнюю строку последней таблицы.
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    В этой строке отображаются значение `ViewBag.Msg`, которое содержит отчет о состоянии о текущей операции. Значение `ViewBag.Msg` устанавливается при выборе ссылки на любое действия предыдущего шага.   
   
    ![Сообщение о состоянии][cache-status-message]
2. Нажмите клавишу **F6** , чтобы скомпилировать проект.

## <a name="provision-the-azure-resources"></a>Подготовка ресурсов Azure к работе
Чтобы разместить приложение в Azure, сначала нужно подготовить к работе службы Azure, требуемые для приложения. В примере приложения в этом руководстве используются следующие службы Azure:

* кэш Azure Redis
* веб-приложение службы приложений;
* База данных SQL

Чтобы развернуть эти службы в выбранной новой или имеющейся группе ресурсов, нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

[![Развертывание в Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Кнопка **Deploy to Azure** (Развернуть в Azure) использует шаблон [быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates) [Create a Web App plus Redis Cache plus SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) (Создание веб-приложения, кэша Redis и базы данных SQL), чтобы подготовить эти службы к работе, а также задать строку подключения для базы данных SQL и параметр приложения для строки подключения кэша Redis для Azure.

> [!NOTE]
> Если у вас нет учетной записи Azure, можно [создать бесплатную учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) всего за несколько минут.
> 
> 

Нажав кнопку **Deploy to Azure** (Развернуть в Azure), вы перейдете на портал Azure, где запустится создание ресурсов, описанных в шаблоне.

![Развернуть в Azure][cache-deploy-to-azure-step-1]

1. В разделе **основных сведений** выберите подписку Azure, которую следует использовать, и имеющуюся группу ресурсов или создайте группу ресурсов и укажите ее расположение.
2. В разделе **Параметры** укажите **имя учетной записи администратора** (не используйте **admin**), **пароль для входа администратора** и **имя базы данных**. Другие параметры настраиваются для плана размещения службы приложений (ценовая категория "Бесплатный") и более экономичных компонентов для базы данных SQL и кэша Redis для Azure, которые не предусмотрены на уровне "Бесплатный".

    ![Развернуть в Azure][cache-deploy-to-azure-step-2]

3. Настроив нужные параметры, прокрутите страницу до конца, ознакомьтесь с условиями и установите флажок **Я принимаю указанные выше условия**.
4. Чтобы начать подготовку ресурсов, нажмите кнопку **Приобрести**.

Чтобы просмотреть ход выполнения развертывания, щелкните значок уведомления и сообщение **Развертывание начато**.

![Развертывание начато][cache-deployment-started]

Состояние развертывания можно просмотреть в колонке **Microsoft.Template** .

![Развернуть в Azure][cache-deploy-to-azure-step-3]

После завершения подготовки к работе можно опубликовать приложение Azure из Visual Studio.

> [!NOTE]
> Все ошибки, возникающие при подготовке, отображаются в колонке **Microsoft.Template**. Среди распространенных ошибок — слишком много ошибок, связанных с SQL Server или планами размещения служб приложений уровня "Бесплатный" на подписку. Устраните ошибки и начните процедуру заново, нажав кнопку **Повторить развертывание** в колонке **Microsoft.Template** или кнопку **Deploy to Azure** (Развернуть в Azure), как описано в этом руководстве.
> 
> 

## <a name="publish-the-application-to-azure"></a>Публикация приложения в Azure
На этом шаге руководства приложение будет опубликовано в Azure и запущено в облаке.

1. В Visual Studio щелкните правой кнопкой мыши проект **ContosoTeamStats** и выберите пункт **Опубликовать**.
   
    ![Опубликовать][cache-publish-app]
2. Щелкните элемент **Служба приложений Microsoft Azure**, выберите параметр **Выбрать существующую** и нажмите кнопку **Опубликовать**.
   
    ![Опубликовать][cache-publish-to-app-service]
3. Выберите подписку, использованную при создании ресурсов Azure, разверните группу ресурсов, содержащую ресурсы и выберите нужное веб-приложение. Если использовалась кнопка **Deploy to Azure** (Развернуть в Azure), имя веб-приложения будет начинаться с **webSite** и содержать некоторые дополнительные символы.
   
    ![Выбор веб-приложения][cache-select-web-app]
4. Нажмите кнопку **ОК**, чтобы начать процесс публикации. Через некоторое время публикация завершится, после чего будет запущен браузер с выполняющимся примером приложения. Если при проверке или публикации возникает ошибка DNS, а подготовка ресурсов Azure к работе для приложения недавно завершилась, подождите немного и повторите попытку.
   
    ![Кэш добавлен][cache-added-to-application]

В следующей таблице описана каждая ссылка на действие из примера приложения.

| Действие | ОПИСАНИЕ |
| --- | --- |
| Создать |Создание команды. |
| Воспроизвести сезон |Воспроизведение сезона игр, обновление статистики команды и удаление всех устаревших данных из кэша команды. |
| Очистить кэш |Удаление статистики команды из кэша. |
| List from Cache (Перечислить из кэша) |Получение статистики команды из кэша. В случае промаха кэша статистика загружается из базы данных и сохраняется в кэше для последующего использования. |
| Sorted Set from Cache (Отсортированный набор из кэша) |Получение статистики команды из кэша с использованием отсортированного набора. В случае промаха кэша статистика загружается из базы данных и сохраняется в кэше с использованием отсортированного набора. |
| Top 5 Teams from Cache (5 лучших команд из кэша) |Получение 5 лучших команд из кэша с использованием отсортированного набора. В случае промаха кэша статистика загружается из базы данных и сохраняется в кэше с использованием отсортированного набора. |
| Load from DB (Загрузить из базы данных) |Получение статистики команды из базы данных. |
| Rebuild DB (Перестроить базу данных) |Повторная сборка базы данных и ее перезагрузка с использованием примера данных команды. |
| Изменить; Сведения; Удалить |Изменение команды, просмотр сведений о команде, удаление команды. |

Выполните некоторые действия и попробуйте получить данные из различных источников. Обратите внимание на разницу во времени при получении данных из базы данных и кэша разными способами.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Удаление ресурсов после завершения работы с приложением
По окончании работы с примером приложения в руководстве можно удалить использованные ресурсы Azure, чтобы сократить затраты и сэкономить ресурсы. Если при работе с разделом **Подготовка ресурсов Azure к работе** использовалась кнопка [Deploy to Azure](#provision-the-azure-resources) (Развернуть в Azure) и все ресурсы находятся в одной группе ресурсов, их можно полностью удалить, удалив группу ресурсов.

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните **Группы ресурсов**.
2. Введите имя группы ресурсов в текстовое поле **Фильтровать элементы…** .
3. Щелкните **…** справа от группы ресурсов.
4. Нажмите кнопку **Delete**(Удалить).
   
    ![Delete][cache-delete-resource-group]
5. Введите имя группы ресурсов и нажмите кнопку **Удалить**.
   
    ![Подтверждение удаления][cache-delete-confirm]

Через некоторое время группа ресурсов и все ее ресурсы будут удалены.

> [!IMPORTANT]
> Обратите внимание, что удаление группы ресурсов — необратимая операция, и все соответствующие ресурсы удаляются окончательно. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если ресурсы для размещения этого примера созданы в существующей группе ресурсов, можно удалить каждый ресурс отдельно в соответствующих колонках.
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>Запуск примера приложения на локальном компьютере
Чтобы запустить приложение локально на компьютере, необходим экземпляр кэша Redis для Azure, в котором следует кэшировать данные. 

* Если приложение опубликовано в Azure, как описано в предыдущем разделе, можно использовать экземпляр кэша Redis для Azure, подготовленный к работе на этом шаге.
* При наличии другого существующего экземпляра кэша Redis для Azure его можно использовать для локального запуска этого примера.
* Если необходимо создать экземпляр кэша Redis для Azure, следует выполнить действия, описанные в разделе [Создание кэша](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

После выбора или создания кэша перейдите к нему на портале Azure и извлеките соответствующие [имя узла](cache-configure.md#properties) и [ключи доступа](cache-configure.md#access-keys). Инструкции см. в разделе [Настройка параметров кэша Redis](cache-configure.md#configure-redis-cache-settings).

1. Откройте файл `WebAppPlusCacheAppSecrets.config` , созданный на шаге [Настройка приложения для использования кэша Redis](#configure-the-application-to-use-redis-cache) этого руководства при помощи выбранного редактора.
2. Измените атрибут `value`, замените `YourCacheName.redis.cache.windows.net` [именем узла](cache-configure.md#properties) кэша, а `YourAccessKey` — [первичным или вторичным ключом](cache-configure.md#access-keys) кэша в качестве пароля.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="YourCacheName.redis.cache.windows.net,abortConnect=false,ssl=true,password=YourAccessKey"/>
    </appSettings>
    ```


1. Для запуска приложения нажмите сочетание клавиш **Ctrl+F5** .

> [!NOTE]
> Обратите внимание: так как приложение вместе с базой данных выполняется локально, а кэш Redis размещен в Azure, производительность кэша может быть ниже производительности базы данных. Чтобы повысить производительность, следует разместить клиентское приложение и экземпляр кэша Redis для Azure в одном расположении. 
> 
> 

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о [начале работы с ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) можно получить на сайте [ASP.NET](http://asp.net/).
* Дополнительные примеры создания веб-приложения ASP.NET в службе приложений см. в статье [Create and deploy an ASP.NET web app in Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) (Создание и развертывание веб-приложения ASP.NET в службе приложений Azure), описывающей [демоверсию](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect.
  * Дополнительные инструкции по быстрому началу работы с помощью средств разработчика Azure из демонстрационного проекта HealthClinic.biz см. [здесь](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
* Узнайте больше о подходе [Code First для создания базы данных](https://msdn.microsoft.com/data/jj193542) в Entity Framework, использованном в этом руководстве.
* Узнайте больше о [веб-приложениях в службе приложений Azure](../app-service/app-service-web-overview.md).
* Узнайте, как [выполнять мониторинг](cache-how-to-monitor.md) кэша на портале Azure.
* Изучите возможности кэша Redis для Azure уровня Premium:
  
  * [Настройка сохраняемости для кэша Redis для Azure уровня Премиум](cache-how-to-premium-persistence.md)
  * [Настройка кластеризации для кэша Redis для Azure уровня Премиум](cache-how-to-premium-clustering.md)
  * [Настройка поддержки виртуальной сети для кэша Redis для Azure уровня Премиум](cache-how-to-premium-vnet.md)
  * Дополнительные сведения о размере, пропускной способности и полосе пропускания для кэшей уровня "Премиум" см. в статье [Кэш Redis для Azure. Вопросы и ответы](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

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

