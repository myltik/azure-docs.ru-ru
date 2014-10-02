<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Build a web application with ASP.NET MVC using DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="hawong"></tags>

# <a name="_Toc395809351">Создание веб-приложения ASP.NET MVC с использованием DocumentDB</a>

# <a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Обзор</a>

## <a name="_Toc395637759">Сценарий</a>

Чтобы показать, как клиенты могут эффективно использовать Azure DocumentDB для
хранения и запросов документов JSON, в настоящем документе приведена пошаговая
инструкция от начала до конца для построения веб-приложения "список дел", используя Azure Document
DB.

В этом пошаговом руководстве показан пример использования служб DocumentDB, предоставляемых
Azure, для хранения и доступа к данным из веб-приложения ASP.NET MVC
, размещенного в Azure. Предполагается, что у вас имеется некоторый опыт использования
ASP.NET MVC и веб-сайтов Azure.

Вы узнаете:

1. Создание и подготовка учетной записи DocumentDB

2. Создание приложения ASP.NET MVC

3. Подключение и использование Azure DocumentDB из веб-приложения

4. Развертывание веб-приложения на веб-сайтах Azure

В ходе учебного курса вы создадите простое веб-приложение
для управления задачами, позволяющее создавать, извлекать и
выполнять задачи. Задачи будут храниться в виде документов JSON в Azure
DocumentDB.

![Alt text][]

# <a name="_Toc395637760">Предварительные требования</a>

Перед выполнением инструкций, приведенных в этой статье, следует убедиться,
что установлены следующие компоненты:

Git для Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (или [Visual Studio Express][], бесплатная
версия)

Пакет Azure SDK для .NET версии 2.3 или выше, доступно в
[установщике веб-платформы Майкрософт][]

Все снимки экранов в данном документе сделаны с использованием
Visual Studio 2013 с установленным обновлением 3 и пакетом Azure SDK для .NET версии
2.4. Если ваша система сконфигурирована с различными версиями программ, то вполне вероятно,
что ваши экраны и опции не будет соответствовать полностью, но если Вы выполните все вышеуказанные требования,
то это решение должно работать.

# <a name="_Toc395637761">Создание учетной записи базы данных DocumentDB</a>

Для подготовки учетной записи базы данных DocumentDB в Azure, откройте
портал управления Azure и, либо щелкните плитку "Коллекция Azure" на
домашней странице, либо нажмите "+" в левом нижнем углу экрана.

![Alt text][1]

Откроется коллекция Azure, где можно будет выбрать из множества
доступных служб Azure. В коллекции выберите "Данные, хранение и
резервное копирование" из списка категорий.

![Alt text][2]

Здесь, выберите параметр для Azure DocumentDB

![Alt text][3]

Затем нажмите "Создать" в нижней части экрана

![Alt text][4]

Откроется выноска "Новая DocumentDB", где можно будет задать
имя, регион, масштаб, группу ресурсов и прочие настройки новой
учетной записи.

![Alt text][5]

Как только вы закончите ввод значений для вашей учетной записи, нажмите кнопку "Создать" и начнется процесс подготовки создания учетной записи базы данных.
Когда процесс подготовки будет завершен, вы уведите уведомление,
которое появится в области уведомлений портала, а плитка на
экране запуска (если вы выбрали ее для создания) изменится, чтобы отобразить
выполненное действие.

![Alt text][6]

После завершения подготовки при нажатии плитки DocumentDB на
экране запуска появится главная выноска для этой вновь созданной
DocumentDB.

![Alt text][7]
![Alt text][8]

При помощи кнопки "Ключи", получите доступ к URL вашей конечной точки и первичный ключ,
скопируйте их в буфер обмена и держите их под рукой, так как мы будем использовать
эти значения в веб-приложении, которое создадим в последующем.

Теперь мы рассмотрим создание нового приложения ASP.NET MVC
с нуля. Для справки готовое решение можно загрузить по
адресу [здесь].

