<properties linkid="develop-net-tutorials-website-with-mongodb-mongolab" urlDisplayName="Website with MongoDB on MongoLab" pageTitle="Create a Website that uses MongoDB on MongoLab (.NET)" metaKeywords="" description="Learn how to create an Azure website that stores data in MongoDB hosted by MongoLab." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create a C# ASP.NET Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="eric@mongolab.com"></tags>

# Создание приложения ASP.NET на C# в Azure с MongoDB с использованием надстройки MongoLab

*Автор: Эрик Седор (Eric Sedor), MongoLab*

Приветствую, искатели приключений! Добро пожаловать в MongoDB как услуга. Изучив данный учебник, вы научитесь:

1.  [Подготавливать базу данных][Подготавливать базу данных] — надстройка [MongoLab][MongoLab], доступная в Магазине Azure, позволяет получать доступ к базе данных MongoDB, размещенной в облаке Azure, и управляется облачной платформой базы данных MongoLab.
2.  [Создавать приложение][Создавать приложение] — это будет простое приложение ASP.NET MVC на C# для создания заметок.
3.  [Развертывать приложение][Развертывать приложение] — объединяя вместе несколько обработчиков конфигурации, мы значительно упрощаем перемещение кода.
4.  [Управлять базой данных][Управлять базой данных] — наконец, мы покажем портал управления базами данных MongoLab, где можно без труда искать, визуализировать и изменять данные.

Если во время работы с этим учебником у вас возникнут какие-либо вопросы, обращайтесь с ними по адресу [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>].

## Быстрый запуск

Если у вас уже есть приложение и веб-сайт Azure, с которым вы собираетесь работать, или у вас есть опыт работы с Магазином Azure, используйте этот раздел в качестве краткого руководства. В противном случае изучите приведенный ниже раздел [Подготовка базы данных][Подготавливать базу данных].

1.  Перейдите на веб-сайт Магазина Azure.<br/>
    ![Магазин][Магазин]
2.  Купите надстройку MongoLab.<br/>
    ![MongoLab][1]
3.  Щелкните надстройку MongoLab в списке надстроек и выберите пункт **Сведения о подключении**.<br/>
    ![ConnectionInfoButton][ConnectionInfoButton]
4.  Скопируйте значение MONGOLAB\_URI в буфер обмена.<br/>
    ![ConnectionInfoScreen][ConnectionInfoScreen]<br/>
    **Этот универсальный код ресурса (URI) содержит имя пользователя и пароль к вашей базе данных. Считайте это конфиденциальной информацией и не передавайте ее.**
5.  Добавьте значение в список «Строки подключения», который можно найти в меню «Конфигурация» веб-приложения Azure:<br/>
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]
6.  В качестве **Имени** введите MONGOLAB\_URI.
7.  В качестве **Значение** вставьте строку подключения, полученную в предыдущем разделе.
8.  В раскрывающемся списке «Тип» выберите пункт **Настраиваемый** (вместо значения по умолчанию **SQLAzure**).
9.  В Visual Studio установите драйвер Mongo для C#. Для этого выберете **Средства -\> Диспетчер пакетов библиотеки -\> Консоль диспетчера пакетов**. В консоли PM введите команду **Install-Package mongocsharpdriver** и нажмите клавишу **ВВОД**.
10. Настройте обработчик в коде, чтобы получить универсальный код ресурса (URI) подключения MongoLab из переменной среды:

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

    Примечание. Azure добавляет префикс **CUSTOMCONNSTR\_** к первоначально объявленной строке подключения, поэтому в коде содержится ссылка на **CUSTOMCONNSTR\_MONGOLAB\_URI.**, а не на **MONGOLAB\_URI**.

Теперь перейдем к полному учебнику...

## <a name="provision"></a>Подготовка базы данных

[WACOM.INCLUDE [howto-provision-mongolab][howto-provision-mongolab]]

## <a name="create"></a>Создание приложения

В этом разделе мы рассмотрим создание проекта C# ASP.NET в Visual Studio и использование драйвера C# MongoDB для разработки простого приложения создания заметок. Вы сможете посетить свой веб-сайт, создать заметку и просмотреть все оставшиеся заметки.

Действия выполняются в Visual Studio Express 2012 for Web.

### Создание проекта

