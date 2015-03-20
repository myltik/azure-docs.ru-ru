<properties 
	pageTitle="Начало работы с DocumentDB .NET SDK | Azure" 
	description="Сведения о создании и настройке учетной записи Azure DocumentDB, создании баз данных и коллекций, а также хранении документов JSON в учетной записи базы данных документов NoSQL." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="mimig"/>

#Начало работы с DocumentDB .NET SDK  

В этом руководстве показано, как приступить к использованию [Microsoft Azure DocumentDB (предварительная версия)](https://portal.azure.com/#gallery/Microsoft.DocumentDB) и [пакета SDK DocumentDB для .NET](http://go.microsoft.com/fwlink/p/?linkid=402989). DocumentDB - это служба баз данных документов NoSQL, для которой доступны [несколько интерфейсов API и пакетов SDK](http://go.microsoft.com/fwlink/p/?linkid=522476). Примеры кода в этом разделе написаны на языке C# и используют пакет SDK DocumentDB для .NET, который упакован и распространяется в виде пакета NuGet. 

В данной статье описаны такие сценарии, как создание и настройка учетной записи DocumentDB, создание баз данных, создание коллекций и сохранение документов JSON в учетной записи. Каждый из этих примеров является частью законченного решения, которое доступно на [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Вы можете [загрузить решение](#GetSolution) с целью просмотра примера кода в контексте или можете просто просматривать примеры в этой статье.

##<a id="Connect"></a>Подключение к учетной записи DocumentDB

Мы начнем с создания нового экземпляра класса [DocumentClient](http://go.microsoft.com/fwlink/p/?linkid=522477) для установления подключения к нашей учетной записи DocumentDB.   Нам в нашем приложении C# потребуются следующие ссылки:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
Затем можно создать экземпляр **DocumentClient** с использованием конечной точки учетной записи DocumentDB и первичного или вторичного ключа доступа, связанного с учетной записью. Создайте клиента, используя код, аналогичный приведенному в следующем примере:   

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

> [AZURE.WARNING] Никогда не храните учетные данные в исходном коде. Чтобы не усложнять этот пример, учетные данные приведены в исходном коде. См. [Веб-сайты Azure. Как работают строки приложения и строки подключения](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) для получения информации о способах хранения учетных данных в рабочей среде. 

Значения для EndpointUrl и AuthorizationKey - URI и первичный ключ для учетной записи DocumentDB, их можно получить из раздела [портала управления Azure (предварительная версия)](https://portal.azure.com) для учетной записи DocumentDB. 

![][1]
 
Эти ключи предоставляют административный доступ к учетной записи DocumentDB и ее ресурсам. DocumentDB также поддерживает использование ключей ресурсов, позволяющих клиентам читать, записывать и удалять ресурсы в учетной записи DocumentDB в соответствии с предоставленными вами разрешениями без необходимости использования ключа учетной записи. Дополнительные сведения о ключах ресурсов см. в разделе "Разрешения", [Модель ресурсов и основные принципы DocumentDB](../documentdb-resources/) 

Теперь, когда стало известно, как подключиться к учетной записи DocumentDB и создать экземпляр класса**DocumentClient**, давайте взглянем на работу с ресурсами DocumentDB.  

##<a id="CreateDB"></a>Создание базы данных
Базу данных DocumentDB можно создать с помощью метода [CreateDatabaseAsync](http://go.microsoft.com/fwlink/p/?linkid=522478) класса **DocumentClient**.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Создание коллекции  

Коллекцию DocumentDB можно создать с помощью метода [CreateDocumentCollectionAsync](http://go.microsoft.com/fwlink/p/?linkid=522479) класса **DocumentClient**.  База данных, созданная на предыдущем шаге, имеет несколько свойств, среди которых есть свойство [CollectionsLink](http://go.microsoft.com/fwlink/p/?linkid=522481).  Имея данную информацию, мы можем создать коллекцию.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Создание документов	
Документ DocumentDB можно создать с помощью метода [CreateDocumentAsync](http://go.microsoft.com/fwlink/p/?linkid=522482) класса **DocumentClient**.  Коллекция, созданная на предыдущем шаге, имеет несколько свойств, среди которых есть свойство [DocumentsLink](http://go.microsoft.com/fwlink/p/?linkid=522483).  Располагая этой информацией, мы можем добавить один или несколько документов.  В целях данного примера, мы подразумеваем, что у нас имеется класс Family, описывающий атрибуты членов семьи, такие как имя, пол и возраст.  

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
 

## Запросы к ресурсам DocumentDB
DocumentDB поддерживает функционально богатые запросы документов JSON, хранящихся в каждой коллекции.  Ниже приведены примеры кода разнообразных запросов с использованием как синтаксиса SQL DocumentDB, так и LINQ, которые можно запускать на добавленных на предыдущем шаге документах.  

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

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
	
##<a id="GetSolution"></a>Получение завершенного решения
Чтобы собрать решение GetStarted, которое содержит все примеры из данной статьи, вам понадобится следующее:

-   [Учетная запись DocumentDB][documentdb-create-account].
-   Решение [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) доступно на GitHub. 

Для восстановления ссылок на DocumentDB .NET SDK в Visual Studio 2013 щелкните правой кнопкой мыши решение GetStarted в обозревателе решений, а затем нажмите "Включить восстановление пакета NuGet", который восстановит ссылки. Затем в файле App.config обновите значения EndpointUrl и AuthorizationKey, как описано в разделе [Подключение к учетной записи DocumentDB](#Connect)  

##<a id="NextSteps"></a>Дальнейшие действия
-	Сведения о [наблюдении за учетной записью DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Подробные сведения о модели программирования см. в разделе "Разработка" на [странице документации DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).


[Подключение к учетной записи DocumentDB]: #Connect
[Создание базы данных]: #CreateDB
[Создание коллекции]: #CreateColl
[Создание документов]: #CreateDoc
[Запрос ресурсов DocumentDB]: #Query
[Дальнейшие действия]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png

<!--HONumber=47-->
