<properties 
	pageTitle="Создание веб-приложения в Azure, которое подключается к базе данных MongoDB, работающей на виртуальной машине" 
	description="В этом учебнике показано, как использовать Git для развертывания приложения ASP.NET в службе приложений Azure, подключенной к MongoDB на виртуальной машине Azure."
	tags="azure-portal" 
	services="app-service\web, virtual-machines" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="cephalin"/>


# Создание веб-приложения в Azure, которое подключается к базе данных MongoDB, работающей на виртуальной машине

С помощью Git можно развернуть приложение ASP.NET в веб-приложениях службы приложений Azure. В этом учебнике будет создано простое приложение списка задач ASP.NET MVC, которое подключается к базе данных MongoDB, работающей на виртуальной машине в Azure. [MongoDB][MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом. После запуска и тестирования приложения ASP.NET на компьютере разработчика приложение будет отправлено в веб-приложения службы Azure с помощью Git.

>[AZURE.NOTE]Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.


## Фоновые знания ##

При изучении данного учебника знание следующих тем может оказаться полезным, хотя и не является обязательным требованием:

* Драйвер C# для MongoDB. Дополнительные сведения о разработке приложений C# для MongoDB см. в [центре языка CSharp][MongoC#LangCenter]. 
* Платформа веб-приложений ASP .NET. Всестороннее описание данного вопроса см. на [веб-сайте ASP.net][ASP.NET].
* Платформа веб-приложений ASP .NET MVC. Всестороннее описание данного вопроса см. на [веб-сайте ASP.NET MVC][MVCWebSite].
* Azure. Для начала можно ознакомиться с разделом [Azure][WindowsAzure].

## Предварительные требования ##

- [Visual Studio 2013 Express для Web][VSEWeb] или [Visual Studio 2013][VSUlt]
- [Пакет Azure SDK для .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- Активная подписка на Microsoft Azure

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a>
## Создание виртуальной машины и установка MongoDB ##

В этом учебном курсе предполагается, что вы создали виртуальную машину в Azure. После создания виртуальной машины необходимо установить на ней MongoDB.

* Создание виртуальной машины Windows и установка MongoDB описаны в разделе [Установка MongoDB на виртуальной машине под управлением Windows Server в Azure][InstallMongoOnWindowsVM].
* Кроме того, при создании виртуальной машины Linux и установке MongoDB можно обратиться к разделу [Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure][InstallMongoOnCentOSLinuxVM].

После создания виртуальной машины в Azure и установки MongoDB обязательно запомните DNS-имя виртуальной машины (например, "testlinuxvm.cloudapp.net") и внешний порт для MongoDB, указанный в конечной точке. Эти сведения потребуются позже в данном учебном курсе.

<a id="createapp"></a>
## Создание приложения ##

В данном разделе с помощью Visual Studio будет создано приложение ASP.NET под именем "Мой список задач" и выполнено его первоначальное развертывание в веб-приложения службы приложений Azure. Приложение будет выполняться локально, но будет подключаться к виртуальной машине в Azure и использовать созданный там экземпляр MongoDB.

1. В Visual Studio щелкните **Создать проект**.

	![Начальная страница, новый проект][StartPageNewProject]

1. В левой области окна **Новый проект** выберите **Visual C#**, а затем выберите **Веб-сайт**. В средней области выберите **Веб-приложение ASP.NET**. В нижней части введите имя "MyTaskListApp" для проекта и нажмите кнопку **ОК**.

	![Диалоговое окно "Новый проект"][NewProjectMyTaskListApp]

1. В диалоговом окне **Новый проект ASP.NET** выберите **MVC** и нажмите кнопку **ОК**.

	![Выбор шаблона MVC][VS2013SelectMVCTemplate]

1. Если вы еще не выполнили вход в Microsoft Azure, будет предложено войти. Следуйте инструкциям для входа в Azure.
2. После входа можно будет настроить веб-приложение службы приложений. Укажите **имя веб-приложения**, **план службы приложений**, **группу ресурсов** и **регион**, а затем нажмите кнопку **ОК**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. После завершения создания проекта дождитесь создания веб-приложения в службе приложений Azure, как указано в окне **Действия службы приложений Azure**. Нажмите кнопку **Опубликовать MyTaskListApp в этом веб-приложении**.

1. Щелкните **Опубликовать**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

	После публикации приложения ASP.NET по умолчанию в веб-приложения службы приложений Azure приложение запустится в браузере.

## Установка драйвера C# для MongoDB

MongoDB обеспечивает поддержку приложений C# на стороне клиента посредством драйвера, который необходимо установить на своем локальном компьютере разработчика. Драйвер C# можно получить через NuGet.

Порядок установки драйвера C# для MongoDB:

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **MyTaskListApp** и выберите **Управление пакетами NuGet**.

	![Управление пакетами NuGet][VS2013ManageNuGetPackages]

2. В левой части окна **Управление пакетами NuGet** щелкните **В сети**. В расположенном справа поле **Поиск в Интернете** введите "mongocsharpdriver". Щелкните элемент **Установить**, чтобы установить драйвер.

	![Поиск драйвера C# для MongoDB][SearchforMongoDBCSharpDriver]

3. Нажмите кнопку **Принимаю**, чтобы принять условия лицензионного соглашения 10gen, Inc.

4. После установки драйвера нажмите кнопку **Закрыть**. ![Драйвер C# для MongoDB установлен][MongoDBCsharpDriverInstalled]


Теперь драйвер C# для MongoDB установлен. В проект были добавлены ссылки на библиотеки **MongoDB.Driver.dll** и **MongoDB.Bson.dll**.

![Ссылки на драйвер C# для MongoDB][MongoDBCSharpDriverReferences]

## Добавление модели ##
В **обозревателе решений** щелкните правой кнопкой по папке *Модели* и выберите команду **Добавить** новый **Класс**. Назовите его *TaskModel.cs*. Замените существующий код в файле *TaskModel.cs* на следующий код:

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

## Добавление уровня доступа к данным ##
В **обозревателе решений** щелкните правой кнопкой мыши по проекту *MyTaskListApp* и выберите команду **Добавить** **Новую папку** под именем *DAL*. Щелкните правой кнопкой мыши папку *DAL* и выберите **Добавить**, чтобы добавить новый **класс**. Назначьте файлу класса имя *Dal.cs*. Замените существующий код в файле *Dal.cs* на следующий код:

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

## Добавление контроллера ##
Откройте файл *Controllers\\HomeController.cs* в **обозревателе решений** и замените имеющийся код на следующий:

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

## Настройка стилей ##
Чтобы изменить заголовок в верхней части страницы, откройте файл *Views\\Shared\\_Layout.cshtml* в **обозревателе решений** и замените "Имя приложения" в заголовке панели переходов на "Мое приложение списка задач", чтобы он выглядел следующим образом.

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Для настройки меню списка задач откройте файл *\\Views\\Home\\Index.cshtml* и замените существующий код следующим:
	
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


Чтобы добавить возможность создания новой задачи, щелкните правой кнопкой мыши по папке *Views\\Home\* и выберите команду **Добавить** **Представление**. Присвойте представлению имя *Create*. Замените код на приведенный ниже:

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

![Обозреватель решений][SolutionExplorerMyTaskListApp]

## Установка строки подключения MongoDB ##
В **обозревателе решений** откройте файл *DAL/Dal.cs*. Найдите следующую строку кода:

	private string connectionString = "mongodb://<vm-dns-name>";

Замените `<vm-dns-name>` на DNS-имя виртуальной машины, где выполняется база данных MongoDB, созданная на шаге [Создание виртуальной машины и установка MongoDB][] в данном учебнике. Чтобы найти DNS-имя виртуальной машины, перейдите на портал управления Azure, выберите **Виртуальные машины** и **DNS-имя**.

Если DNS-имя виртуальной машины имеет значение "testlinuxvm.cloudapp.net" и MongoDB ведет прослушивание порта по умолчанию 27017, строка подключения в коде будет выглядеть следующим образом:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Если конечная точка виртуальной машины задает другой внешний порт для MongoDB, можно указать этот порт в строке подключения:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Дополнительные сведения о строках подключения MongoDB см. в разделе [Подключения][MongoConnectionStrings].

## Тестирование локального развертывания ##

Для запуска приложения на компьютере разработчика выберите пункт **Начать отладку** в меню **Отладка** или нажмите клавишу **F5**. Запускается IIS Express, открывается браузер и запускается главная страница приложения. Можно добавить новую задачу, которая будет добавлена в базу данных MongoDB, выполняемую на виртуальной машине в Azure.

![Приложение "My Task List"][TaskListAppBlank]

## Публикация в веб-приложения службы приложений Azure

В этом разделе внесенные изменения будут опубликованы в веб-приложения службы приложений Azure.

1. В обозревателе решений щелкните правой кнопкой мыши **MyTaskListApp** и выберите пункт **Опубликовать**.
2. Щелкните **Опубликовать**.

	Веб-приложение будет работать в службе приложений Azure и получит доступ к базе данных MongoDB на виртуальных машинах Azure.

## Сводка ##

Вы успешно развернули приложение ASP.NET в веб-приложениях службы приложений Azure. Просмотр веб-приложения

1. Войдите на портал Azure.
2. Нажмите **Веб-приложения**. 
3. Выберите веб-приложение из списка **Веб-приложения**.

Дополнительные сведения о разработке приложений C# для MongoDB см. в [центре языка CSharp][MongoC#LangCenter].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnCentOSLinuxVM]: /manage/linux/common-tasks/mongodb-on-a-linux-vm/
[InstallMongoOnWindowsVM]: /manage/windows/common-tasks/install-mongodb/
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
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 

<!---HONumber=July15_HO3-->