Для примера приложения используется шаблон Visual Studio. Убедитесь, что используется платформа .NET Framework 4.0.

1.  Выберите **Файл -\> Новый проект**. Появится диалоговое окно "Новый проект":<br/>
    ![NewProject][NewProject]
2.  Выберите **Установлено -\> Шаблоны -\> Visual C# -\> Интернет**.
3.  В раскрывающемся списке версий .NET выберите **.NET Framework 4**. (*Примечание. На данный момент Framework 4.5 не поддерживается.*)

    ![ProjectFramework][ProjectFramework]
4.  Выберите **Веб-приложение ASP.NET MVC 4**.
5.  В поле **Имя проекта** введите *mongoNotes*. Если вы выбрали другое имя, потребуется изменить код, используемый в учебнике.
6.  Нажмите кнопку **ОК**. Появится диалоговое окно "Шаблон проекта":<br/>
    ![ProjectTemplate][ProjectTemplate]
7.  Выберите **Интернет-приложение** и нажмите кнопку **OK**. Выполнится построение проекта.
8.  Выберите **Средства -\> Диспетчер пакетов библиотеки -\> Консоль диспетчера пакетов**. В консоли PM введите **Install-Package mongocsharpdriver** и нажмите клавишу **ВВОД**.
    <br/>![PMConsole][PMConsole]
    Драйвер MongoDB для C# будет интегрирован в проект, а в файл *packages.config* будет автоматически добавлена следующая строка:

        < package id="mongocsharpdriver" version="1.8" targetFramework="net40" / >

### Добавление модели заметки

Сначала создайте модель для заметок, содержащую просто дату и текст.

1.  Щелкните правой кнопкой мыши пункт **Модели** в обозревателе решений и выберите **Добавить -\> Класс**. Задайте для этого нового класса имя *Note.cs*.
2.  Замените автоматически сгенерированный код для этого класса следующим кодом:

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

1.  Щелкните правой кнопкой мыши проект **mongoNotes** в обозревателе решений и выберите **Добавить -\> Новая папка**. Назовите папку **DAL**.
2.  Щелкните правой кнопкой мыши пункт **DAL** в обозревателе решений и выберите **Добавить -\> Класс**. Задайте для этого нового класса имя *Dal.cs*.
3.  Замените автоматически сгенерированный код для этого класса следующим кодом:

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
                MongoUrl url = new MongoUrl(connectionString);
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
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

4.  Обратите внимание на следующий код выше:

        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  

    Здесь вы получаете доступ к переменной среды, которая будет настроена позже. При наличии локального экземпляра mongo, используемого для целей разработки, может потребоваться временно установить это значение равным "localhost".

    Также задайте имя базы данных. В частности, присвойте значение **dbName** имени, введенному при подготовке надстройки MongoLab.

1.  Наконец, проверьте следующий код в **GetNotesCollection()**:

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);

    Здесь не надо ничего менять, только помните, что это подобно получению объекта MongoCollection для выполнения вставок, обновлений и запросов, таких как ниже в **GetAllNotes()**:

        collection.FindAll().ToList<Note>();

Для получения дополнительной информации об использовании драйвера MongoDB для C# просмотрите [краткое руководство по драйверу CSharp][краткое руководство по драйверу CSharp] на веб-сайте mongodb.org.

### Добавление представления создания

Теперь добавим представление для создания заметки.

1.  Щелкните правой кнопкой мыши запись **Представления -\> Домашняя страница** в обозревателе решений и выберите **Добавить -\> Представление**. Задайте для представления имя **Create** и нажмите кнопку **Добавить**
2.  Замените автоматически сгенерированный код для этого представления (**Create.cshtml**) следующим кодом:

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

Затем добавьте простой макет для просмотра и создания заметок на веб-сайте.

1.  Откройте файл **Index.cshtml** в меню **Представления -\> Домашняя страница** и замените его содержимое следующим кодом:

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

1.  Откройте файл **HomeController.cs** в разделе **Контроллеры** в обозревателе решений и замените его содержимое следующим кодом:

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

## <a name="deploy"></a>Развертывание приложения

