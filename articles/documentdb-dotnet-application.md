<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Создание веб-приложения ASP.NET MVC с использованием DocumentDB | Azure" description="Узнайте, как использовать DocumentDB для создания веб-приложения списка дел. Данные вы будете хранить в веб-приложении ASP.NET MVC, размещенном в Azure, из которого вы также будете их использовать."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Build a web application with ASP.NET MVC using DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Overview</a>
==============================================================================================

<a name="_Toc395637759">Scenario</a>
------------------------------------

To highlight how customers can efficiently leverage Azure DocumentDB to store and query JSON documents, this document provides an end-to-end walk-through of building a ToDo List web application using Azure Document DB.

This walk-through shows you how to use DocumentDB service provided by Azure to store and access data from an ASP.NET MVC web application hosted on Azure and presumes that you have some prior experience using ASP.NET MVC and Azure Websites.

You will learn:

1\. Creating and provisioning a DocumentDB Account

2\. Creating a ASP.NET MVC Application

3\. Connecting to and using Azure DocumentDB from your web application

4\. Deploying the Web Application to Azure Websites

By following this walk-through, you will build a simple web-based task-management application that allows creating, retrieving and completing of tasks. The tasks will be stored as JSON documents in Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Before following the instructions in this article, you should ensure that you have the following installed:

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (or [Visual Studio Express][] which is the free version)

Azure SDK for .NET version 2.3 or higher, available through [Microsoft Web Platform Installer][]

All the screen shots in this document have been taken using Visual Studio 2013 with Update 3 applied and Azure SDK for .NET version 2.4. If your system is configured with different versions it is possible that your screens and options won't match entirely, but if you meet the above prerequisites this solution should work.

<a name="_Toc395637761">Create a DocumentDB database account</a>
================================================================

To provision a DocumentDB database account in Azure, open the Azure Management Portal and either Click the Azure Gallery tile on the homepage or click "+" in the lower left hand corner of the screen.

![Alt text](./media/documentdb-dotnet-application/image2.png)


This will open the Azure Gallery, where you can select from the many available Azure services. In the Gallery, select "Data, storage and backup" from the list of categories.

![Alt text](./media/documentdb-dotnet-application/image3.png)

From here, select the option for Azure DocumentDB

![Alt text](./media/documentdb-dotnet-application/image4.png)


Then select "Create" from the bottom of the screen

![Alt text](./media/documentdb-dotnet-application/image5.png)

This will open up the "New DocumentDB" blade where you can specify the name, region, scale, resource group and other settings for your new account.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Once you're done supplying the values for your account, click "Create" and the provisioning process will begin creating your database account.
When the provisioning process is complete, you should see a notification appear in the notifications area of the portal and the tile on your start screen (if you selected to create one) will change to show the completed action.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Once provisioning is complete, clicking the DocumentDB tile from the start screen will bring up the main blade for this newly created DocumentDB account.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Using the "Keys" button, access your endpoint URL and the Primary Key, copy these to your clipboard and keep them handy as we will use these values in the web application we will use these values in the web application we will create next.

We will now walk through how to create a new ASP.NET MVC application from the ground-up. For your reference the complete solution can be downloaded [here].

<a name="_Toc395637762">Create a new ASP.NET MVC application</a>
================================================================