# <a name="_Toc395637762">Создание нового приложения ASP.NET MVC</a>

В Visual Studio выберите Файл – Новый проект и выберите вариант
создания нового веб-приложения ASP.NET MVC.

![Alt text][9]

Выберите место для создания нового проекта и нажмите Ok.

![Alt text][10]

Если вы планируете разместить ваше приложение в Azure, установите флажок "Разместить в облаке", расположенный в правом нижнем углу. Мы выбрали размещение в облаке, запуск приложения, размещенного на веб-сайтах Azure. При выборе
этой опции будет осуществлена предварительная подготовка веб-сайтов Azure,
что позволит облегчить развертывание готового рабочего приложения.

Выберите OK, Visual Studio создаст пустое приложение с
шаблоном ASP.NET MVC. Могут появляться дополнительные экраны с приглашением
войти в учетную запись Azure счета и предоставить некоторые сведения для вашего нового
веб-сайта. Введите все эти сведения для Azure и продолжите.

После того, как Visual Studio закончит создание обычного приложения MVC,
у вас будет пустое приложение ASP.NET, которое можно запустить локально.

Мы не станем его запускать локально, потому что я уверен, что мы все видели приложение ASP.NET "Hello World". Пойдем непосредственно к добавлению DocumentDB в этом проекте и созданию приложения.

# <a name="_Toc395637763">Настройка приложения ASP.NET MVC</a>

### 

### <a name="_Toc395637764">Добавление модели</a>

Давайте начнем с буквы **M** из аббревиатуры MVC, с модели (Model). В Обозревателе
решений щелкните правой кнопкой мыши папку *Модели* и выберите **Добавить**, а затем щелкните
**Класс.**

![Alt text][11]

Назовите новый класс **Элемент** и добавьте следующий код в этот
новый класс

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public int ID { get; set; }
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Все данные в DocumentDB передаются по сети и хранятся в формате JSON. Для того,
чтобы управлять сериализацией/десериализацией объектов в JSON.NET, вы
можете использовать атрибут JsonProperty как указано выше, который гарантирует, что имена наших
свойств будут соответствовать соглашению JSON по именованию свойств в нижнем регистре.
Вам **не придется** делать это вручную.

### <a name="_Toc395637765">Добавление контролера</a>

Это касается буквы M, теперь создадим **C** из аббревиатуры MVC, класс
контроллера (Contoller).
 В **обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* и
выберите **Добавить**, а затем щелкните **Контроллер**.

![Alt text][12]

![Alt text][13]

В диалоговом окне **Добавление Scaffold** выберите **Пустой контроллер MVC 5.**
Щелкните **Добавить.**

![Alt text][14]

Назовите новый контроллер **ItemController.**

Visual Studio добавит контроллер ItemController в обозреватель
решений, который должен иметь вид, схожий с приведенным ниже снимком экрана.

![Alt text][15]

### <a name="_Toc395637766">Добавление представлений</a>

И наконец, создадим букву **V** из аббревиатуры MVC, представление (View).

#### Добавление представления индекса элементов

Разверните папку ***Представления*** в Обозревателе решений и найдите пустой элемент папки
(empty), который был создан Visual Studio
при добавлении *ItemController* ранее. Щелкните правой кнопкой мыши на ***Элемент***
 и выберите "Добавить новое представление".

![Alt text][16]

В диалоговом окне "Добавление представления". Вызовите "***Индекс***" представления, используйте шаблон
***Список***, выберите ***Элемент (Todo.Models)***, который был создан ранее в этом уроке
в виде класса и выберите ***\_Layout.cshtml***
в решении в качестве макета страницы.

![Alt text][17]

После установки этих значений, нажмите "Добавить", Visual Studio создаст
представление. Visual Studio создаст шаблон представления. По завершении процесса
создания, откроется файл cshtml. Можно закрыть этот документ
в Visual Studio, мы вернемся к нему позже.

#### Добавление нового элемента представления

