<properties 
	pageTitle="Создание веб-приложения ASP.NET MVC с использованием DocumentDB | Azure" 
	description="В этом учебном курсе показано, как использовать службу DocumentDB с .NET для создания веб-приложения со списком дел (To Do List). Хранить данные и осуществлять доступ к ним будем из веб-приложения ASP.NET MVC, размещенном на веб-сайтах Azure." 
	services="documentdb" 
	documentationCenter="" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/26/2015" 
	ms.author="ryancraw"/>

#<a name="_Toc395809351"></a>Создание веб-приложения ASP.NET MVC с использованием DocumentDB

Чтобы показать, как можно эффективно использовать Azure DocumentDB для хранения и запросов документов JSON, в настоящем документе приведена пошаговая инструкция от начала до конца для построения веб-приложения "список дел" с использованием Azure DocumentDB. Задачи будут храниться в виде документов JSON в Azure DocumentDB.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image1.png)

В этом учебном курсе показано, как использовать службу DocumentDB, предоставляемую Azure, для хранения данных и доступа к ним из веб-приложения ASP.NET MVC, размещенного в Azure.

> [AZURE.TIP] Подразумевается, что у вас уже имеется опыт использования ASP.NET MVC и веб-сайтов Azure. Если вы новичок в ASP.NET или [необходимы средства для работы],(#_Toc395637760) то рекомендуется загрузить готовый учебный проект [todo](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/todo) с [GitHub](https://github.com/Azure/azure-documentdb-net) и собрать его с использованием [инструкций, приведенных в конце этого раздела](#GetProject)  После сборки можно просмотреть этот раздел, чтобы получить подробные сведения о коде в контексте проекта.

## <a name="_Toc395637760"></a>Предварительные требования

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

- Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](../../pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/) или последующих версий, либо [Visual Studio Express], являющийся бесплатной версией.
- Пакет SDK Azure для .NET версии 2.3 и выше доступен через [установщик веб-платформы Майкрософт][].

Все снимки экранов в этом разделе выполнены с использованием Visual Studio 2013 с пакетом обновлений 3 и пакета SDK Azure для .NET версии 2.4. Если ваша система сконфигурирована с различными версиями программ, то вполне вероятно, что ваши экраны и опции не будет соответствовать полностью, но если Вы выполните все вышеуказанные требования, то это решение должно работать.

## <a name="_Toc395637761"></a>Шаг 1. Создание учетной записи базы данных DocumentDB