In Visual Studio, click File - New Project and select the option to create a new ASP.NET MVC Web Application.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Select where you would like to create the project, what you want the project to be called (I'm using "todo") and click Ok.

![Alt text](./media/documentdb-dotnet-application/image11.png)


If you plan on hosting your application in Azure then select the box on the lower right to "Host in the cloud". We've selected to host in the cloud, and run the application hosted in an Azure Website. Selecting this option will pre-provision an Azure Website for you and make life a lot easier when it comes times to deploy the final working application. If you want to host this elsewhere or don't want to configure Azure upfront, then just un-check the "Host in the Cloud" option.

Select OK and let Visual Studio do its thing around scaffolding the empty ASP.NET MVC template. If you chose to host this in the Cloud you will see at least one additional screen asking you to login to your Azure account and provide some values for your new Website. Proceed to supply all these Azure values and continue. 

I haven't chosen a "Database server" here because we're not using an Azure SQL Database Server here, we're going to be creating a new Azure DocumentDB Account later on in the management portal. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Once Visual Studio has finished creating the boilerplate MVC application you have an empty ASP.NET application that you can run locally.

We'll skip running locally because I'm sure we've all seen the ASP.NET "Hello World" application. Let's go straight to adding DocumentDB to this project and building our application.

</h1>
<a name="_Toc395637767">Adding DocumentDB to your project</a>
=============================================================

That takes care of most of the ASP.NET MVC plumbing that we need for
this solution. Now let's get to the real purpose of this tutorial, adding Azure DocumentDB to our web application.

### 

### <a name="_Toc395637764">Installing the NuGet package</a>

The DocumentDB .NET SDK is packaged and distributed as a NuGet package. Using the NuGet package manager in Visual Studio (which you can get to by Right-Clicking on the Project and choosing "Manage NuGet Packages"

![Alt text](./media/documentdb-dotnet-application/image21.png)

Search for Online for "Azure DocumentDB" and install the package. This will download and install the DocumentDB package as well as all dependencies, like Newtonsoft.Json.

**NOTE:** While the service is still in preview, the NuGet package is marked as "Prerelease" so you need to include the option to "Include Prerelease", else you will not find the package. 

Alternatively you could use the Package Command Console to install the package by typing;

    Install-Package Microsoft.Azure.Documents.Client -Pre

Once installed your Visual Studio solution should resemble the following with two new references added;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Setting up the ASP.NET MVC application</a>
==================================================================

### 

### <a name="_Toc395637764">Add a Model</a>

Let's begin by creating the **M** in MVC, the model. In Solution Explorer, right-click the *Models* folder and then click **Add**, then **Class**

![Alt text](./media/documentdb-dotnet-application/image12.png)

Name your new Class, **Item** and then add the following code in to this new Class

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

All data in DocumentDB is passed over the wire, and stored, as JSON. To control the way your objects are serialized/deserialized by JSON.NET you can use the JsonProperty attribute as demonstrated in the Item class we just created. You don't **have** to do this but i wanted to ensure that my properties followed the JSON camelCase naming conventions. 

Not only can you control the format of the property name when it goes in to JSON, but you can entirely rename your .NET properties like I did with the Description property. 

You can, if you like, use JsonConverter objects here as well to completely control how serialization is handled.  

In order to get Visual Studio to resolve the "JsonProperty" attribute used here you need to add the following using statement to the using section of your class file;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Add a Controller</a>

That takes care of the M, now let's create the **C** in MVC, a controller class.
In **Solution Explorer**, right-click the *Controllers* folder and then click **Add**, then **Controller**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


In the **Add Scaffold** dialog box, click **MVC 5 Controller - Empty.** Click **Add.**

![Alt text](./media/documentdb-dotnet-application/image15.png)

Name your new Controller, **ItemController.**

Visual Studio will now add the ItemController your Solution Explorer should look like similar to below.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Add Views</a>

And finally, let's create the **V** in MVC, a view.


#### Add Item Index View

Expand the ***Views***  folder in Solution Explorer and location the (empty) Item folder which Visual Studio would've created for you when you added the *ItemController* earlier. Right click on ***Item*** and choose to Add a new View.

![Alt text](./media/documentdb-dotnet-application/image17.png)

In the "Add View" dialog. Call your view "***Index***", use the ***List*** Template, select the ***Item (todo.Models)*** which we created earlier as the class and finally use the ***~Views/Shared/_Layout.cshtml*** in the Solution as the Layout page.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Once all these values are set, click Add and let Visual Studio create your view for you. Visual Studio will create a template view. Once it is done, it will open the cshtml file created. We can close this document in Visual Studio as we will come back to it later.

#### Add New Item View

In a similar fashion to above, create a new View for creating new Items as per the example shown below;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Add Edit Item View

<a name="_Toc395888515"></a>
============================

And finally, add one last View for editing an Item in the same way as before;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Once this is done, close the cshtml documents in Visual Studio as we will return to these Views later.

</h1>
<a name="_Toc395637769">Wiring up DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Listing Incomplete Items</a>

Open the **ItemController** and remove all the code within the class (but leave the class) that Visual Studio added. We'll rebuild it piece by piece using DocumentDB.

Add the following code snippet within the now empty ItemController class;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

This code also uses a "pseudo repository" class called DocumentDBRepository which we have yet to create. This is actually just a Helper class that contains all the DocumentDB specific code. For the purposes of this walk-through we aren't going to implement a full data access layer with dependency injection, and factories and repository patterns, as you would probably be doing if you were building a real world application. 
For the purposes of this walk-through we're just going to put all the data access logic directly in to one project to keep things simple and focus on the DocumentDB specific bits.

Add a new Class to your project and call it **DocumentDBRepository.**
Replace the code in the class file with the following;

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

Spend some time resolving all the namespaces in Visual Studio. All namespaces will be easy to resolve in Visual Studio as long as the NuGet package was installed successfully. The references to the ReadOrCreateDatabase and ReadOrCreateCollection methods will remain unresolved until we have added them, which comes next. 

There are two method calls used here for reading or creating DocumentDB Databases and Document Collections.
So add the following two methods to this class;

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

This takes care setting up the Database, a DocumentCollection, and creating some code to connect to DocumentDB through the DocumentClient. 

We're reading some values from configuration, so open the **web.config** and add the following lines under the
<AppSettings\> section;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

Теперь добавим код для выполнения работы. 
Приложение ToDo List со списком задач прежде всего должно отображать список незавершенных элементов. Ниже показан метод, как это можно сделать, поэтому скопируйте и вставьте его в наш репозиторий:
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
Ссылка на *CreateDocumentQuery* при разрешении потребует добавления вручную с использованием директивы;

    using Microsoft.Azure.Documents.Linq;

На данном этапе ваше решение должно компилироваться без каких-либо ошибок.

Чтобы запустить приложение сейчас, нужно перейти к главному контроллеру и представлению индекса в этом контроллере. Это поведение по умолчанию для шаблона проекта MVC мы выбрали вначале, но нам нужно кое-что другое! Давайте изменим маршрутизацию в данном приложении MVC, чтобы изменить это поведение.

Откройте ***App\_Start\RouteConfig.cs*** и найдите строку, начинающуюся со "значений по умолчанию:" ("defaults:"),  и измените ее следующим образом;

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Таким образом ASP.NET MVC понимает, что если вы не указали значение в поле URL-адреса для управления поведением маршрутизации, вместо "Главный" нужно использовать "Элемент" в качестве контроллера, а пользовательский индекс - в качестве представления.
Теперь при запуске приложение будет обращаться к **ItemController** и возвращать результаты метода **GetIncompleteItems** в представление просмотра, элемента или индекса. 

Если создать и запустить этот проект сейчас, отобразится примерно следующее:    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">Добавление элементов</a>

Давайте добавим некоторые элементы в нашу базу данных, чтобы можно было что-то увидеть, кроме пустой сетки.

У нас уже есть представление для создания и кнопка на представлении индекса, которые пользователь применяет для создания представления. Теперь добавим код в контроллере и репозитории для сохранения записи в DocumentDB.

Откройте ***ItemController.cs*** и добавьте следующий фрагмент кода, благодаря которому ASP.NET MVC понимает, что нужно делать для действия "Создать". В данном случае требуется только отрисовка связанного HTML представления Create.cs, созданного ранее.

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

**Примечание о безопасности**: Атрибут [ValidateAntiForgeryToken] используется для защиты этого приложения от атак с подделкой запроса между сайтами. Добавления данного атрибута недостаточно - нужно, чтобы эти представления работали с маркером защиты от подделки. Подробнее об этом, а также о том, как правильно это сделать, см. в статье [Предотвращение подделки запроса между сайтами][]. Исходный код, содержащийся в ссылке загрузки в конце статьи, полностью описывает алгоритм действий.

**Примечание о безопасности**: Кроме того, мы используем атрибут [привязки] для параметра метода, чтобы обеспечить защиту от оверпостинга. Подробнее об этом см. в статье [Основные операции CRUD в приложении ASP.NET MVC][]

Теперь контроллер элементов  будет (надежно) передавать элемент из формы в метод CreateDocument в репозитории, поэтому добавьте этот метод в класс DocumentDBRepository.

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

Затем представление редактирования передает команду POST HTTP в IndexController. 
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

2\.Нажмите кнопку "Изменить" рядом с элементом в списке и вы будете переведены в представление редактирования, где сможете изменить свойства объекта, в том числе объекта с установленным флажком "Завершено". Таким образом элементы будут отмечены как завершенные и удалены из списка незавершенных задач.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.Чтобы завершить задачу, просто установите флажок и нажмите **Сохранить.** Вы будете перенаправлены обратно на страницу списка, где элемент больше не будет отображаться в списке.

</h3>
<a name="_Toc395637774">Разверните приложение на веб-сайтах Azure</a>
================================================================

### 

Теперь, когда у вас есть готовое приложение и оно корректно работает в DocumentDB, мы собираемся развернуть его на веб-сайтах Azure.

Если вы выбрали вариант "Разместить в облаке" при создании пустого проекта ASP.NETMVC, Visual Studio упростит процедуру и сделает большую часть работы за нас. Чтобы опубликовать приложение, щелкните правой кнопкой мыши проект в обозревателе решений (убедитесь, что он не запущен локально) и выберите пункт "Опубликовать".

![Alt text](./media/documentdb-dotnet-application/image28.png)

Все уже должно быть настроено в соответствии с вашими учетными данными. На самом деле сайт уже создан в Azure по конечному URL-адресу, вам просто нужно нажать кнопку**Опубликовать**.

![Alt text](./media/documentdb-dotnet-application/image29.png)

Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения и запустит браузер, где вы увидите свое творение, запущенное в Azure!

</h3>



<a name="_Toc395637775">Заключение</a>
======================================

### 

Поздравляем! Вы только что создали свое первое приложение ASP.NET MVC с использованием Azure DocumentDB и опубликовали его на веб-сайтах Azure. Исходный код для готового приложения, включая подробную информацию и функции удаления, которые мы пропустили, можно загрузить [здесь][].


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/ru-ru/products/visual-studio-express-vs.aspx
[Установщик веб-платформы Майкрософт]: http://www.microsoft.com/web/downloads/platform.aspx
[здесь]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Предотвращение подделки запросов между сайтами]: http://go.microsoft.com/fwlink/?LinkID=517254
[Основные операции CRUD в ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