Аналогичным образом, создайте новое представление для создания новых элементов
как указано в примере, показанном ниже;

![Alt text][18]

#### Добавление редактирования элемента представления

# <a name="_Toc395888515"></a>

И, наконец, добавить последнее представление для редактирования элементов так же, как
ранее;

![Alt text][19]

Как только все будет готово, закройте документы cshtml в Visual Studio, так как мы
вернемся к этим представлениям позже.

</h1>
# <a name="_Toc395637767">Добавление в проект DocumentDB</a>

Теперь займемся большей частью подготовки ASP.NET MVC, которая понадобится для данного
решения. Теперь давайте перейдем к действительной цели этого урока, добавив Azure DocumentDB в наше веб-приложение.

</h1>
## <a name="_Toc395637768">Установка пакета NuGet для DocumentDB</a>

Пакет DocumentDB .NET SDK упакован и распространяется в виде пакета NuGet.
Воспользуйтесь диспетчером пакетов NuGet в Visual Studio (его можно вызвать,
 щелкнув правой кнопкой мыши в проекте и выбрав пункт "Управление пакетами NuGet"

![Alt text][20]

Выполните в Интернете поиск значения "Azure DocumentDB" и установите пакет. При этом
будет загружен и установлен пакет DocumentDB, а также все
зависимости, такие как Newtonsoft.Json.

После установки ваше решение Visual Studio должно иметь следующий вид
с добавленными двумя новыми ссылками;

![Alt text][21]

</h1>
## <a name="_Toc395637769">Подключение DocumentDB</a>

### <a name="_Toc395637770">Вывод списка незавершенных элементов</a>

Откройте **ItemController** и удалите весь код класса
(но оставьте сам класс), автоматически добавленный Visual Studio. Мы постепенно воссоздадим его с использованием DocumentDB.

Добавьте следующий фрагмент кода в пустой класс ItemController
;

    public async Task<ActionResult> Index() 
    {
        var items = await repo.GetIncompleteItems();
        return View(items);
    }

Будет использован класс "псевдо-репозитория" для DocumentDB, который является
обычным вспомогательным классом, который содержит весь специфичный для DocumentDB код. Для
целей данного пошагового руководства, мы не собираемся реализовать
полный уровень доступа к данным с инъекциями зависимостей с помощью шаблона хранилища,
как вы могли бы сделать, если бы вы разрабатывали настоящее приложение. Для целей
данного пошагового руководства мы всего лишь собираемся собрать всю
логику доступа к данным в один проект, чтобы не усложнять вещи.

Добавьте новый класс в проект и назовите его **DocumentDBRepository.**
 И замените код в файле класса следующим;

    public static class DocumentDBRepository
    {
        public static async Task<List<Item>> GetIncompleteItems()
        {
            return await Task<List<Item>>.Run( () => 
                Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => !d.Completed)
                    .AsEnumerable()
                    .ToList<Item>());
        }
    } 

#### 

Уделите некоторое время добавлению соответствующих операторов using для разрешения
ссылок. Все ссылки будет легко разрешить в Visual Studio в случае,
если пакет NuGet был успешно установлен. Ссылка на
*CreateDocumentQuery* при разрешении потребует ручного добавления
с использованием директивы;

    using Microsoft.Azure.Documents.Linq; 

