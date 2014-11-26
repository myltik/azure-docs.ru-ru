<properties linkid="develop-dotnet-website-with-mongodb-vm" urlDisplayName="Website with MongoDB VM" pageTitle=".NET website with MongoDB on a virtual machine - Azure" metaKeywords="Azure Git ASP.NET MongoDB, Git .NET, Git MongoDB, ASP.NET MongoDB, Azure MongoDB, Azure ASP.NET, Azure tutorial" description="A tutorial that teaches you how to use Git to deploy an ASP.NET app to an Azure website connected to MongoDB on a virtual machine." metaCanonical="" services="web-sites,virtual-machines" documentationCenter=".NET" title="Create an Azure website that connects to MongoDB running on a virtual machine in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Создание веб-сайта Azure, который подключается к базе данных MongoDB, выполняющейся на виртуальной машине в Azure

С помощью Git можно развернуть приложение ASP.NET на веб-сайте Azure. В этом учебнике будет создано простое приложение списка задач MVC ASP.NET, которое подключается к базе данных MongoDB, запущенной на виртуальной машине в Azure. [MongoDB][MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом. После запуска и тестирования приложения ASP.NET на компьютере разработчика вы отправите приложение на веб-сайт Azure с помощью Git.

[WACOM.INCLUDE [create-account-and-websites-and-vms-note](../includes/create-account-and-websites-and-vms-note.md)]

## Обзор

Изучив данный учебник, вы научитесь:

-   [Создание виртуальной машины и установка MongoDB][Создание виртуальной машины и установка MongoDB]
-   [Создание и запуск приложения "My Task List" ASP.NET на компьютере разработчика][Создание и запуск приложения "My Task List" ASP.NET на компьютере разработчика]
-   [Создание веб-сайта Azure][Создание веб-сайта Azure]
-   [Развертывание приложения ASP.NET на веб-сайте с помощью Git][Развертывание приложения ASP.NET на веб-сайте с помощью Git]

## Фоновые знания

При изучении данного учебника знание следующих тем может оказаться полезным, хотя и не является обязательным требованием:

-   Драйвер C# для MongoDB. Дополнительные сведения о разработке приложений C# для MongoDB см. в [центре языка CSharp][центре языка CSharp].
-   Платформа веб-приложений ASP .NET. Всестороннее описание данного вопроса приведено на [веб-сайте ASP.net][веб-сайте ASP.net].
-   Платформа веб-приложений ASP .NET MVC. Всестороннее описание данного вопроса приведено на [веб-сайте ASP.NET MVC][веб-сайте ASP.NET MVC].
-   Azure. Для начала можно ознакомиться с разделом [Azure][Azure].

## Подготовка

В этом разделе будет рассказано, как создать виртуальную машину в Azure и установить MongoDB, а также настроить среду разработки.

<span id="virtualmachine"></span></a>

### Создание виртуальной машины и установка MongoDB

В этом учебном курсе предполагается, что вы создали виртуальную машину в Azure. После создания виртуальной машины необходимо установить на ней MongoDB.

-   Создание виртуальной машины Windows и установка MongoDB описаны в разделе [Установка MongoDB на виртуальной машине под управлением Windows Server в Azure][Установка MongoDB на виртуальной машине под управлением Windows Server в Azure].
-   Кроме того, при создании виртуальной машины Linux и установке MongoDB можно обратиться к разделу [Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure][Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure].

После создания виртуальной машины в Azure и установки MongoDB обязательно запомните DNS-имя виртуальной машины (например, "testlinuxvm.cloudapp.net") и внешний порт для MongoDB, указанный в конечной точке. Эти сведения потребуются позже в данном учебном курсе.

### Установка Visual Studio

Начните с установки и запуска [Visual Studio Express 2013 для Web][Visual Studio Express 2013 для Web] или [Visual Studio 2013][Visual Studio 2013].

Visual Studio — это интегрированная среда разработки. Как Microsoft Word используется для составления документов, так и интегрированная среда разработки используется для создания приложений. В этом учебнике используется Microsoft Visual Studio 2013, однако можно использовать Visual Studio Express 2013 — бесплатную версию Microsoft Visual Studio.

<span id="createapp"></span></a>

## Создание и запуск приложения "My Task List" ASP.NET на компьютере разработчика

В данном разделе с помощью Visual Studio будет создано приложение ASP.NET, имеющее имя "My Task List". Приложение будет выполняться локально, но будет подключаться к виртуальной машине в Azure и использовать созданный там экземпляр MongoDB.

### Создание приложения

В Visual Studio щелкните **Создать проект**.

![Начальная страница, новый проект][Начальная страница, новый проект]

В левой области окна **Новый проект** выберите **Visual C#**, а затем выберите **Веб-сайт**. В средней области выберите **Веб-приложение ASP.NET**. В нижней части введите имя "MyTaskListApp" для проекта и нажмите кнопку **ОК**.

![Диалоговое окно "Новый проект"][Диалоговое окно "Новый проект"]

В диалоговом окне **Новый проект ASP.NET** выберите **MVC** и нажмите кнопку **ОК**.

![Выбор шаблона MVC][Выбор шаблона MVC]

После завершения проекта отображается страница по умолчанию, созданная с помощью шаблона.

![Приложение ASP.NET MVC по умолчанию][Приложение ASP.NET MVC по умолчанию]

### Установка драйвера C# для MongoDB

MongoDB обеспечивает поддержку приложений C# на стороне клиента посредством драйвера, который необходимо установить на своем локальном компьютере разработчика. Драйвер C# можно получить через NuGet.

Порядок установки драйвера C# для MongoDB:

1.  В **обозревателе решений** в проекте **MyTaskListApp** щелкните правой кнопкой мыши по элементу **Ссылки** и выберите **Управление пакетами NuGet**.

    ![Управление пакетами NuGet][Управление пакетами NuGet]

2.  В левой части окна **Управление пакетами NuGet** щелкните **В сети**. В расположенном справа поле **Поиск в Интернете** введите "mongocsharpdriver". Щелкните элемент **Установить**, чтобы установить драйвер.

    ![Поиск драйвера C# для MongoDB][Поиск драйвера C# для MongoDB]

3.  Нажмите кнопку **Принимаю**, чтобы принять условия лицензионного соглашения 10gen, Inc.

4.  После установки драйвера нажмите кнопку **Закрыть**.
    ![Драйвер MongoDB C# установлен][Драйвер MongoDB C# установлен]

Теперь драйвер C# для MongoDB установлен. В проект были добавлены ссылки на библиотеки **MongoDB.Driver.dll** и **MongoDB.Bson.dll**.

![Ссылки на драйвер C# для MongoDB][Ссылки на драйвер C# для MongoDB]

### Добавление модели

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

### Добавление уровня доступа к данным

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

### Добавление контроллера

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

### Настройка стиля сайта

Чтобы изменить заголовок в верхней части страницы, откройте файл Views\\Shared\\\_Layout.cshtml в **обозревателе решений** и замените "Имя приложения" в заголовке панели переходов на "Мое приложение списка задач", чтобы он выглядел следующим образом.

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

Чтобы добавить возможность создания новой задачи, щелкните правой кнопкой мыши по папке *Views\\Home\\* и выберите команду **Добавить** **Представление**. Присвойте представлению имя *Create*. Замените код на приведенный ниже:

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

![Обозреватель решений][Обозреватель решений]

### Установка строки подключения MongoDB

В **обозревателе решений** откройте файл *DAL/Dal.cs*. Найдите следующую строку кода:

    private string connectionString = "mongodb://<vm-dns-name>";

Замените `<vm-dns-name>` на DNS-имя виртуальной машины, где выполняется база данных MongoDB, созданная на шаге [Создание виртуальной машины и установка MongoDB][Создание виртуальной машины и установка MongoDB]. Чтобы найти DNS-имя виртуальной машины, перейдите на портал управления Azure, выберите **Виртуальные машины** и **DNS-имя**.

Если DNS-имя виртуальной машины имеет значение "testlinuxvm.cloudapp.net" и MongoDB ведет прослушивание порта по умолчанию 27017, строка подключения в коде будет выглядеть следующим образом:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Если конечная точка виртуальной машины задает другой внешний порт для MongoDB, можно указать этот порт в строке подключения:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Дополнительные сведения о строках подключения MongoDB см. в разделе [Подключения][Подключения].

### Тестирование локального развертывания

Для запуска приложения на компьютере разработчика выберите пункт **Начать отладку** в меню **Отладка** или нажмите клавишу **F5**. Запускается IIS Express, открывается браузер и запускается главная страница приложения. Можно добавить новую задачу, которая будет добавлена в базу данных MongoDB, выполняемую на виртуальной машине в Azure.

![Приложение "My Task List"][Приложение "My Task List"]

## <span class="short-header">Развертывание приложения на веб-сайте Azure</span>Развертывание приложения ASP.NET на веб-сайте Azure

В данном разделе с помощью Git будет создан веб-сайт и развернуто приложение ASP.NET под именем "Мой список задач".

<span id="createwebsite"></span></a>

### Создание веб-сайта Azure

В этом разделе будет создан веб-сайт Azure.

1.  Откройте веб-браузер и перейдите на [портал управления Azure][портал управления Azure]. Войдите в систему с использованием учетной записи Azure.
2.  Щелкните **+Создать** в нижней части страницы, затем щелкните **Веб-сайт** и выберите **Быстрое создание**.
3.  Введите уникальный префикс URL-адреса приложения.
4.  Выберите регион.
5.  Щелкните **Создать веб-сайт**.

![Создание нового веб-сайта][Создание нового веб-сайта]

1.  Веб-сайт создается быстро и будет указан в списке **Веб-сайты**.

![WAWSDashboardMyTaskListApp][WAWSDashboardMyTaskListApp]

<span id="deployapp"></span></a>

### Развертывание приложения ASP.NET на веб-сайте с помощью Git

В этом разделе приложение "Мой список задач" будет развернуто с помощью Git.

1.  Щелкните по имени веб-сайта в области **Веб-сайты**, затем **Панель мониторинга**. В разделе "Сводка" в правой части окна щелкните **Настроить развертывания в системе управления версиями**.
2.  На странице **Где находится исходный код?** выберите **Локальный репозиторий Git** и щелкните по стрелке **Далее**.
3.  Создание репозитория Git должно занять совсем немного времени. Запишите инструкции на полученной странице, так как они будут использоваться в следующем разделе.

    ![Репозиторий Git готов.][Репозиторий Git готов.]

4.  В разделе **Передача локальных файлов в Azure** приведены инструкции по перемещению кода в Azure. Эти инструкции будут выглядеть примерно следующим образом:

    ![Передача локальных файлов в Azure][Передача локальных файлов в Azure]

5.  Если у вас не установлен Git, установите его с помощью ссылки **Получить** на шаге 1.
6.  Следуя инструкциям из шага 2, сохраните локальные файлы.
7.  Добавьте удаленный репозиторий Azure и передайте файлы на веб-сайт Azure, следуя инструкциям из шага 3.
8.  После завершения развертывания можно увидеть следующее подтверждение:

    ![Развертывание завершено][Развертывание завершено]

9.  Теперь ваш веб-сайт Azure доступен для использования. Откройте страницу **Панель мониторинга** для своего веб-сайта и проверьте поле **URL-адрес сайта**, чтобы узнать URL-адрес веб-сайта. После выполнения описанных здесь процедур сайт будет доступен по следующему URL-адресу: <http://mytasklistapp.azurewebsites.net>.

## Сводка

Вы успешно развернули приложение ASP.NET на веб-сайте Azure. Для просмотра сайта выберите ссылку в поле **URL-адрес сайта** на странице **Панель мониторинга**. Дополнительные сведения о разработке приложений C# для MongoDB см. в [центре языка CSharp][центре языка CSharp].



  [MongoDB]: http://www.mongodb.org
  [Создание виртуальной машины и установка MongoDB]: #virtualmachine
  [Создание и запуск приложения "My Task List" ASP.NET на компьютере разработчика]: #createapp
  [Создание веб-сайта Azure]: #createwebsite
  [Развертывание приложения ASP.NET на веб-сайте с помощью Git]: #deployapp
  [центре языка CSharp]: http://docs.mongodb.org/ecosystem/drivers/csharp/
  [веб-сайте ASP.net]: http://www.asp.net/
  [веб-сайте ASP.NET MVC]: http://www.asp.net/mvc
  [Azure]: http://www.windowsazure.com
  [Установка MongoDB на виртуальной машине под управлением Windows Server в Azure]: /ru-ru/manage/windows/common-tasks/install-mongodb/
  [Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure]: /ru-ru/manage/linux/common-tasks/mongodb-on-a-linux-vm/
  [Visual Studio Express 2013 для Web]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
  [Visual Studio 2013]: http://www.microsoft.com/visualstudio/eng/2013-downloads
  [Начальная страница, новый проект]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
  [Диалоговое окно "Новый проект"]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
  [Выбор шаблона MVC]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
  [Приложение ASP.NET MVC по умолчанию]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
  [Управление пакетами NuGet]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
  [Поиск драйвера C# для MongoDB]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
  [Драйвер MongoDB C# установлен]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
  [Ссылки на драйвер C# для MongoDB]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
  [Обозреватель решений]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
  [Подключения]: http://www.mongodb.org/display/DOCS/Connections
  [Приложение "My Task List"]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
  [портал управления Azure]: http://manage.windowsazure.com
  [Создание нового веб-сайта]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
  [WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
  [Репозиторий Git готов.]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
  [Передача локальных файлов в Azure]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
  [Развертывание завершено]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png
