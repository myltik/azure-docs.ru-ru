<properties 
	pageTitle="Создание веб-приложения .NET в Azure с MongoDB с помощью надстройки MongoLab" 
	description="Узнайте, как создать веб-приложение ASP.NET в службе приложений Azure, которая хранит данные в MongoDB, размещенной в MongoLab." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="chrisckchang" 
	manager="partners@mongolab.com" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="chris@mongolab.com"/>



# Создание веб-приложения .NET в Azure с MongoDB с помощью надстройки MongoLab

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

<p><em>Автор: Эрик Седор (Eric Sedor), MongoLab</em></p>

Приветствуем вас, искатели приключений! Добро пожаловать в MongoDB как услуга. Изучив данный учебник, вы научитесь:

1. [Подготавливать базу данных][provision]. Надстройка [MongoLab](http://mongolab.com), доступная в Azure Marketplace, позволяет получать доступ к базе данных MongoDB, размещенной в облаке Azure, и управляется облачной платформой базы данных MongoLab.
1. [Создавать приложение][create] — это будет простое приложение ASP.NET MVC на C# для создания заметок.
1. [Развертывать приложение][deploy]. Объединив вместе несколько обработчиков конфигурации, мы значительно упрощаем перемещение кода в [веб-приложения службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
1. [Управлять базой данных][manage] — наконец, мы покажем портал управления базами данных MongoLab, где можно без труда искать, визуализировать и изменять данные.

Если во время работы с этим учебником у вас возникнут какие-либо вопросы, обращайтесь с ними по адресу [support@mongolab.com](mailto:support@mongolab.com).

## Быстрый запуск
Если у вас уже есть необходимое веб-приложение в службе приложений Azure или опыт работы с Azure Marketplace, используйте этот раздел в качестве краткого руководства. В противном случае изучите приведенный ниже раздел [Подготовка базы данных][provision].
 
1. Откройте Azure Marketplace, последовательно щелкнув **Создать** > **Marketplace**. 
	<!-- ![Store][button-store] -->

1. Приобретите надстройку MongoLab. 
	![MongoLab][entry-mongolab]

1. Щелкните надстройку MongoLab в списке надстроек, а затем выберите пункт **Сведения о подключении**.  
	![ConnectionInfoButton][button-connectioninfo]

1. Скопируйте MONGOLAB_URI в буфер обмена.  
	![ConnectionInfoScreen][screen-connectioninfo]  
	**Этот универсальный код ресурса (URI) содержит имя пользователя базы данных и пароль. Считайте это конфиденциальной информацией и не передавайте ее.**

1. Добавьте значение в список "Строки подключения" в меню "Конфигурация" веб-приложения Azure:  
	![WebSiteConnectionStrings][focus-website-connectinfo]

1. В качестве **Имени** введите MONGOLAB_URI.

1. В качестве **Значение** вставьте строку подключения, полученную в предыдущем разделе.

1. В раскрывающемся списке «Тип» выберите пункт **Настраиваемый** (вместо значения по умолчанию **SQLAzure**).

1. В Visual Studio установите драйвер Mongo для C#. Для этого выберете **Средства -> Диспетчер пакетов библиотеки -> Консоль диспетчера пакетов**. В консоли PM введите команду **Install-Package mongocsharpdriver** и нажмите клавишу **ВВОД**.

1. Настройте обработчик в коде, чтобы получить универсальный код ресурса (URI) подключения MongoLab из переменной среды:

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

> **Примечание.** Azure добавляет префикс **CUSTOMCONNSTR\_** в исходно объявленную строку подключения, поэтому код ссылается на **CUSTOMCONNSTR_MONGOLAB\_URI.**, а не на **MONGOLAB\_URI**.

Теперь перейдем к полному учебнику...

<a name="provision"></a>
## Подготовка базы данных

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Создание приложения

В этом разделе мы рассмотрим создание проекта C# ASP.NET в Visual Studio и использование драйвера C# MongoDB для разработки простого приложения создания заметок. Вы сможете ознакомиться с вашим веб-приложением, создать заметку и просмотреть всех оставшиеся заметки.

Действия выполняются в Visual Studio Express 2013 для Web.

### Создание проекта
Для примера приложения используется шаблон Visual Studio. Убедитесь, что используется платформа .NET Framework 4.5.

1. Выберите **Файл -> Новый проект**. Появится диалоговое окно "Новый проект":    
	![NewProject][dialog-mongolab-csharp-newproject]

1. Выберите **Установлено -> Шаблоны -> Visual C# -> Интернет**.

1. В раскрывающемся меню версий .NET выберите пункт **.NET Framework 4.5**.

1. Выберите пункт **Приложение MVC**.

1. В поле _Имя проекта_ введите **mongoNotes**. Если вы выбрали другое имя, потребуется изменить код, используемый в учебнике.

1. Выберите **Средства -> Диспетчер пакетов библиотеки -> Консоль диспетчера пакетов**. В консоли PM введите команду **Install-Package mongocsharpdriver** и нажмите клавишу **ВВОД**.  
	![PMConsole][focus-mongolab-csharp-pmconsole] 
	Драйвер MongoDB C# интегрируется с проектом, а в файл _packages.config_ будет автоматически добавлена следующая строка:

        < package id="mongocsharpdriver" version="1.9.2" targetFramework="net45" / >

### Добавление модели заметки
Сначала создайте модель для заметок, содержащую просто дату и текст.

1. Щелкните правой кнопкой мыши пункт **Модели** в обозревателе решений и выберите **Добавить -> Класс**. Задайте для этого нового класса имя *Note.cs*.

1. Замените автоматически сгенерированный код для этого класса следующим кодом:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MongoDB.Bson.Serialization.Attributes;
        using MongoDB.Bson.Serialization.IdGenerators;
        using MongoDB.Bson;
                
        namespace mongoNotes.Models
        {
            public class Note
            {
                public Note()
                {
                    Date = DateTime.UtcNow;
                }
                
                private DateTime date;
        
                [BsonId(IdGenerator = typeof(CombGuidGenerator))]
                public Guid Id { get; set; }
        
                [BsonElement("Note")]
                public string Text { get; set; }
        
                [BsonElement("Date")]
                public DateTime Date {
                    get { return date.ToLocalTime(); }
                    set { date = value;}
                }
            }
        }

### Добавление уровня доступа к данным
Очень важно установить создать средства доступа к MongoDB для извлечения и сохранения заметок. Уровень доступа к данным будет использовать модель заметок и будет привязан к HomeController позднее.

1. Щелкните правой кнопкой мыши проект **mongoNotes** в обозревателе решений и выберите **Добавить -> Новая папка**. Назовите папку **DAL**.

1. Щелкните правой кнопкой мыши пункт **DAL** в обозревателе решений и выберите **Добавить -> Класс**. Задайте для этого нового класса имя *Dal.cs*.

1. Замените автоматически сгенерированный код для этого класса следующим кодом:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using mongoNotes.Models;
        using MongoDB.Driver;
        using System.Configuration;

        namespace mongoNotes
        {
            public class Dal : IDisposable
            {
                private MongoServer mongoServer = null;
                private bool disposed = false;
        
                private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
                MongoUrl url;
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                    url = new MongoUrl(connectionString);
                }
           
                public List<Note> GetAllNotes()
                {
                    try
                    {
                        MongoCollection<Note> collection = GetNotesCollection();
                        return collection.FindAll().ToList<Note>();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<Note>();
                    }
                }
        
                // Creates a Note and inserts it into the collection in MongoDB.
                public void CreateNote(Note note)
                {
                    MongoCollection<Note> collection = getNotesCollectionForEdit();
                    try
                    {
                        collection.Insert(note);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private MongoCollection<Note> GetNotesCollection()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
                    return noteCollection;
                }
        
                private MongoCollection<Note> getNotesCollectionForEdit()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
                    return notesCollection;
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

1. Обратите внимание на следующий код выше:
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
Здесь вы получаете доступ к переменной среды, которая будет настроена позже. При наличии локального экземпляра mongo, используемого для целей разработки, может потребоваться временно установить это значение равным "localhost".
  
  Также задайте имя базы данных. В частности, присвойте значение **dbName** имени, введенному при подготовке надстройки MongoLab.

1. Наконец, проверьте следующий код в **GetNotesCollection()**:  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  Здесь не надо ничего менять, только помните, что это подобно получению объекта MongoCollection для выполнения вставок, обновлений и запросов, таких как ниже в **GetAllNotes()**:

        collection.FindAll().ToList<Note>();

Дополнительные сведения об использовании драйвера MongoDB для C# см. в [кратком руководстве по драйверу CSharp](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "Краткое руководство по драйверу CSharp") на сайте mongodb.org.

### Добавление представления создания
Теперь добавим представление для создания заметки.

1. Щелкните правой кнопкой мыши запись **Представления -> Домашняя страница** в обозревателе решений и выберите **Добавить -> Представление**. Задайте для представления имя **Create** и нажмите кнопку **Добавить**

1. Замените автоматически сгенерированный код для этого представления (**Create.cshtml**) следующим кодом:

        @model mongoNotes.Models.Note
        
        <script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
        
        @using (Html.BeginForm("Create", "Home")) {
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>New Note</legend>
                <h3>New Note</h3>       
                <div class="editor-label">
                    @Html.LabelFor(model => model.Text)
                </div>
                <div class="editor-field">
                    @Html.EditorFor(model => model.Text)
                </div>
               <p>
                    <input type="submit" value="Create" />
               </p>
           </fieldset>
        }

### Изменение файла index.cshtml
Затем добавьте простой макет для просмотра и создания заметок в веб-приложении.

1. Откройте файл **Index.cshtml** в меню **Представления -> Домашняя страница** и замените его содержимое следующим кодом:  

        @model IEnumerable<mongoNotes.Models.Note>
        
        @{
            ViewBag.Title = "Notes";
        }
        
        <h2>My Notes</h2>

        <table border="1">
            <tr>
                <th>Date</th>
                <th>Note Text</th>      
            </tr>
        
        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Date)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Text)
                </td>       
            </tr>
        }
        
        </table>
        <div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### Изменение файла HomeController.cs
Наконец, контроллер HomeController должен создать экземпляр уровня доступа к данным и применить его к представлениям.

1. Откройте файл **HomeController.cs** в разделе **Контроллеры** в обозревателе решений и замените его содержимое следующим кодом:  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using System.Web.Mvc;
        using mongoNotes.Models;
        using System.Configuration;
        
        namespace mongoNotes.Controllers
        {
            public class HomeController : Controller, IDisposable
            {
                private Dal dal = new Dal();
                private bool disposed = false;
                //
                // GET: /Task/
        
                public ActionResult Index()
                {
                    return View(dal.GetAllNotes());
                }
        
                //
                // GET: /Task/Create
        
                public ActionResult Create()
                {
                    return View();
                }
        
                //
                // POST: /Task/Create
        
                [HttpPost]
                public ActionResult Create(Note note)
                {
                    try
                    {
                        dal.CreateNote(note);
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
    
<a name="deploy"></a>
## Развертывание приложения

После разработки приложения пришло время создать веб-приложение в службе приложений Azure для его размещения и настройки, а также развертывания кода. Центральной частью этого раздела является использование строки подключения MongoDB (URI). Вы собираетесь настроить переменную среды в своем веб-приложении с использованием этого универсального кода ресурса (URI), чтобы не включать URI в свой код. URI следует рассматривать как конфиденциальную информацию, так как он содержит учетные данные для подключения к вашей базе данных.

### Создание веб-приложения и получение файла параметров публикации
Создать веб-приложение в службе приложений Azure очень просто, потому что Azure автоматически создает профиль публикации для Visual Studio.

1. На портале Azure нажмите кнопку **Создать**.  
	![Создать][button-new]

1. Последовательно выберите пункты **Среда выполнения приложений > Веб-приложение > Быстрое создание**.  
	<!-- ![CreateWebApp][screen-mongolab-newwebsite] -->

1. Введите префикс URL-адреса. Выберите предпочтительное имя, но помните, что оно должно быть уникальным (скорее всего, имя "mongoNotes" не будет доступно).

1. Щелкните **Создать веб-приложение**.

1. По завершении создания веб-приложения щелкните его имя в списке веб-приложений. Откроется панель мониторинга веб-приложения.  
	![WebAppDashboard][screen-mongolab-websitedashboard]

1. Щелкните **Загрузить профиль публикации** в разделе **Сводка** и сохраните PUBLISHSETTINGS-файл в любом каталоге.  
![DownloadPublishProfile][button-website-downloadpublishprofile]

Вы также можете настроить веб-приложение непосредственно из Visual Studio. Когда вы связываете учетную запись Azure с Visual Studio, следуйте подсказкам, которые помогут вам настроить веб-приложение. После этого вы сможете просто в обозревателе решений щелкнуть правой кнопкой мыши имя проекта и выполнить развертывание в Azure. Вам все же необходимо настроить строку подключения MongoLab, как описано далее.

### Получение строки подключения к MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

### Добавление строки подключения в число переменных среды веб-приложения

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

### Публикация веб-приложения
1. В Visual Studio щелкните правой кнопкой мыши проект **mongoNotes** в обозревателе решений и выберите **Опубликовать**. Появится диалоговое окно публикации:  
	<!-- ![Publish][dialog-mongolab-vspublish] -->

1. Нажмите кнопку **Импорт** и выберите PUBLISHSETTINGS-файл в выбранном каталоге скачивания. Этот файл автоматически заполняет значения в диалоговом окне публикации.

1. Щелкните **Проверить подключение** для проверки файла.

1. После успешного завершения проверки щелкните **Опубликовать**. После завершения публикации откроется новая вкладка браузера с вашим веб-приложением.

1. Введите текст заметки, нажмите кнопку **Создать** и изучите результаты!  
	![HelloMongoAzure][screen-mongolab-sampleapp]

<a name="manage"></a>
## Управление базой данных

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

Поздравляем! Вы только что запустили приложение ASP.NET на C# на основе размещенной в MongoLab базы данных MongoDB! Теперь, когда база данных MongoLab создана, вы можете обращаться по адресу [support@mongolab.com](mailto:support@mongolab.com) по любым вопросам, связанным с вашей базой данных, а также для получения справки по MongoDB или непосредственно по соответствующему драйверу для C#. Удачи!

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[screen-mongolab-sampleapp]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage


<!---HONumber=54--> 