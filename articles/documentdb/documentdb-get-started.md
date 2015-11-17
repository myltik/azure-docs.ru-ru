<properties
	pageTitle="Базы данных NoSQL — приступая к работе с пакетом SDK для DocumentDB .NET | Microsoft Azure"
	description="Узнайте, как создать базу данных и настроить учетную запись Azure DocumentDB. Создавайте базы данных, коллекции и храните документы JSON в учетной записи базы данных NoSQL."
	keywords="Создание базы данных, создание базы данных, базы данных nosql, базы данных nosql, nuget, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="11/05/2015"
	ms.author="anhoh"/>

#Начало работы с DocumentDB .NET SDK  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Добро пожаловать в раздел начала работы с пакетом DocumentDB .NET SDK! После изучения этого учебника e вас будет консольное приложение, которое создает ресурсы DocumentDB и отправляет запросы к ним.

Мы рассмотрим следующие вопросы:

- создание учетной записи DocumentDB и подключение к ней;
- настройка решения Visual Studio;
- создание базы данных;
- создание коллекции;
- создание документов JSON;
- выполнение запросов к коллекции;
- удаление базы данных.

У вас нет времени? Не беспокойтесь! Завершенное решение доступно на [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Краткие инструкции см. в разделе [Получение завершенного решения](#GetSolution).

После этого используйте кнопки голосования в верхней или нижней части этой страницы, чтобы отправить нам отзыв. Если вы хотите, чтобы мы связались с вами, укажите ваш электронный адрес в комментариях.

А теперь приступим к работе!

## Предварительные требования

Убедитесь, что у вас есть указанные ниже компоненты.

- Активная учетная запись Azure. Если у вас нет учетной записи, вы можете зарегистрироваться и получить [бесплатную пробную версию Azure](http://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013 или Visual Studio 2015](http://www.visualstudio.com/).

## Этап 1: создание учетной записи DocumentDB

Создадим учетную запись DocumentDB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к этапу [Настройка решения Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Шаг 2: настройка решения Visual Studio

1. Откройте **Visual Studio** у себя на компьютере.
2. В меню **Файл** выберите пункт **Создать**, а затем — пункт **Проект**.
3. В диалоговом окне **Создание проекта** последовательно выберите элементы **Шаблоны**, **Visual C#**, **Консольное приложение**, а затем укажите имя проекта и нажмите кнопку **OK**.
4. В **обозревателе решений** щелкните правой кнопкой мыши новое консольное приложение (оно находится в решении Visual Studio).
5. Не выходя из меню, щелкните элемент **Управление пакетами NuGet**.
6. На левой панели окна **Управление пакетами NuGet** последовательно щелкните элементы **В сети** / **nuget.org**.
7. В поле ввода **Поиск в Интернете** начните поиск **клиентской библиотеки DocumentDB**.
8. Найдите **клиентскую библиотеку Microsoft Azure DocumentDB** и нажмите кнопку **Установить**. Идентификатором пакета для клиентской библиотеки DocumentDB является [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

Отлично! Теперь, когда мы завершили настройку, начнем писать код.

##<a id="Connect"></a> Этап 3: подключение к учетной записи DocumentDB

Во-первых, добавьте эти ссылки в начало приложения C# в файле Program.cs:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT]Чтобы выполнить это приложение, убедитесь, что вы добавили зависимости, как описано выше.

Затем сохраните конечную точку учетной записи DocumentDB, а также первичный или вторичный ключ доступа, доступный на [портале предварительной версии Azure](https://portal.azure.com).

![Снимок экрана портала предварительной версии Azure, на котором показаны учетная запись DocumentDB с выделенным АКТИВНЫМ концентратором, выделенной кнопкой "Ключи" в колонке учетной записи DocumentDB и выделенными универсальным кодом ресурса, ПЕРВИЧНЫМ КЛЮЧОМ И ВТОРИЧНЫМ КЛЮЧОМ в колонке "Ключи".][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

Мы запустим ознакомительное демонстрационное приложение, создав экземпляр **DocumentClient**. Создайте асинхронную задачу с именем **GetStartedDemo** и еще один экземпляр **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Вызовите асинхронную задачу из метода **Main**, используя код, аналогичный приведенному ниже.

	public static void Main(string[] args)
    {
		try
    	{
        	GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

> [AZURE.WARNING]Никогда не сохраняйте учетные данные в исходном коде. Здесь они показаны в исходном коде для сохранения простоты примера. Сведения о том, как сохранять учетные данные в рабочей среде, см. в статье [ Веб-сайты Azure: как работают строки приложений и строки подключений](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Взгляните на приложение на [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs): это пример того, как учетные данные сохраняются не в исходном коде.

Теперь, когда вы знаете, как подключаться к учетной записи DocumentDB и создавать экземпляр **DocumentClient**, давайте посмотрим на работу с ресурсами DocumentDB.

## Этап 4: создание базы данных
[Базу данных](documentdb-resources.md#databases) можно создать, используя метод [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) класса **DocumentClient**. База данных представляет собой логический контейнер для хранения документов, разделенных между коллекциями. Создайте ее в вашем методе **GetStartedDemo** после создания **DocumentClient**.

	// Check to verify a database with the id=FamilyRegistry does not exist
	Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

	// If the database does not exist, create a new database
    if (database == null)
    {
    	database = await client.CreateDatabaseAsync(
    		new Database
            {
            	Id = "FamilyRegistry"
            });

		// Write the new database's id to the console
		Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateColl"></a>Этап 5: создание коллекции  

> [AZURE.WARNING]Элемент **CreateDocumentCollectionAsync** создаст новую коллекцию S1, с которой связаны ценовые требования. Дополнительные сведения см. на нашей [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).

Вы можете создать [коллекцию](documentdb-resources.md#collections), используя метод [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) класса **DocumentClient**. Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript. Созданная коллекция будет сопоставлена с [уровнем производительности S1](documentdb-performance-levels.md). Создав базу данных в методе **GetStartedDemo**, создайте коллекцию с именем **FamilyCollection**.

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

	// If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
    	documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
        	new DocumentCollection
            {
            	Id = "FamilyCollection"
            });

		// Write the new collection's id to the console
		Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateDoc"></a>Этап 6: создание документов
Вы можете создать [документ](documentdb-resources.md#documents) с помощью метода [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx), который относится к классу **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь мы можем добавить один или несколько документов. Если у вас уже есть данные, которые необходимо хранить в базе данных, можно использовать [средство миграции данных](documentdb-import-data.md) в DocumentDB.

Сначала нужно создать классы **Parent**, **Child**, **Pet**, **Address** и **Family**. Для этого после метода **GetStartedDemo** необходимо добавить следующие подклассы.

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

Затем в асинхронном методе **GetStartedDemo** создайте документы.

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

	// If the document does not exist, create a new document
	if (document == null)
	{
	    // Create the Andersen Family document
	    Family andersonFamily = new Family
	    {
	        Id = "AndersenFamily",
	        LastName = "Andersen",
	        Parents = new Parent[] {
	            new Parent { FirstName = "Thomas" },
	            new Parent { FirstName = "Mary Kay"}
	        },
	        Children = new Child[] {
	            new Child
	            { 
	                FirstName = "Henriette Thaulow", 
	                Gender = "female", 
	                Grade = 5, 
	                Pets = new Pet[] {
	                    new Pet { GivenName = "Fluffy" } 
	                }
	            } 
	        },
	        Address = new Address { State = "WA", County = "King", City = "Seattle" },
	        IsRegistered = true
	    };
	
	    // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
	    await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
	}

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
        Family wakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new Parent[] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
	}

Теперь в своей учетной записи DocumentDB вы создали следующие базу данных, коллекцию и документы.

![Схема, иллюстрирующая иерархические отношения между учетной записью, базой данных, коллекцией и документами](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>Этап 7: запросы ресурсов DocumentDB

Служба DocumentDB поддерживает функционально богатые [запросы](documentdb-sql-query.md) документов JSON, хранящихся в каждой коллекции. Ниже приведены примеры кода разнообразных запросов с использованием как синтаксиса SQL DocumentDB, так и LINQ, которые можно запускать на добавленных на предыдущем шаге документах. Добавьте эти запросы в асинхронный метод **GetStartedDemo**.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

В приведенной ниже схеме демонстрируется, как для созданной коллекции вызывается синтаксис запроса DocumentDB SQL (этот принцип действует и при работе с запросами LINQ).

![Схема, иллюстрирующая масштаб и значение запроса](./media/documentdb-get-started/collection-documents.png)

Ключевое слово [FROM](documentdb-sql-query.md#from-clause) использовать в запросе необязательно, так как запросы DocumentDB заранее привязаны к одной коллекции. Поэтому слова "FROM Families f" можно заменить на "FROM root r" или любое другое имя переменной. Поведение службы DocumentDB будет таким, будто Families, root или любая другая переменная указывает на текущую коллекцию по умолчанию.

##<a id="DeleteDatabase"></a>Этап 8: удаление базы данных

Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (коллекций, документов и т. д.). Вы можете удалить базу данных и документ клиента, добавив в конец асинхронного метода **GetStartedDemo** приведенный ниже фрагмент кода.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
	client.Dispose();

##<a id="Run"></a>Этап 9: запуск приложения

Теперь все готово к запуску приложения. В конце вашего метода **Main** добавьте приведенную ниже строку кода, которая позволит прочитать вывод консоли до того, как будет завершена работа приложения.

	Console.ReadLine();

Теперь, чтобы построить приложение в режиме отладки, нажмите, находясь в Visual Studio, клавишу F5.

Должны отобразиться выходные данные вашего приложения. Они должны содержать результаты обработки добавленных запросов. При этом выглядеть они должны примерно так, как показано в примере ниже.

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query

Поздравляем! Вы создали свое первое приложение DocumentDB!

##<a id="GetSolution"></a> Получение полного решения
Чтобы собрать решение GetStarted, которое содержит все примеры из данной статьи, вам понадобится следующее:

-   [учетная запись DocumentDB][documentdb-create-account];
-   решение [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started), доступное в GitHub.

Чтобы в Visual Studio восстановить ссылки на пакет SDK для .NET в DocumentDB, в обозревателе решений щелкните правой кнопкой мыши решение **GetStarted**, а затем выберите элемент **Включить восстановление пакета NuGet**. Затем в файле App.config обновите значения EndpointUrl и AuthorizationKey согласно инструкциям раздела [Подключение к учетной записи DocumentDB](#Connect).

## Дальнейшие действия

-   Требуется более сложный пример ASP.NET MVC? См. статью [Создание веб-приложения ASP.NET MVC с использованием DocumentDB](documentdb-dotnet-application.md).
-	Узнайте, как [контролировать учетную запись DocumentDB](documentdb-monitor-accounts.md).
-	Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
-	Дополнительные сведения о модели программирования см. в разделе «Разработка» [на странице документации DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=Nov15_HO3-->