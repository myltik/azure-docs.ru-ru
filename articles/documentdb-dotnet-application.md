<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Создание веб-приложения ASP.NET MVC с использованием DocumentDB | Azure" description="Узнайте, как использовать DocumentDB для создания веб-приложения списка дел. Данные вы будете хранить в веб-приложении ASP.NET MVC, размещенном в Azure, из которого вы также будете их использовать.."  metaKeywords="NoSQL, DocumentDB, database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Создание веб-приложения ASP.NET MVC с использованием DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Обзор</a>
==============================================================================================

<a name="_Toc395637759">Сценарий</a>
------------------------------------

Чтобы показать, как клиенты могут эффективно использовать Azure DocumentDB для хранения и запросов документов JSON, в настоящем документе приведена пошаговая инструкция от начала до конца для построения веб-приложения "список дел", используя Azure DocumentDB.

В этом пошаговом руководстве показан пример использования служб DocumentDB, предоставляемых Azure, для хранения и доступа к данным из веб-приложения ASP.NET MVC, размещенного в Azure. Предполагается, что у вас имеется некоторый опыт использования ASP.NET MVC и веб-сайтов Azure.

Вы узнаете:

1\. Создание и подготовка учетной записи DocumentDB

2\. Создание приложения ASP.NET MVC

3\. Подключение и использование Azure DocumentDB из веб-приложения

4\. Развертывание веб-приложения на веб-сайтах Azure

В ходе учебного курса вы создадите простое веб-приложение для управления задачами, позволяющее создавать, извлекать и выполнять задачи. Задачи будут храниться в виде документов JSON в Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

Git для Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (или [Visual Studio Express,][] бесплатная версия)

Пакет Azure SDK для .NET версии 2.3 или выше, доступный в [установщике веб-платформы Майкрософт][]

Все снимки экранов в данном документе сделаны с использованием Visual Studio 2013 с установленным обновлением 3 и пакетом Azure SDK для .NET версии 2.4. Если ваша система сконфигурирована с различными версиями программ, то вполне вероятно, что ваши экраны и опции не будет соответствовать полностью, но если Вы выполните все вышеуказанные требования, то это решение должно работать.

<a name="_Toc395637761">Создание учетной записи базы данных DocumentDB</a>
================================================================

Для подготовки учетной записи базы данных DocumentDB в Azure откройте портал управления Azure и либо щелкните плитку "Коллекция Azure" на домашней странице, либо нажмите "+" в левом нижнем углу экрана.

![Alt text](./media/documentdb-dotnet-application/image2.png)


Откроется коллекция Azure, где можно будет выбрать из множества доступных служб Azure. В Коллекции выберите "Данные, хранение и резервное копирование" в списке категорий.

![Alt text](./media/documentdb-dotnet-application/image3.png)

Здесь, выберите параметр для Azure DocumentDB

![Alt text](./media/documentdb-dotnet-application/image4.png)


Затем нажмите "Создать" в нижней части экрана

![Alt text](./media/documentdb-dotnet-application/image5.png)

Откроется колонка "Создать DocumentDB", где можно будет задать имя, регион, масштаб, группу ресурсов и прочие параметры новой учетной записи.

![Alt text](./media/documentdb-dotnet-application/image6.png)


По завершении ввода значений для вашей учетной записи нажмите кнопку "Создать", после чего запустится процесс создания учетной записи базы данных.
После выполнения процесса подготовки вы увидите уведомление в области уведомлений портала, а плитка на начальном экране (если было выбрано ее создание) изменит внешний вид, подтверждая выполненные действия.

![Alt text](./media/documentdb-dotnet-application/image7.png)


После завершения подготовки нажатие плитки DocumentDB на начальном экране вызовет основную колонку новой созданной учетной записи DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


При помощи кнопки "Ключи" получите свой URL-адрес конечной точки и первичный ключ, скопируйте их в буфер обмена и держите под рукой, так как они будут использоваться в веб-приложении, которое будет создано в дальнейшем.

Теперь мы рассмотрим создание нового приложения ASP.NET MVC с нуля. Для справки готовое решение можно загрузить [отсюда].

<a name="_Toc395637762">Создание нового приложения ASP.NET MVC</a>
================================================================

В Visual Studio выберите "Файл - Создать проект" и выберите вариант создания нового веб-приложения ASP.NET MVC.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Выберите место для создания проекта и его название (мы используем "todo") и щелкните ОК.