Нам нужно добавить в наш псевдорепозиторий класс
, чтобы все работало как надо. Добавьте нижеприведенный код
в класс репозитория;


        private static Database database;
        private static Database Database
        {
            get
            {
                if (database == null)
                {
                    ReadOrCreateDatabase().Wait();
                }
                
                return database;
            }
        }

        private static DocumentCollection collection;
        private static DocumentCollection Collection
        {
            get
            {
                if (collection == null)
                {
                    ReadOrCreateCollection(Database.SelfLink).Wait();
                }
                
                return collection;
            }
        }

        private static string databaseId;
        private static String DatabaseId
        {
            get
            {
                if (string.IsNullOrEmpty(databaseId))
                {
                    databaseId = ConfigurationManager.AppSettings["database"];
                }
            
                return databaseId;
            }
        }
      
        private static string collectionId;
        private static String CollectionId
        {
            get
            {
                if (string.IsNullOrEmpty(collectionId))
                {
                    collectionId = ConfigurationManager.AppSettings["collection"];
                }
            
                return collectionId;
            }
        }

        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
                    String endpoint = ConfigurationManager.GetSetting("endpoint")
                    string authKey = ConfigurationManager.GetSetting("authKey");
                    Uri endpointUri = new Uri(endpoint);
                    client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }

        private static async Task ReadOrCreateCollection(string databaseLink)
        {
            var collections = Client.CreateDocumentCollectionQuery(databaseLink)
                              .Where(col => col.Id == CollectionId).ToArray();
            
            if (collections.Any())
            {
                collection = collections.First();
            }
            else
            {
                collection = await Client.CreateDocumentCollectionAsync(databaseLink, 
                    new DocumentCollection { Id = CollectionId });
            }
        }
     
        private static async Task ReadOrCreateDatabase()
        {
            var databases = Client.CreateDatabaseQuery()
                            .Where(db => db.Id == DatabaseId).ToArray();
            
            if (databases.Any())
            {
                database = databases.First();
            }
            else
            {
                Database database = new Database { Id = DatabaseId };   
                database = await Client.CreateDatabaseAsync(database);
            }
        }

</h1>
#### 

Пир первом доступе к методу GetIncompleteItems() он вызывается на
клиентской стороне, устанавливается соединение с Azure DocumentDB. При первом
вызове в GetIncompleteItems() также будет произведена попытка чтения или создания
базы данных и коллекции документов для этого приложения. Это нужно выполнить
один раз, после этого вы сможете просто кэшировать свойства SelfLink для
обоих ресурсов и использовать их в приложении.

Если вы подключитесь к DocumentDB позже из того же приложения,
то каждый раз будет повторно использоваться тот же экземпляр клиента, база данных и коллекция
вместо создания нового экземпляра.

Откройте файл **web.config** и добавьте следующие строки в раздел
<appsettings\>;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>

</h4>
На данном этапе ваше решение должно компилироваться без каких-либо ошибок.

Чтобы запустить приложение сейчас, нужно перейти к главному контроллеру
и представлению индекса в этом контроллере. Это поведение по умолчанию для шаблона проекта
MVC мы выбрали в начале, но нам нужно кое-что другое! Давайте
изменим маршрутизацию в данном приложении MVC, чтобы изменить это поведение.

Откройте файл ***RouteConfig.cs*** в папке App\_Start folder, найдите строку,
начинающуюся с "defaults:", и измените ее следующим образом;

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Теперь MVC ASP.NET понимает, что если вы не указали значение в URL, чтобы контролировать поведение маршрутизации, то вместо "Главный" использовать "Элемент" в качестве контроллера и пользовательский индекс в качестве представления.
Теперь, если вы запустите приложение, оно будет вызывать в **ItemController** и возвращать результаты метода **GetIncompleteItems** в представление Views\\Item\\Index.
Если запустить этот проект сейчас, вы должны увидеть примерное такую картину;

![Alt text][22]

### <a name="_Toc395637771">Добавление элементов</a>

Давайте теперь добавим некоторые элементы в нашу базу данных,
 чтобы можно было что-то увидеть кроме пустой сетки.

Мы уже получили представление для создания и кнопку на представлении индекса,
которые используются пользователем для создания представления. Давайте добавим код
в контроллере и репозитории для сохранения записи в DocumentDB.

Откройте ***ItemController.cs*** и добавьте следующий фрагмент кода
 благодаря ему ASP.NET MVC знает, что делать для действия "Создать", в этом случае это
просто отрисовка связанного HTML представления Create.cs, созданного ранее.

    public ActionResult Create()
    { 
          return View(); 
    }