Завершив разработку приложения, следует создать веб-сайт Azure для его размещения, настроить этот веб-сайт и развернуть код. Центральной частью этого раздела является использование строки подключения MongoDB (URI). Вы настроите на своем веб-сайте переменную среды с этим универсальным кодом ресурса (URI), чтобы не включать его в используемый код. URI следует рассматривать как конфиденциальную информацию, так как он содержит учетные данные для подключения к базе данных.

### Создание нового веб-сайта и получение файла параметров публикации

Создать веб-сайт в Azure очень просто, потому что Azure автоматически создает профиль публикации для Visual Studio.

1.  На портале Azure нажмите кнопку **Создать**.<br/>
    ![Создать][Создать]
2.  Выберите **Среда выполнения приложений -\> Веб-сайт -\> Быстро создать**.<br/>
    ![CreateSite][CreateSite]
3.  Введите префикс URL-адреса. Выберите предпочтительное имя, но помните, что оно должно быть уникальным (скорее всего, имя "mongoNotes" не будет доступно).
4.  Щелкните **Создать веб-сайт**.
5.  После завершения создания веб-сайта щелкните его имя в списке веб-сайтов. Появится панель мониторинга веб-сайта.<br/>
    ![WebSiteDashboard][WebSiteDashboard]
6.  Щелкните **Загрузить профиль публикации** в разделе **сводка** и сохраните PUBLISHSETTINGS-файл в любом каталоге.<br/>
    ![DownloadPublishProfile][DownloadPublishProfile]

### Получение строки подключения к MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab][howto-get-connectioninfo-mongolab]]

### Добавление строки подключения в число переменных среды веб-сайта

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab][howto-save-connectioninfo-mongolab]]

### Публикация веб-сайта

1.  В Visual Studio щелкните правой кнопкой мыши проект **mongoNotes** в обозревателе решений и выберите **Опубликовать**. Появится диалоговое окно публикации:<br/>
    ![Опубликовать][Опубликовать]
2.  Нажмите кнопку **Импорт** и выберите PUBLISHSETTINGS-файл в выбранном каталоге скачивания. Этот файл автоматически заполняет значения в диалоговом окне публикации.
3.  Щелкните **Проверить подключение** для проверки файла.
4.  После успешного завершения проверки щелкните **Опубликовать**. После завершения публикации откроется новая вкладка браузера с созданным веб-сайтом.
5.  Введите текст заметки, щелкните **Создать** и просмотрите полученный результат.<br/>
    ![HelloMongoAzure][HelloMongoAzure]

## <a name="manage"></a>Управление базой данных

[WACOM.INCLUDE [howto-access-mongolab-ui][howto-access-mongolab-ui]]

Поздравляем! Вы только что запустили приложение ASP.NET на C# на основе размещенной в MongoLab базы данных MongoDB! Теперь, когда база данных MongoLab создана, вы можете обращаться по адресу [support@mongolab.com](mailto:support@mongolab.com) по любым вопросам, связанным с вашей базой данных, а также для получения справки по MongoDB или непосредственно по соответствующему драйверу для C#. Удачи!

  [Подготавливать базу данных]: #provision
  [MongoLab]: http://mongolab.com
  [Создавать приложение]: #create
  [Развертывать приложение]: #deploy
  [Управлять базой данных]: #manage
  [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>]: mailto:support@mongolab.com
  [Магазин]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-store.png
  [1]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/entry-mongolab.png
  [ConnectionInfoButton]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-websiteconnectionstring.png
  [howto-provision-mongolab]: ../includes/howto-provision-mongolab.md
  [NewProject]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-newproject.png
  [ProjectFramework]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-dotNet-Framework4-mongolab.png
  [ProjectTemplate]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-projecttemplate.png
  [PMConsole]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-csharp-pmconsole.png
  [краткое руководство по драйверу CSharp]: http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "краткое руководство по драйверу CSharp"
  [Создать]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-new.png
  [CreateSite]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-websitedashboard.png
  [DownloadPublishProfile]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-website-downloadpublishprofile.png
  [howto-get-connectioninfo-mongolab]: ../includes/howto-get-connectioninfo-mongolab.md
  [howto-save-connectioninfo-mongolab]: ../includes/howto-save-connectioninfo-mongolab.md
  [Опубликовать]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-vspublish.png
  [HelloMongoAzure]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-sampleapp.png
  [howto-access-mongolab-ui]: ../includes/howto-access-mongolab-ui.md