![Alt text](./media/documentdb-dotnet-application/image11.png)


Если вы планируете разместить ваше приложение в Azure, установите флажок "Разместить в облаке", расположенный в правом нижнем углу. Мы выбрали размещение в облаке, запуск приложения, размещенного на веб-сайтах Azure. При выборе этой опции будет осуществлена предварительная подготовка веб-сайта Azure, что упростит развертывание готового рабочего приложения. Если вы не хотите заранее настраивать Azure или предпочитаете разместить приложение в другом месте, просто снимите флажок с параметра "Разместить в облаке".

Выберите OK, и Visual Studio создаст пустой шаблон ASP.NET MVC. Если было выбрано размещение в облаке, вы увидите по крайней мере еще один экран, на котором будет предложено войти в учетную запись Azure и ввести некоторые значения для нового веб-сайта. Введите все эти сведения для Azure и продолжите. 

"Сервер базы данных" в этом случае не выбран, так как здесь мы не используем сервер базы данных SQL Azure. Позже мы создадим новую учетную запись Azure DocumentDB на портале управления. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

После того как Visual Studio закончит создание шаблонного приложения MVC, у вас будет пустое приложение ASP.NET, которое можно запустить локально.

Мы не станем его запускать локально, потому что я уверен, что мы все видели приложение ASP.NET "Hello World". Пойдем непосредственно к добавлению DocumentDB в этом проекте и созданию приложения.

</h1>
<a name="_Toc395637767">Добавление в проект DocumentDB</a>
=============================================================

Это - основная часть подготовки ASP.NET MVC, необходимая для данного решения. Теперь давайте перейдем к действительной цели этого урока, добавив Azure DocumentDB в наше веб-приложение.

### 

### <a name="_Toc395637764">Установка пакета NuGet</a>

DocumentDB .NET SDK комплектуется и распространяется как пакет NuGet. Воспользуйтесь диспетчером пакетов NuGet в Visual Studio (его можно вызвать, щелкнув правой кнопкой мыши на проекте и выбрав пункт "Управление пакетами NuGet")

![Alt text](./media/documentdb-dotnet-application/image21.png)

Выполните в Интернете поиск значения "Azure DocumentDB" и установите пакет. При этом будет загружен и установлен пакет DocumentDB, а также все зависимости, такие как Newtonsoft.Json.

**ВНИМАНИЕ:** поскольку служба все еще работает в предварительном режиме, пакет NuGet отмечен как "Предварительный выпуск". Поэтому необходимо выбрать параметр "Включить предварительные выпуски", иначе пакет не будет найден.

Для установки пакета можно также воспользоваться командной консолью для пакетов, задав команду

    Install-Package Microsoft.Azure.Documents.Client -Pre

После установки ваше решение Visual Studio должно иметь приблизительно следующий вид с двумя добавленными ссылками:

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Настройка приложения ASP.NET MVC</a>
==================================================================

### 

### <a name="_Toc395637764">Добавление модели</a>

Давайте начнем с буквы **M** из аббревиатуры MVC, т. е. с модели (Model). В Обозревателе решений щелкните правой кнопкой мыши папку "Модели" ( *Models*) и выберите **Добавить**, а затем **Класс**.

![Alt text](./media/documentdb-dotnet-application/image12.png)

Назовите новый класс **Item** и добавьте следующий код в этот новый класс:

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
		
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
		
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
		
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Все данные в DocumentDB передаются по сети и хранятся в формате JSON. Для управления сериализацией и десериализацией объектов с помощью JSON.NET можно использовать атрибут JsonProperty, как показано на примере только что созданного сласса "Item". Хоть это и **не обязательно**, при именовании свойств я придерживался принципа "camelCase", принятого в JSON. 

Можно не только управлять форматом свойства при его поступлении в JSON, но и полностью переименовывать свойства .NET, - здесь, например, я переименовал свойство Description. 

Для полного управления обработкой сериализации здесь также можно использовать объекты JsonConverter.  

Чтобы Visual Studio смогла разрешить используемый здесь атрибут JsonProperty, в раздел использования файла класса необходимо добавить следующий оператор использования: 

    using Newtonsoft.Json;

### <a name="_Toc395637765">Добавление контролера</a>