Теперь нам нужно добавить еще немного кода в этом контроллере, который будет принимать
данные из представления.

Продолжаем и добавляем следующий блок кода, который указывает ASP.NET MVC, что
делать с формой POST для этого контроллера.

    [HttpPost]  
    public async Task<ActionResult> Create(Item item)  
    {
        if (ModelState.IsValid)  
        {  
            await repo.CreateDocument(item);  
            return RedirectToAction("Index");  
        }   
        return View(item);   
    }

Теперь контроллер элементов передает элемент из формы в метод
CreateDocument нашего класса псевдо репозитория, так что добавьте
следующий метод в класс DocumentDBRepository.

    public static async Task<Document> CreateDocument(dynamic item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item); 
    }

Этот метод просто принимает любой объект, переданный ему, и сохраняет его в
DocumentDB.

На этом мы завершаем написание кода, необходимого для добавления новых элементов в нашу базу данных.

### <a name="_Toc395637772">Редактирование элементов</a>

Нам нужно сделать еще одну последнюю вещь, это реализация возможности редактирования
элементов в базе данных и, чтобы отметить их завершенными, так же как мы отмечаем выполненные
задачи. Как с добавлением, представление для редактирования уже было добавлено, так что мы
снова просто должны добавить некоторый дополнительный код для нашего контроллера и класса
DocumentDBRepository.

Добавьте следующие методы в класс ItemController;

    public async Task<ActionResult> Edit(string id)    
    {  
        if (id == null)  
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);   
        }

        Item item = await repo.GetDocument(id);
        if (item == null)
        {       
            return HttpNotFound();
        }
        
        return View(item);
    }

    [HttpPost] 
    public async Task<ActionResult> Edit(Item item)  
    {
        if (ModelState.IsValid)
        {
             await repo.UpdateDocument(item);
             return RedirectToAction("Index");
        }
        
        return View(item); 
    }

Первый метод обрабатывает команду GET HTTP, которая формируется, когда пользователь
нажимает на ссылку "Изменить" в представлении индекса. Этот метод извлекает документ из
DocumentDB и передает его в режиме редактирования.

### 

Представление редактирования затем формирует команду POST HTTP в наш IndexController и
второй метод, который мы добавили, который обрабатывает это прохождение обновленного объекта
в DocumentDB, чтобы быть сохраненным в базе данных.