Давайте начнем с создания учетной записи DocumentDB. Если учетная запись уже имеется, можно перейти к разделу [Создание нового приложения ASP.NET MVC](#_Toc395637762) 

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

<br/>
Теперь мы рассмотрим создание нового приложения ASP.NET MVC с нуля. 

## <a name="_Toc395637762"></a>Шаг 2. Создание нового приложения ASP.NET MVC

Теперь давайте создадим новый проект ASP.NET.

1. В меню **Файл** Visual Studio последовательно выберите **Создать** и **Проект**.

   	Откроется диалоговое окно **Новый проект**.
2. На вкладке **Типы проектов** раскройте **Шаблоны**, **Visual C#**, **Веб** и выберите **Веб-приложение ASP.NET**.

  	![Screen shot of the New Project dialog box with the ASP.NET Web Application project type highlighted](./media/documentdb-dotnet-application/image10.png)

3. В поле **Имя** введите имя проекта. В данном уроке используется имя "todo". 
4. Нажмите **Обзор** для выбора папки, в которой будет создан проект, затем нажмите **OK**.

  	Откроется диалоговое окно **Новый проект ASP.NET**.

  	![Screen shot of the New ASP.NET Project dialog box with the MVC template highlighted and the Host in the cloud box checked](./media/documentdb-dotnet-application/image11.png)

5. В панели шаблонов выберите **MVC**.
6. Если вы планируете разместить ваше приложение в Azure, установите флажок **Разместить в облаке**, расположенный в правом нижнем углу. Мы выбрали размещение в облаке, запуск приложения, размещенного на веб-сайтах Azure. При выборе этого варианта будет осуществлена предварительная подготовка веб-сайтов Azure, что позволит облегчить развертывание готового рабочего приложения. Если необходимое разместить это приложение где-либо еще или если вы не хотите производить настройку параметров Azure в данный момент, то просто снимите флажок **Разместить в облаке**.
7. Нажмите **OK**, Visual Studio создаст пустое приложение с шаблоном ASP.NET MVC. 
8. Если решено разместить приложение в облаке, то вы увидите по крайней мере один дополнительный экран с запросом входа в учетную запись Azure и предоставление некоторых значений для нового веб-сайта. Укажите все дополнительные значения и продолжите. 

  	Мы не выбираем "Сервер базы данных", так как не будем использовать сервер базы данных SQL Azure, а собираемся позднее создать новую учетную запись Azure DocumentDB на портале Azure (предварительная версия). 

  	![Screen shot of the Configure Microsoft Azure Website dialog box](./media/documentdb-dotnet-application/image11_1.png)

9. После того как Visual Studio закончит создание шаблонного приложения MVC, у вас будет пустое приложение ASP.NET, которое можно запустить локально.

Мы не станем проект запускать локально, потому что я уверен, что мы все видели приложение ASP.NET "Hello World". Пойдем непосредственно к добавлению DocumentDB в этом проекте и созданию приложения.

## <a name="_Toc395637767"></a>Шаг 3. Добавление в проект DocumentDB

Теперь займемся большей частью подготовки ASP.NET MVC, которая понадобится для 
данного решения. Теперь давайте перейдем к действительной цели этого урока, добавив Azure DocumentDB в наше веб-приложение.

1. DocumentDB .NET SDK комплектуется и распространяется как пакет NuGet. Чтобы загрузить пакет NuGet в Visual Studio, воспользуйтесь диспетчером пакетов NuGet в Visual Studio, щелкнув правой кнопкой мыши на проекте в **обозревателе проектов** и затем выбрав **Управление пакетами NuGet**.

  	Откроется диалоговое окно **Управление пакетами NuGet**.

  	![Sreen shot of the right-click options for the project in Solution Explorer, with Manage NuGet Packages highlighted.](./media/documentdb-dotnet-application/image21.png)

2. В текстовом поле **поиска в сети** введите "Azure DocumentDB". Из результатов поиска установите пакет **Клиентские библиотеки Microsoft Azure DocumentDB**. При этом будет загружен и установлен пакет DocumentDB, а также все зависимости, такие как Newtonsoft.Json.

  	> [AZURE.NOTE] Пока служба находится в режиме предварительного просмотра, пакет NuGet помечен как Prerelease (предварительный выпуск), поэтому необходимо включить опцию "Включить предварительные выпуск", в противном случае вы не сможете найти пакет. 

  	Другой способ - использование консоли диспетчера пакетов для установки пакета с помощью следующих команд.

    	Install-Package Microsoft.Azure.Documents.Client -Pre

3. После установки пакета ваше решение Visual Studio должно иметь следующий вид с добавленными двумя новыми ссылками, Microsoft.Azure.Documents.Client и Newtonsoft.Json.

  	![Sreen shot of the two references added to the project in Solution Explorer.](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>Шаг 4. Настройка приложения MVC ASP.NET
 
А теперь давайте добавим модели, представления и контроллеры к этому приложению MVC:

- [Добавление модели](#_Toc395637764).
- [Добавление контроллера](#_Toc395637765).
- [Добавление представлений](#_Toc395637766).


### <a name="_Toc395637764"></a>Добавление модели

Давайте начнем с буквы **M** из аббревиатуры MVC, т. е. с модели (Model). 

1. В обозревателе решений щелкните правой кнопкой мыши папку **Модели**, выберите в меню команду **Добавить**, а затем выберите пункт **Класс**.

  	Откроется диалоговое окно **Добавление нового элемента**.

2. Назовите новый класс **Item** и добавьте следующий код в новый файл Item.cs.

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

	Все данные в DocumentDB передаются по сети и хранятся в виде JSON. Чтобы управлять способом сериализации и десериализации объектов в JSON.NET, можно использовать атрибут **JsonProperty**, как показано в только что созданном классе **Item**. Вам не обязательно **нужно** делать это, но я хочу убедиться, что свойства соответствуют соглашению об именовании JSON camelCase. 

	Вы можете не только управлять форматом имени свойства при его отправке в JSON, но и полностью переименовать свойства .NET, как это было сделано со свойством **Описание**. 

	При желании можно также использовать объекты **JsonConverter** для полного управления обработкой сериализации.  

3. Для того чтобы Visual Studio смогла разрешить используемый атрибут **JsonProperty**, необходимо добавить следующее к оператору using в разделе using файла класса.

    	using Newtonsoft.Json;

### <a name="_Toc395637765"></a>Добавление контроллера

Это касается буквы **M**, теперь создадим **C** из аббревиатуры MVC, класс контроллера.

1. В **обозревателе решений** щелкните правой кнопкой мыши папку **Контроллеры** и выберите **Добавить**, а затем нажмите **Контроллер**.

    Откроется диалоговое окно **Добавить Scaffold**.

2. Выберите **MVC 5 Controller - Empty** (Контроллер MVC 5 - пустой) и нажмите **Добавить**.

	![Screen shot of the Add Scaffold dialog box with the MVC 5 Controller - Empty option highlighted](./media/documentdb-dotnet-application/image14.png)

3. Назовите новый контроллер **ItemController**.

	![Screen shot of the Add Controller dialog box](./media/documentdb-dotnet-application/image15.png)

После создания файла решение Visual Studio должно иметь следующий вид с новым файлом ItemController.cs в **обозревателе решения**.

![Screen shot of Solution Explorer with the new ItemController.cs file and Item.cs file highlighted](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766"></a>Добавление представлений

И наконец, создадим букву **V** из аббревиатуры MVC, представление (View).

- [Добавление представления "Список элементов"](#AddItemIndexView) 
- [Добавление представления "Новый элемент"](#AddNewIndexView) 
- [Добавление представления "Изменение элемента"](#_Toc395888515) 


#### <a name="AddItemIndexView"></a>Добавление представления "Список элементов"

1. В **обозревателе решения** раскройте папку ***Представления***, щелкните правой кнопкой мыши пустую папку **Item**, которая была ранее создана Visual Studio при добавлении класса **ItemController**, нажмите **Добавить** и **Представление**.

![Screen shot of Solution Explorer showing the Item folder with the Add View commands highlighted](./media/documentdb-dotnet-application/image17.png)

2. В диалоговом окне **Добавить представление** выполните следующие действия.
	- В поле **Название представления** введите ***Index***.
	- В поле **Шаблон** выберите ***Список***.
	- В поле **Класс модели** выберите ***Элемент (todo.Models)***.
	- В поле страницы макета введите ***~/Views/Shared/_Layout.cshtml***.
	- Щелкните **Добавить**.
	
![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image18.png)

3. После того как установлены все эти значения, нажмите **Добавить**, и Visual Studio создаст представление. Visual Studio создаст шаблон представления. По завершении процесса создания откроется созданный файл cshtml. Теперь можно закрыть этот файл в Visual Studio, вернемся к нему позже.

#### <a name="AddNewIndexView"></a>Добавление представления "Новый элемент"

Аналогично созданию представления **Список элементов** создадим новое представление для создания новых **Элементов**.

В диалоговом окне **Добавить представление** выполните следующие действия.

- В поле **Название представления** введите ***Создать***.
- В поле **Шаблон** выберите ***Создать***.
- В поле **Класс модели** выберите ***Элемент (todo.Models)***.
- В поле страницы макета введите ***~/Views/Shared/_Layout.cshtml***.
- Щелкните **Добавить**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image19.png)

#### <a name="_Toc395888515"></a>Добавление представления "Изменение элемента"

И наконец, добавим последнее представление для редактирования **элементов** так же, как и ранее.


В диалоговом окне **Добавить представление** выполните следующие действия.

- В поле **Название представления** введите ***Изменить***.
- В поле **Шаблон** выберите ***Изменить***.
- В поле **Класс модели** выберите ***Элемент (todo.Models)***.
- Нажмите **Создать в виде частичного представления**.
- Щелкните **Добавить**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image20.png)

Как только все будет готово, закройте документы cshtml в Visual Studio, так как мы вернемся к этим представлениям позже.

## <a name="_Toc395637769"></a>Шаг 5. Подключение DocumentDB

В этом разделе мы добавим код для обработки следующего:

- [вывод списка незавершенных элементов];(#_Toc395637770) 
- [добавление элементов];(#_Toc395637771) 
- [редактирование элементов](#_Toc395637772) 

### <a name="_Toc395637770"></a>Вывод списка незавершенных элементов

1. Откройте **ItemController** и удалите весь код в классе, автоматически добавленный Visual Studio (но оставьте сам класс). Мы постепенно воссоздадим его с использованием DocumentDB.

2. Добавьте следующий фрагмент кода в пустой класс **ItemController**.

    	public ActionResult Index()
    	{
			var items = DocumentDBRepository<Item>.GetItems(d => !d.Completed);
			return View(items);
    	}

	Этот код также использует класс "псевдорепозитория", который называется **DocumentDBRepository**, его нам предстоит создать позже. Это просто вспомогательный класс, который содержит весь код, имеющий отношение к DocumentDB. Для целей данного пошагового руководства мы не будем реализовать полный уровень доступа к данным со всеми зависимостями, фабриками и структурами репозиториев, как вы бы сделали при разработке настоящего приложения. 
	Для целей данного пошагового руководства мы всего лишь собираемся внедрить всю логику доступа к данным в один проект, чтобы не усложнять вещи и сконцентрироваться на информации о DocumentDB.

3. Добавьте в проект новый класс и назовите его **DocumentDBRepository**. 
4. Замените существующий код в файле **DocumentDBRepository** следующим кодом.

    	public static class DocumentDBRepository<T>
    	{
	       using Microsoft.Azure.Documents; 
	       using Microsoft.Azure.Documents.Client; 
	       using Microsoft.Azure.Documents.Linq; 
		
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

5. Потратьте некоторое время, чтобы разрешить все пространства имен в Visual Studio, которые должны включать добавление следующего с помощью директив using в файле DocumentDBRepository.cs.

		using Microsoft.Azure.Documents;
		using Microsoft.Azure.Documents.Client;
		using Microsoft.Azure.Documents.Linq;
		using System.Configuration;

 	При наличии успешно установленного пакета NuGet разрешить все пространства в Visual Studio будет очень просто. Ссылки на методы **ReadOrCreateDatabase** и **ReadOrCreateCollection** остаются неразрешенными до их добавления, это произойдет дальше.
 
6. Есть два способа вызова методов, используемых здесь, для чтения или создания баз данных DocumentDB и коллекций документов. Добавьте следующие два метода в класс **DocumentDBRepository**.

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

	Данный код обрабатывает настройки базы данных [**DocumentCollection**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.documentcollection.aspx) и создает некоторый код для подключения к DocumentDB через [**DocumentClient**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx). 

7. Из параметров конфигурации читаются некоторые значения, поэтому откройте файл **Web.config** и добавьте следующие строки в раздел `<AppSettings>`.
	
    	<add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    	<add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    	<add key="database" value="ToDoList"/>
    	<add key="collection" value="Items"/>
	
8. Теперь обновите эти значения с помощью раздела "Ключи" портала управления Azure Management. Значение **URI** из раздела "Ключи" используйте в качестве значения ключа конечной точки, а значение **Первичный ключ** используйте в качестве ключа authKey.
	
	Теперь добавим немного кода для выполнения работы. 	

9. Первое, что мы хотим сделать с приложением списка заданий, - отображение незавершенных элементов.  Ниже приведен метод, который выполняет это действие, поэтому скопируйте и вставьте его в классе **DocumentDBRepository** в любое место.

	    public static IEnumerable<T> GetItems(Expression<Func<T, bool>> predicate) 
	    {
			return Client.CreateDocumentQuery<T>(Collection.DocumentsLink) 
				.Where(predicate) 
				.AsEnumerable(); 
		} 
	    
	На данном этапе ваше решение должно компилироваться без каких-либо ошибок.

	Чтобы запустить приложение сейчас, нужно перейти к **главному контроллеру** и **представлению индекса** в этом контроллере. Это поведение по умолчанию для шаблона проекта MVC мы выбрали вначале, но нам нужно кое-что другое! Давайте изменим маршрутизацию в данном приложении MVC, чтобы изменить это поведение.

11. Откройте ***App\_Start\RouteConfig.cs*** и найдите строку, начинающуюся с "defaults:", измените ее следующим образом.

    	defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

	Теперь она сообщает ASP.NET MVC, что если не указано значение URL-адреса для управления поведения маршрутизацией, то вместо **Home** нужно использовать **Item** в качестве контроллера и использовать **Index** в качестве представления.
	Теперь, если запустить приложение, оно вызовет **ItemController** и вернет результаты метода **GetIncompleteItems** в представление **Views**\\**Item**\\**Index**. 

12. Если собрать и запустить этот проект сейчас, то вы увидите нечто похожее на это.    

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Добавление элементов

Давайте теперь добавим некоторые элементы в нашу базу данных, чтобы можно было что-то увидеть, кроме пустой сетки.

Мы уже имеем представление для **Создания** и кнопку на представлении **индекса**, которые используются пользователем для представления **Создания**. Давайте добавим код в контроллере и репозиторий для сохранения записи в DocumentDB.

1. Откройте файл ItemController.cs и добавьте в него следующий код, благодаря которому ASP.NET MVC узнает, что необходимо выполнить для действия **Создать**. В этом случае просто выводится связанное представление Create.cshtml, которое было создано ранее.

    	public ActionResult Create()
    	{ 
			return View(); 
   		}

	Теперь нам нужно добавить еще немного кода в этом контроллере, который будет принимать данные из **Создание**.

2. Продолжаем и добавляем следующий блок кода, который указывает ASP.NET MVC, что делать с формой POST для этого контроллера.
	
    	[HttpPost]
    	[ValidateAntiForgeryToken]
   	 	public async Task<ActionResult> Create([Bind(Include = 	"Id,Name,Description,Completed")] Item item)  
  	  	{
			if (ModelState.IsValid)  
			{  
			    await DocumentDBRepository<Item>.CreateItemAsync(item);
			    return RedirectToAction("Index");  
			}   
			return View(item);   
		}

	**Примечание о безопасности**. Атрибут **ValidateAntiForgeryToken** используется здесь для защиты этого приложения от атак с подделкой запроса между сайтами. Кроме добавления этого атрибута представления также должны работать с данным маркером защиты от подделки запросов. Дополнительные сведения об этом и примеры правильной реализации данной технологии см. в разделе [Предотвращение атак с подделкой межсайтовых запросов][]. Исходный код, предоставленный на [Github][], имеет полную реализацию.

	**Примечание о безопасности**. Также используется атрибут **Bind** для защиты от атак overposting. Дополнительные сведения см. в разделе [Основные операции CRUD в ASP.NET MVC][].

3. Теперь, когда метод **Create** реализован, **ItemController** передает объекту **Item** из формы метода **CreateDocument**. Добавьте следующий метод в класс **DocumentDBRepository**.

    	public static async Task<Document> CreateItemAsync(T item)
   	 	{
   	   		return await Client.CreateDocumentAsync(Collection.SelfLink, item);
   		}

	Этот метод просто принимает любой объект, переданный ему, и сохраняет его в DocumentDB.

На этом мы завершаем написание кода, необходимого для добавления новых элементов в нашу базу данных.


### <a name="_Toc395637772"></a>Редактирование элементов

Нам нужно сделать еще одну последнюю вещь, это реализация возможности редактирования **элементов** в базе данных, и чтобы отметить их завершенными, так же как мы отмечаем выполненные задачи. Представление для редактирования уже добавлено в проект, поэтому нам снова нужно просто добавить некоторый код в контроллер и класс **DocumentDBRepository**.

1. Добавьте следующие методы в класс **ItemController**.

    	[HttpPost]
   		[ValidateAntiForgeryToken]
    	public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    	{
     	   if (ModelState.IsValid)
    	    {
    	        await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
    	        return RedirectToAction("Index");
    	    }

  	      return View(item);
 	   	}
		
		public ActionResult Edit(string id)
		{
		    if (string.IsNullOrEmpty(id))
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
					 
		    Item item = (Item)DocumentDBRepository<Item>.GetItem(d => d.Id == id);
		 	
		    if (item == null)
		    {
		        return HttpNotFound();
		    }
		 	
		    return View(item);
		}
		
	
	Первый метод обрабатывает команду GET HTTP, которая формируется, когда пользователь нажимает ссылку **Изменить** в представлении **индекса**. Этот метод извлекает [**документ **](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) из DocumentDB и передает его в представление **редактирования**.

	Представление **редактирования** выполняет команду HTTP POST в **IndexController**. 
	
	Второй метод, который мы добавили, обрабатывает передачу обновленного объекта для DocumentDB, чтобы сохранить его в базе данных.

2. Добавьте следующую директиву using в файл ItemController.cs.

		using todo.Models;
		using System.Threading.Tasks;
		using System.Net;

3. Добавьте следующие методы в класс **DocumentDBRepository**.

    	public static T GetItem(Expression<Func<T, bool>> predicate)
    	{
        	return Client.CreateDocumentQuery<T>(Collection.DocumentsLink)
                    	.Where(predicate)
                    	.AsEnumerable()
                    	.FirstOrDefault();
    	}	
		
		public static Document GetDocument(string id) 
		{ 
			return Client.CreateDocumentQuery(Collection.DocumentsLink) 
				.Where(d => d.Id == id) 
				.AsEnumerable() 
				.FirstOrDefault(); 
		} 

    	public static async Task<Document> UpdateItemAsync(string id, T item)
    	{
        	Document doc = GetDocument(id);	
        	return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    	}

	Первый из этих двух методов извлекает **элементы** из DocumentDB, которые передаются обратно в **ItemController**, и затем в представление **редактирования**.
	
	Второй из двух только что добавленных методов заменяет **документ** в DocumentDB версией **документа**, переданного из **ItemController**.

4. Добавьте следующую директиву using в файл DocumentDBRepository.cs.

		using System.Threading.Tasks;

	Вот и все, что нам понадобится для запуска нашего приложения, вывода невыполненных **элементов**, добавления новых **элементов** и изменения **элементов**.

## <a name="_Toc395637773"></a>Шаг 6. Локальный запуск приложения

Для проверки приложения на локальном компьютере выполните следующие действия.

1. Нажмите клавишу F5 Visual Studio для сборки приложения в режиме отладки. Должно собраться приложение и запуститься браузер с пустой сеткой, которую мы уже видели ранее.

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image24.png)

	Если на этом этапе возникают ошибки, можно сравнить код с кодом учебника todo на [GitHub][].

2. Нажмите ссылку **Создать** и добавьте значения в поля **Имя** и **Описание**. Оставьте флажок **Завершено** сброшенным, в противном случае новый **элемент** будет добавлен в завершенном состоянии и не появится в начальном списке.

	![Screen shot of the Create view](./media/documentdb-dotnet-application/image25.png)

3. Нажмите **Создать**, и вы будете перенаправлены назад на представление **индекса**, а **элемент** появится в списке.

	![Screen shot of the Index view](./media/documentdb-dotnet-application/image26.png)

	Можете добавить еще несколько **элементов** в список задач.

3. Нажмите на **Изменить** рядом с **элементом** в списке, и вы будете переведены к представлению **Изменить**, где можно обновить любое свойство вашего объекта, включая флаг **Завершено**. Если установить флаг **Завершено** и нажать **Сохранить**, то **элемент** будет удален из списка незавершенных задач.

	![Screen shot of the Index view with the Completed box checked](./media/documentdb-dotnet-application/image27.png)

4. После тестирования приложения нажмите Ctrl+F5 для выхода из режима отладки. Теперь все готово к развертыванию!

##<a name="_Toc395637774"></a>Шаг 7. Развертывание приложения на веб-сайтах Azure

Теперь, когда у вас есть законченное приложение и оно работает правильно в DocumentDB, мы собираемся развернуть его на веб-сайтах Azure. Если вы выбрали **Разместить в облаке** при создании пустого проекта ASP.NETMVC, то Visual Studio все упростит и сделает большую часть работы за нас. 

Для этого нужно всего лишь щелкнуть правой кнопкой мыши проект развертывания Azure в **обозревателе решений** и нажать **Опубликовать**.

![Screen shot of the Publish option in Solution Explorer](./media/documentdb-dotnet-application/image28.png)

Все уже должно быть настроено в соответствии с вашими учетными данными, на самом деле сайт уже создан в Azure и указан в **URL-адресе назначения**. Все, что вам нужно, это нажать **Опубликовать**.

![Screen shot of the Publish Web dialog box](./media/documentdb-dotnet-application/image29.png)

Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения и запустит браузер, где вы увидите свое творение, запущенное в Azure!

##<a name="_Toc395637775"></a>Дальнейшие действия

Поздравляем! Вы только что создали свое первое приложение ASP.NET MVC с использованием Azure DocumentDB и опубликовали его на веб-сайтах Azure. Исходный код для законченного приложения, в том числе подробные сведения и удаленные функциональные возможности, которые не были включены в эту статью, можно загрузить или клонировать с [Github][]. Если вы заинтересованы в его добавлении к своему приложению, берите код и добавляйте его.

Для добавления дополнительной функциональности в приложение ознакомьтесь с доступными интерфейсами API [библиотеки DocumentDB для .NET](http://msdn.microsoft.com/library/azure/dn783362.aspx), а также вносите свои изменения в код библиотеки DocumentDB для .NET на [GitHub][]. 

##<a id="GetProject"></a>Получение проекта из GitHub

Если вы хотите сэкономить время и просто собрать готовое решение todo без добавления собственного кода, то вам повезло. Готовое решение доступно на GitHub и его можно собрать и развернуть за несколько минут, следуя нижеуказанным инструкциям.

1. Убедитесь в наличии [необходимого программного обеспечения],(#_Toc395637760)  которое включает Visual Studio и пакет SDK Azure для .NET версии 2.3 и выше.

2. Выполните клонирование репозитория azure-documentdb-net, используя Git для Windows ([http://www.git-scm.com/](http://www.git-scm.com/)) или загрузите zip-файл с сайта [GitHub](https://github.com/Azure/azure-documentdb-net/).

2. В Visual Studio откройте файл todo.sln из каталога azure-documentdb-net/tutorials/todo.

3. Чтобы восстановить ссылки на пакет SDK DocumentDB для .NET в Visual Studio 2013, щелкните правой кнопкой мыши на решении todo в **обозревателе решений**, а затем нажмите **Включить восстановление пакетов NuGet**, при этом будут восстановлены ссылки. 

4. Получите значения **URI** и **Первичный ключ** или **Вторичный ключ** из раздела **Ключи** учетной записи DocumentDB на [портале Azure (предварительная версия)](https://portal.azure.com/). 

	
	Если у вас нет учетной записи, см. раздел [Создание учетной записи базы данных] (/documentation/articles/documentdb-create-account/).

	![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the KEYS tile highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade](./media/documentdb-dotnet-application/keys.png)

5. В файле Web.config обновите значения по умолчанию для ключей **конечной точки** и **authKey**.

    	<add key="endpoint" value="~enter URI for your DocumentDB Account, from Azure Management Portal~" /> 
		<add key="authKey" value="~enter either Primary or Secondary key for your DocumentDB Account, from Azure Management Portal~" /> 

	- Скопируйте значение **URI** из раздела "Ключи" и вставьте его в значение свойства **конечная точка**. 
	- Скопируйте значение **Первичный ключ** или **Вторичный ключ** из раздела **Ключи** и вставьте его в значение свойства **authKey**.
	


7. Теперь можно [запустить приложение локально],(#_Toc395637773)  а затем [развернуть его на веб-сайтах Azure](#_Toc395637774) 


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Установщик веб-платформы Майкрософт]: http://www.microsoft.com/web/downloads/platform.aspx
[Github]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Предотвращение подделки запросов между сайтами]: http://go.microsoft.com/fwlink/?LinkID=517254
[Основные операции CRUD в ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=47-->