С буквой M разобрались, теперь создадим **C** из аббревиатуры MVC, т. е. класс контроллера (Controller).
В **Обозревателе решений** щелкните правой кнопкой мыши папку "Контроллеры" ( *Controllers*) и выберите **Добавить**, а затем **Контроллер**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


В диалоговом окне **Добавление Scaffold** выберите **"Пустой контроллер MVC 5".** Нажмите **Добавить.**

![Alt text](./media/documentdb-dotnet-application/image15.png)

Назовите новый контроллер **ItemController.**

Visual Studio добавит контроллер ItemController в обозреватель решений, который должен иметь вид, схожий с приведенным ниже снимком экрана.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Добавление представлений</a>

И наконец, создадим букву **V** из аббревиатуры MVC: представление (View).


#### Добавление представления индекса элементов

Разверните папку ***Представления*** в Обозревателе решений и найдите пустую папку Item, созданную Visual Studio ранее при добавлении  *ItemController*. Щелкните правой кнопкой мыши на ***Item*** и выберите "Добавить новое представление".

![Alt text](./media/documentdb-dotnet-application/image17.png)

В диалоговом окне "Добавление представления". Вызовите представление "***Индекс***", используйте шаблон ***Список***, выберите ***Item (todo.Models)***, созданный ранее в качестве класса, и используйте ***~Views/Shared/_Layout.cshtml*** в решении в качестве страницы макета.

![Alt text](./media/documentdb-dotnet-application/image18.png)


После установки этих значений нажмите "Добавить", и Visual Studio создаст представление. Visual Studio создаст шаблон представления. По завершении процесса создания откроется созданный файл cshtml. Можно закрыть этот документ в Visual Studio, мы вернемся к нему позже.

#### Добавление нового элемента представления

Аналогичным образом создайте новое представление для создания новых элементов, как показано в примере ниже.

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Добавление редактирования элемента представления

<a name="_Toc395888515"></a>
============================

И, наконец, добавьте последнее представление для редактирования элементов так же, как ранее.

![Alt text](./media/documentdb-dotnet-application/image20.png)


Как только все будет готово, закройте документы cshtml в Visual Studio, так как мы вернемся к этим представлениям позже.

</h1>
<a name="_Toc395637769">Подключение DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Вывод списка незавершенных элементов</a>

Откройте **ItemController** и удалите весь код в классе, автоматически добавленный Visual Studio (но оставьте сам класс). Мы постепенно воссоздадим его с использованием DocumentDB.

Добавьте следующий фрагмент кода в пустой класс ItemController.

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

Этот код также использует класс "псевдорепозитория", именуемый DocumentDBRepository, который нам еще предстоит создать. Фактически он представляет собой вспомогательный класс, содержащий весь код, специфичный для DocumentDB. Для целей данного пошагового руководства мы не будем реализовать полный уровень доступа к данным со всеми зависимостями, фабриками и структурами репозиториев, как вы бы сделали при разработке настоящего приложения. 
Для целей данного пошагового руководства мы всего лишь соберем всю логику доступа к данным в один проект, чтобы не усложнять задачи и сконцентрировать внимание на особенностях DocumentDB.

Добавьте новый класс в свой проект и назовите его **DocumentDBRepository.**
Замените существующий код в файле класса следующим:

    public static class DocumentDBRepository
    {
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
				
        private static Database database;
        private static Database Database
        {
			get
			{
				if (database == null)
				{
					database = ReadOrCreateDatabase();
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
					collection = ReadOrCreateCollection(Database.SelfLink);
				}
				
				return collection;
			}
        }
		
        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
					string endpoint = ConfigurationManager.AppSettings["endpoint"];
					string authKey = ConfigurationManager.AppSettings["authKey"];
					Uri endpointUri = new Uri(endpoint);
					client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }
    } 

#### 

Выполните разрешение всех пространств имен в Visual Studio. При наличии успешно установленного пакета NuGet разрешить все пространства в Visual Studio будет очень просто. Неразрешенными останутся ссылки на методы ReadOrCreateDatabase и ReadOrCreateCollection, пока мы не добавим их, о чем и пойдет речь далее. 

