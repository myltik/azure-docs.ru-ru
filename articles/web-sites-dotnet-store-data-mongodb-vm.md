<properties urlDisplayName="Website with MongoDB VM" pageTitle="Веб-сайт .NET с MongoDB на виртуальной машине - Azure" metaKeywords="Azure Git ASP.NET MongoDB, Git .NET, Git MongoDB, ASP.NET MongoDB, Azure MongoDB, Azure ASP.NET, Azure tutorial" description="В этом учебнике показано, как использовать Git для развертывания приложения ASP.NET на веб-сайте Azure, подключенном к MongoDB на виртуальной машине." metaCanonical="" services="web-sites,virtual-machines" documentationCenter=".NET" title="Create an Azure website that connects to MongoDB running on a virtual machine in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />


# Создание веб-сайта Azure, который подключается к базе данных MongoDB, выполняющейся на виртуальной машине в Azure

С помощью Git можно развернуть приложение ASP.NET на веб-сайте Azure. В этом учебнике будет создано простое приложение списка задач MVC ASP.NET, которое подключается к базе данных MongoDB, запущенной на виртуальной машине в Azure.  [MongoDB][MongoDB] является популярной высокопроизводительной базой данных NoSQL с открытым кодом. После запуска и тестирования приложения ASP.NET на компьютере разработчика вы отправите приложение на веб-сайт Azure с помощью Git.

[WACOM.INCLUDE [create-account-and-websites-and-vms-note](../includes/create-account-and-websites-and-vms-note.md)]



##Обзор

Изучив данный учебник, вы научитесь:

- [Создание виртуальной машины и установка MongoDB](#virtualmachine)
- [Создание и запуск приложения ASP.NET "My Task List" на компьютере разработчика](#createapp)
- [Создание веб-сайта Azure](#createwebsite)
- [Развертывание приложения ASP.NET на веб-сайте с помощью Git](#deployapp)


##Фундаментальные знания

При изучении данного учебника знание следующих тем может оказаться полезным, хотя и не является обязательным требованием:

* Драйвер C# для MongoDB. Дополнительную информацию о разработке приложений C# для MongoDB см. в [центре языка CSharp][MongoC#LangCenter]. 
* Платформа веб-приложений ASP .NET. Всестороннее описание данного вопроса приведено на [веб-сайте ASP.net][ASP.NET].
* Платформа веб-приложений ASP .NET MVC. Всестороннее описание данного вопроса приведено на [веб-сайте ASP.NET MVC][MVCWebSite].
* Azure. Для начала вы можете ознакомиться с разделом [Azure][WindowsAzure].


##Подготовка

В этом разделе будет рассказано, как создать виртуальную машину в Azure и установить MongoDB, а также настроить среду разработки.

<a id="virtualmachine"></a> 
###Создание виртуальной машины и установка MongoDB

В этом учебном курсе предполагается, что вы создали виртуальную машину в Azure. После создания виртуальной машины необходимо установить на ней MongoDB.

* Создание виртуальной машины Windows и установка MongoDB описаны в разделе [Установка MongoDB на виртуальной машине под управлением Windows Server в Azure][InstallMongoOnWindowsVM].
* Кроме того, при создании виртуальной машины Linux и установке MongoDB вы можете обратиться к разделу [Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure][InstallMongoOnCentOSLinuxVM].

После создания виртуальной машины в Azure и установки MongoDB обязательно запомните DNS-имя виртуальной машины (например, "testlinuxvm.cloudapp.net") и внешний порт для MongoDB, указанный в конечной точке.  Эти сведения потребуются позже в данном учебном курсе.

### Установка Visual Studio

Начните с установки и запуска [Visual Studio Express 2013 для Web] [VSEWeb] или [Visual Studio 2013] [VSUlt].

Visual Studio - это интегрированная среда разработки. Как Microsoft Word используется для составления документов, так и интегрированная среда разработки используется для создания приложений. В этом учебнике используется Microsoft Visual Studio 2013, однако можно использовать Visual Studio Express 2013 - бесплатную версию Microsoft Visual Studio.

<a id="createapp"></a>
##Создание и запуск приложения ASP.NET "Мой список задач" на компьютере разработчика

В данном разделе с помощью Visual Studio будет создано приложение ASP.NET, имеющее имя "Мой список задач".  Приложение будет выполняться локально, но будет подключаться к виртуальной машине в Azure и использовать созданный там экземпляр MongoDB.

###Создание приложения
В Visual Studio щелкните **Создать проект**.

![Start Page New Project][StartPageNewProject]

В левой области окна **Новый проект** выберите **Visual C#**, а затем выберите **Интернет**. В средней области выберите **Веб-приложение ASP.NET**. В нижней части введите имя MyTaskListApp для проекта и нажмите кнопку **ОК**.

![New Project Dialog][NewProjectMyTaskListApp]

В диалоговом окне **Новый проект ASP.NET** выберите **MVC** и нажмите кнопку **ОК**.

![Select MVC Template][VS2013SelectMVCTemplate]

После завершения проекта отображается страница по умолчанию, созданная с помощью шаблона.

![Default ASP.NET MVC Application][VS2013DefaultMVCApplication]

###Установка драйвера C# для MongoDB

MongoDB обеспечивает поддержку приложений C# на стороне клиента посредством драйвера, который необходимо установить на своем локальном компьютере разработчика. Драйвер C# можно получить через NuGet.

Порядок установки драйвера C# для MongoDB:

1. В **обозревателе решений** в проекте **MyTaskListApp** щелкните правой кнопкой мыши элемент **Ссылки** и выберите пункт **Управление пакетами NuGet**.

	![Manage NuGet Packages][VS2013ManageNuGetPackages]

2. В левой части окна **Управление пакетами NuGet** щелкните элемент **В сети**. В расположенном справа поле **Поиск в Интернете** введите mongocsharpdriver.  Щелкните элемент **Установить**, чтобы установить драйвер.

	![Search for MongoDB C# Driver][SearchforMongoDBCSharpDriver]

3. Нажмите кнопку **Принимаю**, чтобы принять условия лицензионного соглашения 10gen, Inc.

4. После установки драйвера нажмите кнопку **Закрыть**.
	![MongoDB C# Driver Installed][MongoDBCsharpDriverInstalled]


Теперь драйвер C# для MongoDB установлен.  В проект были добавлены ссылки на библиотеки **MongoDB.Driver.dll** и **MongoDB.Bson.dll**.

![MongoDB C# Driver References][MongoDBCSharpDriverReferences]

###Добавление модели
В **обозревателе решений** щелкните правой кнопкой мыши папку "Модели", затем нажмите**Добавить**, чтобы добавить новый **Класс**, и назовите его TaskModel.cs.  Замените существующий код в файле TaskModel.cs на следующий код:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MongoDB.Bson.Serialization.Attributes;
	using MongoDB.Bson.Serialization.IdGenerators;
	using MongoDB.Bson;
	
	namespace MyTaskListApp.Models
	{
	    public class MyTask
	    {
	        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
	        public Guid Id { get; set; }
	
	        [BsonElement("Name")]
	        public string Name { get; set; }
	
	        [BsonElement("Category")]
	        public string Category { get; set; }
	
	        [BsonElement("Date")]
	        public DateTime Date { get; set; }
	
	        [BsonElement("CreatedDate")]
	        public DateTime CreatedDate { get; set; }
	
	    }
	}

###Добавление уровня доступа к данным
В **обозревателе решений** щелкните правой кнопкой мыши проект MyTaskListApp и нажмите **Добавить**, чтобы добавить **Новую папку**, и назовите ее DAL.  Щелкните правой кнопкой мыши папку DAL и выберите **Добавить**, чтобы добавить новый **Класс**. Назовите файл класса Dal.cs.  Замените существующий код в файле Dal.cs на следующий код:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using System.Configuration;
	
	namespace MyTaskListApp
	{
	    public class Dal : IDisposable
	    {
	        private MongoServer mongoServer = null;
	        private bool disposed = false;
	
	        // To do: update the connection string with the DNS name
			// or IP address of your server. 
			//For example, "mongodb://testlinux.cloudapp.net"
	        private string connectionString = "mongodb://<vm-dns-name>";
	
	        // This sample uses a database named "Tasks" and a 
			//collection named "TasksList".  The database and collection 
			//will be automatically created if they don't already exist.
	        private string dbName = "Tasks";
	        private string collectionName = "TasksList";
	
	        // Default constructor.        
	        public Dal()
	        {
	        }        
	
	        // Gets all Task items from the MongoDB server.        
	        public List<MyTask> GetAllTasks()
	        {
	            try
	            {
	                MongoCollection<MyTask> collection = GetTasksCollection();
	                return collection.FindAll().ToList<MyTask>();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask >();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            MongoCollection<MyTask> collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.Insert(task, SafeMode.True);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private MongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private MongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        # region IDisposable
	
	        public void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    if (mongoServer != null)
	                    {
	                        this.mongoServer.Disconnect();
	                    }
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	    }
	}

###Добавление контроллера
Откройте файл Controllers\HomeController.cs в **обозревателе решений** и замените имеющийся код на следующий:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;
	using MyTaskListApp.Models;
	using System.Configuration;
	
	namespace MyTaskListApp.Controllers
	{
	    public class HomeController : Controller, IDisposable
	    {
	        private Dal dal = new Dal();
	        private bool disposed = false;
	        //
	        // GET: /MyTask/
	
	        public ActionResult Index()
	        {
	            return View(dal.GetAllTasks());
	        }
	
	        //
	        // GET: /MyTask/Create
	
	        public ActionResult Create()
	        {
	            return View();
	        }
	
	        //
	        // POST: /MyTask/Create
	
	        [HttpPost]
	        public ActionResult Create(MyTask task)
	        {
	            try
	            {
	                dal.CreateTask(task);
	                return RedirectToAction("Index");
	            }
	            catch
	            {
	                return View();
	            }
	        }
	
	        public ActionResult About()
	        {
	            return View();
	        }
	
	        # region IDisposable
	
	        new protected void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        new protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    this.dal.Dispose();
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	
	    }
	}

###Настройка стиля сайта
Чтобы изменить заголовок в верхней части страницы, откройте файл Views\Shared\\_Layout.cshtml в **обозревателе решений** и замените "Имя приложения" в заголовке панели переходов на "Мое приложение списка задач", чтобы он выглядел следующим образом:

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Для настройки меню списка задач откройте файл \Views\Home\Index.cshtml и замените существующий код следующим:
	
	@model IEnumerable<MyTaskListApp.Models.MyTask>
	
	@{
	    ViewBag.Title = "My Task List";
	}
	
	<h2>My Task List</h2>
	
	<table border="1">
	    <tr>
	        <th>Task</th>
	        <th>Category</th>
	        <th>Date</th>
	        
	    </tr>
	
	@foreach (var item in Model) {
	    <tr>
	        <td>
	            @Html.DisplayFor(modelItem => item.Name)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Category)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Date)
	        </td>
	        
	    </tr>
	}
	
	</table>
	<div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


To add the ability to create a new task, right-click the *Views\Home\\* folder and **Add** a **View**.  Name the view *Create*. Replace the code with the following:

	@model MyTaskListApp.Models.MyTask
	
	<script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
	
	@using (Html.BeginForm("Create", "Home")) {
	    @Html.ValidationSummary(true)
	    <fieldset>
	        <legend>New Task</legend>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Name)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Name)
	            @Html.ValidationMessageFor(model => model.Name)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Category)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Category)
	            @Html.ValidationMessageFor(model => model.Category)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Date)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Date)
	            @Html.ValidationMessageFor(model => model.Date)
	        </div>
	
	        <p>
	            <input type="submit" value="Create" />
	        </p>
	    </fieldset>
	}

**Обозреватель решений** должен выглядеть следующим образом:

![Solution Explorer][SolutionExplorerMyTaskListApp]

###Установка строки подключения MongoDB
В **обозревателе решений** откройте файл DAL/Dal.cs. Найдите следующую строку кода:

	private string connectionString = "mongodb://<vm-dns-name>";

Замените <vm-dns-name> на DNS-имя виртуальной машины, где выполняется база данных MongoDB, созданная на шаге [Создание виртуальной машины и установка MongoDB][] данного учебника.  Чтобы найти DNS-имя виртуальной машины, перейдите на портал управления Azure, выберите **Виртуальные машины** и **DNS-имя**.

Если DNS-имя виртуальной машины имеет значение "testlinuxvm.cloudapp.net" и MongoDB ведет прослушивание порта по умолчанию 27017, строка подключения в коде будет выглядеть следующим образом:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Если конечная точка виртуальной машины задает другой внешний порт для MongoDB, можно указать этот порт в строке подключения:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Дополнительную информацию о строках подключения MongoDB см. в разделе [Подключения][MongoConnectionStrings].

###Тестирование локального развертывания

Для запуска приложения на компьютере разработчика выберите пункт **Начать отладку** в меню **Отладка** или нажмите клавишу **F5**. Запускается IIS Express, открывается браузер и запускается главная страница приложения.  Можно добавить новую задачу, которая будет добавлена в базу данных MongoDB, выполняемую на виртуальной машине в Azure.

![My Task List Application][TaskListAppBlank]

<h2>Развертывание приложения ASP.NET на веб-сайте Azure</h2>

В данном разделе с помощью Git будет создан веб-сайт и развернуто приложение ASP.NET под именем "Мой список задач".

<a id="createwebsite"></a> 
###Создание веб-сайта Azure
В этом разделе будет создан веб-сайт Azure.

1. Откройте веб-браузер и перейдите на [портал управления Azure][AzurePortal]. Войдите в систему с использованием учетной записи Azure. 
2. В нижней части страницы щелкните **+Создать**, затем щелкните **Веб-сайт** и выберите **Быстрое создание**.
3. Введите уникальный префикс URL-адреса приложения.
4. Выберите регион.
5. Щелкните **Создать веб-сайт**.

![Create a new web site][WAWSCreateWebSite]

6. Веб-сайт создается быстро и будет указан в списке **Веб-сайты**.

![WAWSDashboardMyTaskListApp][WAWSDashboardMyTaskListApp]

<a id="deployapp"></a> 
###Развертывание приложения ASP.NET на веб-сайте с помощью Git
В этом разделе приложение "Мой список задач" будет развернуто с помощью Git.

1. Щелкните имя своего веб-сайта в области **Веб-сайты**, затем **Панель мониторинга**.  В разделе "Сводка" в правой части окна щелкните **Настроить развертывания в системе управления версиями**.
2. На странице **Где исходный код?** выберите **Локальный репозиторий Git** и щелкните стрелку **Далее**. 
3. Создание репозитория Git должно занять совсем немного времени. Запишите инструкции на полученной странице, так как они будут использоваться в следующем разделе.

	![Git Repository is Ready][Image9]

4. В разделе **Передача локальных файлов в Azure** приведены указания по перемещению кода в Azure. Эти инструкции будут выглядеть примерно следующим образом:

	![Push local files to Azure][Image10]
	
5. Если у вас не установлен Git, установите его с помощью ссылки **Получить** в шаге 1.
6. Следуя указаниям из шага 2, сохраните локальные файлы.  
7. Добавьте удаленный репозиторий Azure и передайте файлы на веб-сайт Azure, следуя инструкциям из шага 3.
8. После завершения развертывания можно увидеть следующее подтверждение:

	![Deployment Complete][Image11]

9. Теперь ваш веб-сайт Azure доступен для использования.  Откройте страницу **Панель мониторинга** для своего сайта и проверьте поле **URL-адрес сайта**, чтобы узнать URL-адрес сайта. После выполнения описанных здесь процедур ваш сайт будет доступен по следующему URL-адресу: http://mytasklistapp.azurewebsites.net.

##Сводка

Вы успешно развернули приложение ASP.NET на веб-сайте Azure.  Для просмотра сайта выберите ссылку в поле **URL-адрес сайта** на странице **Панель мониторинга**. Дополнительную информацию о разработке приложений C# для MongoDB см. в [центре языка CSharp][MongoC#LangCenter]. 


<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnCentOSLinuxVM]: /ru-ru/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[InstallMongoOnWindowsVM]: /ru-ru/manage/windows/common-tasks/install-mongodb/
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Создание виртуальной машины и установка MongoDB]: #virtualmachine
[Создание и запуск приложения ASP.NET "Мой список задач" на компьютере разработчика]: #createapp
[Создание веб-сайта Azure]: #createwebsite
[Развертывание приложения ASP.NET на веб-сайте с помощью Git]: #deployapp

<!--HONumber=35.1-->
