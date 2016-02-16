<properties
	pageTitle="Начало работы с первым приложением службы поиска Azure в .NET | Microsoft Azure | Размещенная облачная служба поиска"
	description="Учебник по созданию решения Visual Studio с помощью клиентской библиотеки .NET из пакета SDK для Поиска Azure в .NET."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Как впервые создать приложение, использующее Поиск Azure в .NET

Узнайте, как в Visual Studio 2013 или более поздней версии создать собственное приложение поиска для .NET, использующее Поиск Azure. В этом руководстве используется [пакет SDK службы поиска Azure для .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx), а также REST API службы поиска Azure.

Чтобы запустить этот пример, вам потребуется служба поиска Azure, в которой можно зарегистрироваться на [портале Azure](https://portal.azure.com). Пошаговые инструкции приведены в статье [Создание службы поиска Azure на классическом портале Azure](search-create-service-portal.md).

## О данных

В этом примере приложения используются данные [Геологической службы США (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), отфильтрованные по штату Род-Айленд, чтобы сократить размер набора данных. Мы будем использовать эти данные при создании приложения поиска, отображающего в результатах знаковые здания, такие как больницы и школы, а также геологические объекты, например реки, озера и возвышенности.

В этом приложении программа **DataIndexer** создает и загружает индекс с помощью [индексатора](https://msdn.microsoft.com/library/azure/dn798918.aspx), получая отфильтрованный набор данных USGS из общедоступной базы данных Azure SQL. Учетные данные и сведения о подключении к сетевому источнику данных предоставлены в исходном коде. Дальнейшая настройка не требуется.

> [AZURE.NOTE] Мы применили фильтр к этому набору данных, чтобы не превысить ограничение ценовой категории для бесплатного использования, составляющее 10 000 документов. Если используется другая ценовая категория, это ограничение не применяется. Дополнительные сведения об ограничениях каждой ценовой категории см. в разделе [Ограничения](search-limits-quotas-capacity.md).

<a id="sub-2"></a>
## Поиск имени службы и ключа API для службы поиска Azure ##

Создав службу, вернитесь на портал, чтобы получить URL-адрес или ключ `api-key`. Для подключения к службе поиска вам потребуется как URL-адрес, так и `api-key`, чтобы проверить подлинность при вызове.

1. Войдите на [портал Azure](https://portal.azure.com).
2. На панели переходов щелкните **Служба поиска**, чтобы получить список всех служб поиска Azure, подготовленных для вашей подписки.
3. Выберите службу, которую вы хотите использовать.
4. На панели мониторинга службы вы увидите плитки, указывающие на важную информацию, а также значок ключа для доступа к ключам администратора.

   ![][3]

3. Скопируйте URL-адрес службы и ключ администратора. Они потребуются вам позже, при добавлении их в файлы app.config и web.config в проектах Visual Studio.

## Создание нового проекта и решения

Это решение содержит два проекта:

- **DataIndexer** — консольное приложение Visual C# для загрузки данных.
- **SimpleSearchMVCApp** — веб-приложение MVC на Visual C# в ASP.NET для отправки запросов и возврата результатов поиска.

На этом этапе вы создадите оба проекта.

1. Последовательно выберите элементы **Visual Studio** > **Создать проект** > **Visual C#** > **Консольное приложение**.
2. Назовите проект **DataIndexer**, а решение — **AzureSearchDotNetDemo**.
3. В обозревателе решений выберите решение и щелкните правой кнопкой мыши элемент **Добавить**, а затем выберите элементы **Новый проект** > **Visual C#** > **Веб-приложение ASP.NET**.
4. Назовите проект **SimpleSearchMVCApp**.
5. В окне "Новый проект ASP.NET" выберите шаблон MVC и сбросьте настройки, чтобы избежать создания программных артефактов, которые не пригодятся вам при использовании этого учебника.

   Снимите флажки "Размещение Azure" и "Модульные тесты", а затем выберите для параметра "Проверка подлинности" значение "Отсутствует".

   ![][10]

После создания проектов ваше решение должно выглядеть аналогично приведенному ниже примеру.

   ![][4]

## Установка клиентской библиотеки .NET и обновление других пакетов

1. В обозревателе решений выберите решение и щелкните правой кнопкой мыши элемент **Управление пакетами NuGet**.
2. Последовательно выберите элементы **Обновления** > **Только стабильные** > **Обновить все**.

   ![][11]

3. Согласитесь на дополнительную установку пакетов, чтобы установить также все зависимые компоненты.

4. Затем установите клиентскую библиотеку для Поиска Azure в .NET. Обязательно укажите условие поиска правильно, иначе найти пакет будет сложно. Снова щелкните правой кнопкой мыши элемент **Управление пакетами NuGet**.

5. Выберите элементы **В сети** > **nuget.org** > **Только стабильные**, затем выполните поиск по запросу *azure.search*. Щелкните **Установить**, чтобы установить библиотеку.

   ![][12]

## Добавление ссылки на сборку System.Configuration

**DataIndexer** использует **System.Configuration** для считывания параметров конфигурации из файла app.config.

1. Щелкните правой кнопкой мыши **DataIndexer** > **Добавить** > **Ссылка** > **Платформа** > **System.Configuration**. Установите флажок.
2. Нажмите кнопку **ОК**.

## Обновление файлов конфигурации

Каждый проект содержит файлы конфигурации, в которых указано имя службы и ключ API.

1. В **DataIndexer** замените содержимое файла App.config приведенным ниже примером, указав вместо элементов [SERVICE NAME] и [SERVICE KEY] значения, действительные для вашей службы. Обратите внимание, что имя службы — это не полный URL-адрес. Например, если конечная точка службы поиска — **https://mysearchsrv.search.microsoft.net*, в файле App.config следует ввести имя службы *mysearchsrv*.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		    <appSettings>
		      <add key="SearchServiceName" value="[SEARCH SERVICE]" />
		      <add key="SearchServiceApiKey" value="[SEARCH SERVICE API KEY]" />
		    </appSettings>
		</configuration>

2. В **SimpleSearchMVCApp** замените файл Web.config приведенным ниже примером, снова указав вместо элементов [ИМЯ СЛУЖБЫ] и [КЛЮЧ СЛУЖБЫ] значения, действительные для вашей службы.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
			For more information on how to configure your ASP.NET application, please visit
			http://go.microsoft.com/fwlink/?LinkId=152368
			-->
		<configuration>
			<configSections>
				<!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
				<section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
			</configSections>
			<connectionStrings>
				<add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />
			</connectionStrings>
			<appSettings>
				<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
				<add key="SearchServiceApiKey" value="[API KEY]" />

				<add key="webpages:Version" value="2.0.0.0" />
				<add key="webpages:Enabled" value="false" />
				<add key="PreserveLoginUrl" value="true" />
				<add key="ClientValidationEnabled" value="true" />
				<add key="UnobtrusiveJavaScriptEnabled" value="true" />
			</appSettings>
			<system.web>
				<httpRuntime targetFramework="4.5" />
				<compilation debug="true" targetFramework="4.5" />
				<authentication mode="Forms">
					<forms loginUrl="~/Account/Login" timeout="2880" />
				</authentication>
				<pages>
					<namespaces>
						<add namespace="System.Web.Helpers" />
						<add namespace="System.Web.Mvc" />
						<add namespace="System.Web.Mvc.Ajax" />
						<add namespace="System.Web.Mvc.Html" />
						<add namespace="System.Web.Optimization" />
						<add namespace="System.Web.Routing" />
						<add namespace="System.Web.WebPages" />
					</namespaces>
				</pages>
				<profile defaultProvider="DefaultProfileProvider">
					<providers>
						<add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</profile>
				<membership defaultProvider="DefaultMembershipProvider">
					<providers>
						<add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />
					</providers>
				</membership>
				<roleManager defaultProvider="DefaultRoleProvider">
					<providers>
						<add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</roleManager>
				<!--
								If you are deploying to a cloud environment that has multiple web server instances,
								you should change session state mode from "InProc" to "Custom". In addition,
								change the connection string named "DefaultConnection" to connect to an instance
								of SQL Server (including SQL Azure and SQL  Compact) instead of to SQL Server Express.
					-->
				<sessionState mode="InProc" customProvider="DefaultSessionProvider">
					<providers>
						<add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
					</providers>
				</sessionState>
			</system.web>
			<system.webServer>
				<validation validateIntegratedModeConfiguration="false" />
				<handlers>
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />
					<remove name="ExtensionlessUrlHandler-Integrated-4.0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
					<remove name="OPTIONSVerbHandler" />
					<remove name="TRACEVerbHandler" />
				</handlers>
			</system.webServer>
			<entityFramework>
				<defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
					<parameters>
						<parameter value="v12.0" />
					</parameters>
				</defaultConnectionFactory>
			</entityFramework>
			<runtime>
				<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
					<dependentAssembly>
						<assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-7.0.0.0" newVersion="7.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
				</assemblyBinding>
			</runtime>
		</configuration>


## Изменение DataIndexer

Эта программа — консольное приложение, которое подключается к службе поиска, указанной в файле app.config, создает индекс, а затем загружает в него набор данных USGS из базы данных SQL Azure. В этой части руководства мы будем использовать индексатор.

Прежде чем запускать программу, замените файл **Program.cs**, используемый для создания индекса, индексатора, загрузки данных и составления сообщений.

### Обновление Program.cs

1. В обозревателе решений откройте **DataIndexer** > **Program.cs**
2. Замените содержимое файла Program.cs на код, приведенный ниже.

	    using System;
	    using System.Configuration;
	    using System.Threading;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace DataIndexer
	    {
	        class Program
	        {
	            private const string GeoNamesIndex = "geonames";
	            private const string UsgsDataSource = "usgs-datasource";
	            private const string UsgsIndexer = "usgs-indexer";
	
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            // This Sample shows how to delete, create, upload documents and query an index
	            static void Main(string[] args)
	            {
	                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                // Create an HTTP reference to the catalog index
	                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                _indexClient = _searchClient.Indexes.GetClient(GeoNamesIndex);
	
	                Console.WriteLine("{0}", "Deleting index, data source, and indexer...\n");
	                if (DeleteIndexingResources())
	                {
	                    Console.WriteLine("{0}", "Creating index...\n");
	                    CreateIndex();
	                    Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
	                    SyncDataFromAzureSQL();
	                }
	                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
	                Console.ReadKey();
	            }
	
	            private static bool DeleteIndexingResources()
	            {
	                // Delete the index, data source, and indexer.
	                try
	                {
	                    _searchClient.Indexes.Delete(GeoNamesIndex);
	                    _searchClient.DataSources.Delete(UsgsDataSource);
	                    _searchClient.Indexers.Delete(UsgsIndexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error deleting indexing resources: {0}\r\n", ex.Message);
	                    Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
	                    return false;
	                }
	
	                return true;
	            }
	
	            private static void CreateIndex()
	            {
	                // Create the Azure Search index based on the included schema
	                try
	                {
	                    var definition = new Index()
	                    {
	                        Name = GeoNamesIndex,
	                        Fields = new[] 
	                        { 
	                            new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
	                        }
	                    };
	
	                    _searchClient.Indexes.Create(definition);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating index: {0}\r\n", ex.Message);
	                }
	
	            }
	
	            private static void SyncDataFromAzureSQL()
	            {
	                // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
	                Console.WriteLine("{0}", "Creating Data Source...\n");
	                var dataSource =
	                    new DataSource()
	                    {
	                        Name = UsgsDataSource,
	                        Description = "USGS Dataset",
	                        Type = DataSourceType.AzureSql,
	                        Credentials = new DataSourceCredentials("Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;"),
	                        Container = new DataContainer("GeoNamesRI")
	                    };
	
	                try
	                {
	                    _searchClient.DataSources.Create(dataSource);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating data source: {0}", ex.Message);
	                    return;
	                }
	
	                Console.WriteLine("{0}", "Creating Indexer and syncing data...\n");
	
	                var indexer =
	                    new Indexer()
	                    {
	                        Name = UsgsIndexer,
	                        Description = "USGS data indexer",
	                        DataSourceName = dataSource.Name,
	                        TargetIndexName = GeoNamesIndex
	                    };
	
	                try
	                {
	                    _searchClient.Indexers.Create(indexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating and running indexer: {0}", ex.Message);
	                    return;
	                }
	
	                bool running = true;
	                Console.WriteLine("{0}", "Synchronization running...\n");
	                while (running)
	                {
	                    IndexerExecutionInfo status = null;
	
	                    try
	                    {
	                        status = _searchClient.Indexers.GetStatus(indexer.Name);
	                    }
	                    catch (Exception ex)
	                    {
	                        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
	                        return;
	                    }
	
	                    IndexerExecutionResult lastResult = status.LastResult;
	                    if (lastResult != null)
	                    {
	                        switch (lastResult.Status)
	                        {
	                            case IndexerExecutionStatus.InProgress:
	                                Console.WriteLine("{0}", "Synchronization running...\n");
	                                Thread.Sleep(1000);
	                                break;
	
	                            case IndexerExecutionStatus.Success:
	                                running = false;
	                                Console.WriteLine("Synchronized {0} rows...\n", lastResult.ItemCount);
	                                break;
	
	                            default:
	                                running = false;
	                                Console.WriteLine("Synchronization failed: {0}\n", lastResult.ErrorMessage);
	                                break;
	                        }
	                    }
	                }
	            }
	        }
	    }


## Создание и запуск DataIndexer

1. Щелкните правой кнопкой мыши проект **DataIndexer**.
2. Создайте проект.
3. Нажмите клавишу **F5**, чтобы запустить его.

Появится окно консоли со следующими сообщениями.

![][6]

На портале вы увидите новый индекс **geonames**. Обновление на портале может занять несколько минут, поэтому перед проверкой результатов вам, возможно, придется немного подождать.

![][7]

## Изменение SimpleSearchMVCApp

**SimpleSearchMVC** — это веб-приложение, выполняемое локально в IIS Express. Оно предусматривает поле поиска и представляет результаты поиска в таблице.

Перед запуском этой программы вам потребуется внести три изменения.

- Замените файл **HomeController.cs**, который нужен для принятия данных, вводимых пользователем. В этом примере условие поиска хранится в переменной *q*.

- Замените **index.cshtml** — веб-страницу для ввода условий поиска и отображения результатов поиска.

- Добавьте **FeatureSearch.cs** — класс, создающий клиент поиска и выполняющий поиск.

### Обновление HomeController.cs

Замените код по умолчанию приведенным ниже.

	    using Microsoft.Azure.Search.Models;
	    using System;
	    using System.Collections.Generic;
	    using System.Linq;
	    using System.Web;
	    using System.Web.Mvc;
	
	    namespace SimpleSearchMVCApp.Controllers
	    {
	        public class HomeController : Controller
	        {
	            //
	            // GET: /Home/
	            private FeaturesSearch _featuresSearch = new FeaturesSearch();
	
	            public ActionResult Index()
	            {
	                return View();
	            }
	
	            public ActionResult Search(string q = "")
	            {
	                // If blank search, assume they want to search everything
	                if (string.IsNullOrWhiteSpace(q))
	                    q = "*";
	
	                return new JsonResult
	                {
	                    JsonRequestBehavior = JsonRequestBehavior.AllowGet,
	                    Data = _featuresSearch.Search(q).Results
	                };
	            }
	        }
	    }


### Обновление Index.cshtml

Замените код по умолчанию приведенным ниже.

	@{
	    ViewBag.Title = "Azure Search - Feature Search";
	}

	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
	<script type="text/javascript">

	    $(function () {
	        // Execute search if user clicks enter
	        $("#q").keyup(function (event) {
	            if (event.keyCode == 13) {
	                Search();
	            }
	        });
	    });

	    function Search() {
	        // We will post to the MVC controller and parse the full results on the client side
	        // You may wish to do additional pre-processing on the data before sending it back to the client
	        var q = $("#q").val();

	        $.post('/home/search',
	        {
	            q: q
	        },
	        function (data) {
	            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
	            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
	            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
	            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
	            searchResultsHTML += "<td>DATE EDITED</td></tr>";
	            for (var i = 0; i < data.length; i++) {
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
	            }

	            $("#searchResults").html(searchResultsHTML);

	        });

	        function parseJsonDate(jsonDateString) {
	            if (jsonDateString != null)
	                return new Date(parseInt(jsonDateString.replace('/Date(', '')));
	            else
	                return "";
	        }
	    };

	</script>
	<h2>USGS Search for Rhode Island</h2>

	<div class="container">
	    <input type="search" name="q" id="q" autocomplete="off" size="100" /> <button onclick="Search();">Search</button>
	</div>
	<br />
	<div class="container">
	    <div class="row">
	        <table id="searchResults" border="1"></table>
	    </div>
	</div>


### Добавление FeaturesSearch.cs

Добавьте класс, обеспечивающий возможность поиска для вашего приложения.

1. В обозревателе решений щелкните правой кнопкой мыши элемент **SimpleSearchMVCApp**, а затем выберите **Добавить** > **Новый элемент** > **Код** > **Класс**.
2. Назовите класс **FeaturesSearch**.
3. Замените код по умолчанию приведенным ниже.


	    using System;
	    using System.Configuration;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace SimpleSearchMVCApp
	    {
	        public class FeaturesSearch
	        {
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            public static string errorMessage;
	
	            static FeaturesSearch()
	            {
	                try
	                {
	                    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                    // Create an HTTP reference to the catalog index
	                    _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                    _indexClient = _searchClient.Indexes.GetClient("geonames");
	                }
	                catch (Exception e)
	                {
	                    errorMessage = e.Message.ToString();
	                }
	            }
	
	            public DocumentSearchResult Search(string searchText)
	            {
	                // Execute search based on query string
	                try
	                {
	                    SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
	                    return _indexClient.Documents.Search(searchText, sp);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
	                }
	                return null;
	            }
	
	        }
	    }


### Назначение SimpleSearchMVCApp запускаемым проектом

Щелкните правой кнопкой мыши проект **SimpleSearchMVCApp**, чтобы назначить его запускаемым проектом.

## Сборка и запуск SimpleSearchMVCApp

При сборке и запуске этой программы в своем браузере по умолчанию вы увидите веб-страницу с полем поиска для доступа к данным USGS, хранящимся в индексе в службе поиска Azure.

![][8]

### Поиск по данным USGS

Набор данных USGS включает записи, относящиеся к штату Род-Айленд. Если нажать кнопку **Поиск** в пустом поле поиска, вы получите 50 первых записей. Это настройка по умолчанию.

Если ввести условие поиска, у поисковой системы появятся критерии для сужения области поиска. Попробуйте ввести региональное имя или название. *Роджер Уильямс* был первым губернатором Род-Айленда. В его честь названы многие парки, здания и школы.

![][9]

Вы также можете использовать эти запросы, добавляя или удаляя фразы и операторы для изменения области поиска:

- Пемброк ИЛИ Потакет;
- гусь +мыс -шея.


## Дальнейшие действия

Это первый учебник по Поиску Azure в .NET с использованием набора данных USGS. Мы будем постепенно расширять это руководство, чтобы рассказать о дополнительных функциях поиска, которые вы можете использовать в собственных решениях.

Если у вас уже есть опыт работы с Поиском Azure, вы можете использовать этот пример как фундамент для средств подбора (запросы опережающего ввода или автозаполнения), фильтров и фасетной навигации. Вы также можете улучшить страницу результатов поиска, добавив счетчики и пакетную обработку документов, чтобы пользователи могли просматривать результаты постранично.

Новичок в Поиске Azure? Мы рекомендуем ознакомиться с другими учебниками, чтобы получить представление о том, что вы можете создать. Посетите нашу [страницу документации](https://azure.microsoft.com/documentation/services/search/), чтобы найти дополнительные ресурсы. Вы также можете перейти по ссылкам в нашем [списке видео и учебников](search-video-demo-tutorial-list.md), чтобы получить дополнительные сведения.

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG

<!---HONumber=AcomDC_0211_2016-->