Здесь используется два вызова методов для чтения или создания баз данных и коллекций документов DocumentDB.
Добавьте в класс следующие два метода:

    private static DocumentCollection ReadOrCreateCollection(string databaseLink)
    {
        var col = Client.CreateDocumentCollectionQuery(databaseLink)
                          .Where(c => c.Id == CollectionId)
                          .AsEnumerable()
                          .FirstOrDefault();

        if (col == null)
        {
            col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        }
		
        return col;
    }

    private static Database ReadOrCreateDatabase()
    {
        var db = Client.CreateDatabaseQuery()
                        .Where(d => d.Id == DatabaseId)
                        .AsEnumerable()
                        .FirstOrDefault();
		
        if (db == null)
        {
            db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
        }
		
        return db;
    }

</h1>

#### 

Это необходимо для настройки базы данных, коллекции документов и создания кода для подключения к DocumentDB через DocumentClient. 

Некоторые значения будут считываться из конфигурации, поэтому нужно открыть **web.config** и добавить следующие строки в разделе
<AppSettings\>:

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

Теперь добавим немного кода для выполнения работы. 
Приложение ToDo List со списком задач прежде всего должно отображать список незавершенных элементов. Ниже показан метод, как это можно сделать, поэтому скопируйте и вставьте его в наш репозиторий:
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
Ссылка на  *CreateDocumentQuery* будет разрешена после добавления вручную директивы использования:

    using Microsoft.Azure.Documents.Linq;

На данном этапе ваше решение должно компилироваться без каких-либо ошибок.

Чтобы запустить приложение сейчас, нужно перейти к главному контроллеру и представлению индекса в этом контроллере. Это поведение по умолчанию для шаблона проекта MVC мы выбрали вначале, но нам нужно кое-что другое! Давайте изменим маршрутизацию в данном приложении MVC, чтобы изменить это поведение.

Откройте ***App\_Start\RouteConfig.cs***, найдите строку, начинающуюся с "defaults:", и измените ее следующим образом:

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Таким образом ASP.NET MVC понимает, что если вы не указали значение в поле URL-адреса для управления поведением маршрутизации, вместо "Главный" нужно использовать "Элемент" в качестве контроллера, а пользовательский индекс - в качестве представления.
Теперь при запуске приложение будет обращаться к вашему **ItemController** и возвращать результаты метода **GetIncompleteItems** в представление Views\Item\Index. 

Если создать и запустить этот проект сейчас, отобразится примерно следующее:    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">Добавление элементов</a>

Давайте добавим некоторые элементы в нашу базу данных, чтобы можно было что-то увидеть, кроме пустой сетки.

У нас уже есть представление для создания и кнопка на представлении индекса, которые пользователь применяет для создания представления. Теперь добавим код в контроллере и репозитории для сохранения записи в DocumentDB.

Откройте **ItemController.cs** и добавьте следующий фрагмент кода, благодаря которому ASP.NET MVC будет знать, что делать при выполнении операции "Создать"; в данном случае это просто отрисовка связанного представления Create.cshtml, созданного ранее.

    public ActionResult Create()
    { 
	return View(); 
    }

Теперь необходимо добавить еще один код в этот контроллер, который будет принимать данные из представления.

Добавьте следующий блок кода, который указывает ASP.NET MVC, что нужно делать с формой POST для этого контроллера.
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**Примечание о безопасности**: Атрибут [ValidateAntiForgeryToken] используется для защиты этого приложения от атак с подделкой запроса между сайтами. Добавления данного атрибута недостаточно - нужно, чтобы эти представления работали с маркером защиты от подделки. Подробнее об этом, а также о том, как правильно это реализовать, см. в статье [Предотвращение подделки запросов между сайтами][]. Исходный код, содержащийся в ссылке загрузки в конце статьи, полностью описывает алгоритм действий.

**Примечание о безопасности**: Кроме того, мы используем атрибут [Bind] для параметра метода, чтобы обеспечить защиту от оверпостинга. Подробнее см. стать. [Основные операции CRUD в приложении ASP.NET MVC][]

Теперь контроллер элементов будет (надежно) передавать элемент Item из формы в метод CreateDocument в репозитории, поэтому добавьте этот метод в класс DocumentDBRepository.

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

Этот метод просто принимает любой объект, переданный ему, и сохраняет его в DocumentDB.
На этом мы завершаем написание кода, необходимого для добавления новых элементов в нашу базу данных.


### <a name="_Toc395637772">Редактирование элементов</a>