Добавьте следующие методы в класс DocumentDBRepository;

    public static async Task<Item> GetDocument(string id)
    {
        return await Task<Item>.Run(() =>
            Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => d.ID == id)
                .AsEnumerable()
                .FirstOrDefault());
    }

    public static async Task<Document> UpdateDocument(Item item)
    {
        var doc = Client.CreateDocumentQuery<Document>(Collection.DocumentsLink)
                    .Where(d => d.Id == item.ID)
                    .AsEnumerable().FirstOrDefault();
        
        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

Первый из этих двух методов извлекает записи из DocumentDB которые
передаются обратно в ItemController и затем в представление редактирования.

Второй из двух только что добавленных методов заменяет документ в
 DocumentDB с версией документа, переданного из
ItemController.

Вот и все, что мы понадобится для запуска нашего приложение,
отображения списка невыполненных элементов, добавления новых элементов и, наконец, редактирования элементов.

</h3>
# <a name="_Toc395637773">Локальный запуск приложения</a>

### 

Для тестирования приложения на локальном компьютере, нажмите F5 в Visual Studio,
он должен создать приложение и запустить браузер с пустой
сеткой на странице, которую мы видели мы видели раньше:

![Alt text][23]

Используйте предоставленные поля Имя элемента и Имя категории для ввода
данных, а затем нажмите ссылку **Создать новый элемент** и введите некоторые
значения. Оставьте флажок "Завершено" сброшенным, в противном случае новый элемент будет
добавлен ​​в завершенном состоянии и не появится на начальном списке.

![Alt text][24]

Если вы нажали "Создать", вы будете перенаправлены обратно на страницу индекса и,
надеюсь, ваш элемент отображается в списке.

![Alt text][25]

Не стесняйтесь добавить еще несколько элементов в список задач.

2.Нажмите на "Изменить" рядом с элементом в списке, и вы будете переведены
к представлению "Правка", где можно обновить любое свойство вашего объекта,
в том числе с флажком "Завершено". Это пометит элемент завершенным
 и удалит его из списка незавершенных задач.

![Alt text][26]

3. Для завершения задачи, просто установите флажок, а затем нажмите
**"Сохранить".** Вы будете перенаправлены обратно на страницу списка,
где элемент больше не будет появляться в списке.

</h3>
# <a name="_Toc395637774">Разверните приложение на веб-сайтах Azure</a>

### 

Теперь, когда у вас есть законченное приложение и оно работает правильно в
DocumentDB, мы собираемся развернуть его на веб-сайтах Azure.

Если вы выбрали "Разместить в облаке", когда мы создали пустой проект ASP.NET
MVC, то Visual Studio все упростит и сделает большую часть
работы за нас. Нажмите правой кнопкой мыши
на проекте в обозревателе решений (убедитесь, что он не запущен
локально) и выберите пункт "Опубликовать".

![Alt text][27]

Все уже должно быть настроено в соответствии с вашими учетными данными,
на самом деле сайт уже создан в Azure и указан в
"URL назначения",, все что вам нужно, это нажать **"Опубликовать".**

![Alt text][28]

Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения
 и запустит браузер, где можно увидеть ваше творение,
запущенное в Azure!

</h3>
# <a name="_Toc395637775">Заключение</a>

### 

Поздравляем! Вы только что создали свое первое приложение ASP.NET MVC с использованием Azure DocumentDB и опубликовали его на веб-сайтах Azure. Исходный код завершенного учебного приложения можно загрузить по этой ссылке.

[здесь] (<http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409>)

  [Alt text]: ./media/documentdb-dotnet-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [установщике веб-платформы Майкрософт]: http://www.microsoft.com/web/downloads/platform.aspx
  [1]: ./media/documentdb-dotnet-application/image2.png
  [2]: ./media/documentdb-dotnet-application/image3.png
  [3]: ./media/documentdb-dotnet-application/image4.png
  [4]: ./media/documentdb-dotnet-application/image5.png
  [5]: ./media/documentdb-dotnet-application/image6.png
  [6]: ./media/documentdb-dotnet-application/image7.png
  [7]: ./media/documentdb-dotnet-application/image8.png
  [8]: ./media/documentdb-dotnet-application/image9.png
  [9]: ./media/documentdb-dotnet-application/image10.png
  [10]: ./media/documentdb-dotnet-application/image11.png
  [11]: ./media/documentdb-dotnet-application/image12.png
  [12]: ./media/documentdb-dotnet-application/image13.png
  [13]: ./media/documentdb-dotnet-application/image14.png
  [14]: ./media/documentdb-dotnet-application/image15.png
  [15]: ./media/documentdb-dotnet-application/image16.png
  [16]: ./media/documentdb-dotnet-application/image17.png
  [17]: ./media/documentdb-dotnet-application/image18.png
  [18]: ./media/documentdb-dotnet-application/image19.png
  [19]: ./media/documentdb-dotnet-application/image20.png
  [20]: ./media/documentdb-dotnet-application/image21.png
  [21]: ./media/documentdb-dotnet-application/image22.png
  [22]: ./media/documentdb-dotnet-application/image23.png
  [23]: ./media/documentdb-dotnet-application/image24.png
  [24]: ./media/documentdb-dotnet-application/image25.png
  [25]: ./media/documentdb-dotnet-application/image26.png
  [26]: ./media/documentdb-dotnet-application/image27.png
  [27]: ./media/documentdb-dotnet-application/image28.png
  [28]: ./media/documentdb-dotnet-application/image29.png
