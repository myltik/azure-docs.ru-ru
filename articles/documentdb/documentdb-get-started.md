<properties
	pageTitle="Приступая к работе с пакетом SDK для DocumentDB .NET | Microsoft Azure"
	description="Сведения о создании и настройке учетной записи Azure DocumentDB, создании баз данных и коллекций, а также хранении документов JSON в учетной записи базы данных документов NoSQL."
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
	ms.date="09/03/2015"
	ms.author="anhoh"/>

#Начало работы с DocumentDB .NET SDK  

Из данного руководства вы узнаете, как начать работу с [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) и [SDK DocumentDB .NET](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/). Вы сможете построить консольное приложение, которое создает ресурсы DocumentDB, отправляет к ним запросы и отображает выходные данные в окне консоли.

DocumentDB — это служба баз данных документов NoSQL, для которой доступны [несколько интерфейсов API и пакетов SDK](https://msdn.microsoft.com/library/dn781482.aspx). Примеры кода в этом разделе написаны на языке C# и используют пакет DocumentDB .NET SDK, который упакован и распространяется в виде пакета NuGet.

В этой статье описаны следующие сценарии:

- создание учетной записи DocumentDB и подключение к ней;
- добавление DocumentDB в решение Visual Studio;
- создание баз данных;
- создание коллекций;
- создание документов JSON;
- отправка запросов к ресурсам;
- удаление баз данных.

Нет времени на знакомство с учебником, и вы просто хотите получить рабочее решение? Не беспокойтесь. Завершенное решение доступно на [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Краткие инструкции см. в разделе [Получение завершенного решения](#GetSolution).

Завершив изучение учебника, оцените его содержимое с помощью кнопок голосования в начале или конце раздела. Данный раздел активно обновляется, поэтому для нас важны ваши отзывы о том, как улучшить его. Если вы хотите, чтобы мы связались с вами, укажите ваш адрес электронной почты в своих комментариях.

## Предварительные требования

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

- Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/) с обновлением 4 или выше.

## Этап 1: создание учетной записи DocumentDB

Давайте начнем с создания учетной записи DocumentDB. Если у вас уже есть учетная запись, вы можете перейти к этапу [Настройка решения Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Шаг 2: настройка решения Visual Studio

1. Откройте **Visual Studio** у себя на компьютере.
2. В меню **Файл** щелкните пункт **Создать**, а затем — элемент **Проект**.
3. В диалоговом окне **Создать проект** последовательно выберите элементы **Шаблоны** / **Visual C#** / **Консольное приложение**, укажите имя проекта и нажмите кнопку **Добавить**.
4. В **обозревателе решений** щелкните правой кнопкой мыши новое консольное приложение (оно находится в решении Visual Studio).
5. Не выходя из меню, щелкните элемент **Управление пакетами NuGet**.
6. На левой панели окна **Управление пакетами NuGet** последовательно щелкните элементы **В сети** / **nuget.org**.
7. В поле ввода **Поиск в Интернете** начните поиск **клиентской библиотеки DocumentDB**.
8. Найдите **клиентскую библиотеку Microsoft Azure DocumentDB** и нажмите кнопку **Установить**. Идентификатором пакета для клиентской библиотеки DocumentDB является [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

Отлично! Теперь вы готовы начать работу с DocumentDB.

##<a id="Connect"></a> Этап 3: подключение к учетной записи DocumentDB

Мы начнем с создания нового экземпляра класса [DocumentClient](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.aspx) для установления подключения к нашей учетной записи DocumentDB. В нашем приложении C# нам понадобятся приведенные ниже ссылки.

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

Вы можете создать экземпляр **DocumentClient** с использованием конечной точки учетной записи DocumentDB и первичного или вторичного ключа, связанного с учетной записью. Добавьте эти свойства в класс.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

Теперь давайте создадим новую асинхронную задачу, которая в классе называется **GetStartedDemo**. В рамках этой задачи создайте и настройте **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient.
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Вызовите асинхронную задачу из метода Main, используя код, похожий на тот, что приведен ниже.

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

> [AZURE.WARNING]Никогда не сохраняйте учетные данные в исходном коде. Здесь они показаны в исходном коде для сохранения простоты примера. Сведения о том, как сохранять учетные данные в рабочей среде, см. в статье [ Веб-сайты Azure: как работают строки приложений и строки подключений](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Взгляните на приложение на [GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs): это пример того, как учетные данные сохраняются не в исходном коде.

Значения EndpointUrl и AuthorizationKey являются, соответственно, универсальным кодом ресурса и первичным ключом для вашей учетной записи DocumentDB. Их можно получить в колонке [Ключи](https://portal.azure.com), относящейся к этой учетной записи.

![Снимок экрана портала предварительной версии Azure, на котором показаны учетная запись DocumentDB с выделенным АКТИВНЫМ концентратором, выделенной кнопкой "Ключи" в колонке учетной записи DocumentDB и выделенными универсальным кодом ресурса, ПЕРВИЧНЫМ КЛЮЧОМ И ВТОРИЧНЫМ КЛЮЧОМ в колонке "Ключи".][keys]

Эти ключи предоставляют административный доступ к вашей учетной записи DocumentDB и ресурсам в ней. DocumentDB также поддерживает использование ключей ресурсов, позволяющих клиентам читать, записывать и удалять ресурсы в учетной записи DocumentDB в соответствии с предоставленными вами разрешениями без необходимости использования ключа учетной записи. Дополнительные сведения о ключах ресурсов см. в разделах [Разрешения](documentdb-resources.md#permissions) и [Просмотр, копирование и повторное формирование ключей доступа](documentdb-manage-account.md#keys).

Теперь, когда вы знаете, как подключаться к учетной записи DocumentDB и создавать экземпляр **DocumentClient**, давайте посмотрим на работу с ресурсами DocumentDB.

## Этап 4: создание базы данных
[Базу данных](documentdb-resources.md#databases) можно создать, используя метод [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) класса **DocumentClient**. База данных представляет собой логический контейнер для хранения документов, разделенных между коллекциями. Создайте ее в вашем методе **GetStartedDemo** после создания **DocumentClient**.

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Этап 5: создание коллекции  

> [AZURE.WARNING]Элемент **CreateDocumentCollectionAsync** создаст новую коллекцию S1, с которой связаны ценовые требования. Дополнительные сведения см. на нашей [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).

Вы можете создать [коллекцию](documentdb-resources.md#collections), используя метод [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) класса **DocumentClient**. Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript. Созданная коллекция будет сопоставлена с [уровнем производительности S1](documentdb-performance-levels.md). База данных, созданная на предыдущем этапе, имеет несколько свойств, среди которых есть свойство [CollectionsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.database.collectionslink.aspx). Располагая этой информацией, вы, создав базу данных, можете теперь создать коллекцию.

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });

##<a id="CreateDoc"></a>Этап 6: создание документов
Вы можете создать [документ](documentdb-resources.md#documents) с помощью метода [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx), который относится к классу **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Коллекция, созданная на предыдущем этапе, имеет несколько свойств, среди которых есть свойство [DocumentsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.documentcollection.documentslink.aspx). Располагая этой информацией, мы можем добавить один или несколько документов. Если у вас уже есть данные, которые необходимо хранить в базе данных, можно использовать [средство миграции данных](documentdb-import-data.md) в DocumentDB.

Сначала нужно создать классы **Parent**, **Child**, **Pet**, **Address** и **Family**. Для этого нужно добавить приведенные ниже подклассы.

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

    // Create the Andersen family document.
	Family AndersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child {
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, AndersenFamily);

    // Create the WakeField family document.
    Family WakefieldFamily = new Family
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, WakefieldFamily);

Теперь в своей учетной записи DocumentDB вы создали следующие базу данных, коллекцию и документы.

![Схема, иллюстрирующая иерархические отношения между учетной записью, базой данных, коллекцией и документами](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>Этап 7: запросы ресурсов DocumentDB

Служба DocumentDB поддерживает функционально богатые [запросы](documentdb-sql-query.md) документов JSON, хранящихся в каждой коллекции. Ниже приведены примеры кода разнообразных запросов с использованием как синтаксиса SQL DocumentDB, так и LINQ, которые можно запускать на добавленных на предыдущем шаге документах. Добавьте эти запросы в асинхронный метод **GetStartedDemo**.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQL with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

В приведенной ниже схеме демонстрируется, как для созданной коллекции вызывается синтаксис запроса DocumentDB SQL (этот принцип действует и при работе с запросами LINQ).

![Схема, иллюстрирующая масштаб и значение запроса](./media/documentdb-get-started/collection-documents.png)

Ключевое слово [FROM](documentdb-sql-query.md/#from-clause) использовать в запросе необязательно, так как запросы DocumentDB заранее привязаны к одной коллекции. Поэтому слова "FROM Families f" можно заменить на "FROM root r" или любое другое имя переменной. Служба DocumentDB посчитает, что по умолчанию "Families", "root" и любое другое имя переменной имеют отношение к текущей коллекции.

##<a id="DeleteDatabase"></a>Этап 8: удаление базы данных

Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (коллекций, документов и т. д.). Вы можете удалить базу данных и документ клиента, добавив в конец асинхронного метода **GetStartedDemo** приведенный ниже фрагмент кода.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync(database.SelfLink);
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
	{
	  "id": "AndersenFamily",
 	  "child": "Henriette Thaulow"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Jesse"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Lisa"
	}
	{ family = AndersenFamily, child = Henriette Thaulow }
	{ family = WakefieldFamily, child = Jesse }
	{ family = WakefieldFamily, child = Lisa }


> [AZURE.NOTE]Если запустить приложение несколько раз, не удалив при этом базу данных, может возникнуть такая проблема: будет создана база данных с уже используемым ИД. Чтобы избежать этого, вы можете проверить, существует ли уже база данных, коллекция или документ с таким же ИД. Ссылка на информацию о том, как это можно сделать, доступна на нашей странице [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started).

Поздравляем! Вы создали свое первое приложение DocumentDB!

##<a id="GetSolution"></a> Получение полного решения
Чтобы собрать решение GetStarted, которое содержит все примеры из данной статьи, вам понадобится следующее:

-   [учетная запись DocumentDB][documentdb-create-account];
-   решение [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started), доступное в GitHub.

Для восстановления ссылок на DocumentDB .NET SDK в Visual Studio 2013 щелкните правой кнопкой мыши решение **GetStarted** в обозревателе решений, а затем щелкните элемент **Включить восстановление пакета NuGet**. Затем в файле App.config обновите значения EndpointUrl и AuthorizationKey согласно инструкциям раздела [Подключение к учетной записи DocumentDB](#Connect).

## Дальнейшие действия

-   Требуется более сложный пример ASP.NET MVC? См. статью [Создание веб-приложения ASP.NET MVC с использованием DocumentDB](documentdb-dotnet-application.md).
-	Узнайте, как [контролировать учетную запись DocumentDB](documentdb-monitor-accounts.md).
-	Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
-	Дополнительные сведения о модели программирования см. в разделе "Разработка" [на странице документации DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=September15_HO1-->