И последнее, что нужно сделать, - это обеспечить возможность редактировать элементы в базе данных и отмечать их как завершенные аналогично выполненным задачам. Так же как и в случае с добавлением, представление редактирования уже было добавлено, поэтому просто нужно еще раз добавить дополнительный код в контроллер и класс DocumentDBRepository.

Добавьте следующие методы в класс ItemController;

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

Первый метод обрабатывает команду GET HTTP, которая формируется, когда пользователь щелкает ссылку "Изменить" в представлении индекса. Этот метод извлекает документ из DocumentDB и передает его в представление редактирования.

Затем представление редактирования передаст команду POST HTTP в IndexController. 
Второй метод обрабатывает передачу обновленного объекта в DocumentDB для его сохранения в базе данных.

Добавьте следующие методы в класс DocumentDBRepository;

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

Первый из этих двух методов извлекает элемент из DocumentDB, который передается обратно в ItemController, а затем - в представление редактирования.

Второй метод заменяет документ в DocumentDB версией документа, переданного из ItemController.

Вот и все, что необходимо для запуска приложения, отображения списка незавершенных элементов, добавления новых элементов и, наконец, редактирования элементов.

</h3>
<a name="_Toc395637773">Локальный запуск приложения</a>
=========================================================

### 

Чтобы протестировать приложение на локальном компьютере, нажмите клавишу F5 в Visual Studio. Таким образом будет создано приложение, а браузер будет запущен с пустой сеткой на странице, которая отображалась ранее:

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\.Введите данные в поле элемента, его имени и категории, затем щелкните ссылку **Создать** и задайте значения. Не устанавливайте флажок "Завершено", иначе новый элемент будет добавлен ​​в завершенном состоянии и не будет отображаться в начальном списке.

![Alt text](./media/documentdb-dotnet-application/image25.png)

При выборе варианта "Создать" вы будете перенаправлены обратно на страницу индекса и, возможно, ваш элемент будет отображаться в списке.

![Alt text](./media/documentdb-dotnet-application/image26.png)

Не стесняйтесь добавить еще несколько элементов в список задач.

2\.Нажмите кнопку "Изменить" рядом с элементом в списке, после чего вы перейдете в представление редактирования, где сможете изменять любые свойства объекта, в том числе объекта с установленным флажком "Завершено". Таким образом элементы будут отмечены как завершенные и удалены из списка незавершенных задач.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.Чтобы завершить задачу, просто установите флажок и нажмите **Сохранить.** Вы будете перенаправлены обратно на страницу списка, где элемент больше не будет отображаться в списке.

</h3>
<a name="_Toc395637774">Разверните приложение на веб-сайтах Azure</a>
================================================================

### 

Теперь, когда у вас есть готовое приложение и оно корректно работает в DocumentDB, мы собираемся развернуть его на веб-сайтах Azure.

Если вы выбрали вариант "Разместить в облаке" при создании пустого проекта ASP.NETMVC, Visual Studio упростит процедуру и сделает большую часть работы за нас. Чтобы опубликовать приложение, щелкните правой кнопкой мыши проект в обозревателе решений (убедитесь, что он не запущен локально) и выберите пункт "Опубликовать".

![Alt text](./media/documentdb-dotnet-application/image28.png)

Все уже должно быть настроено в соответствии с вашими учетными данными. На самом деле сайт уже создан в Azure по указанному адресу "URL назначения", и все что вам осталось сделать - это нажать **Опубликовать**.

![Alt text](./media/documentdb-dotnet-application/image29.png)

Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения и запустит браузер, где вы увидите свое творение, запущенное в Azure!

</h3>



<a name="_Toc395637775">Заключение</a>
======================================

### 

Поздравляем! Вы только что создали свое первое приложение ASP.NET MVC с использованием Azure DocumentDB и опубликовали его на веб-сайтах Azure. Исходный код для готового приложения, включая функциональность подробных сведений и удаления, которые мы пропустили, можно загрузить [здесь][].


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/ru-ru/products/visual-studio-express-vs.aspx
[Установщик веб-платформы Майкрософт]: http://www.microsoft.com/web/downloads/platform.aspx
[здесь]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Предотвращение подделки запросов между сайтами]: http://go.microsoft.com/fwlink/?LinkID=517254
[Основные операции CRUD в ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=27-->
