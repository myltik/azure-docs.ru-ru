<properties title="Get started with the DocumentDB .NET SDK" pageTitle="Начало работы с DocumentDB .NET SDK | Azure" description="Узнайте, как создать и настроить учетную запись Azure использования documentdb по плану баз данных, создавать коллекции и хранить документы JSON в рамках учетной записи базы данных NoSQL документа." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="mimig" manager="jhubbard" editor="monicar" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="mimig" />

#Начало работы с DocumentDB .NET SDK  

Из данного руководства вы узнаете, как начать работу с [**Microsoft Azure DocumentDB (предварительная версия)**](https://portal.azure.com/#gallery/Microsoft.DocumentDB) и SDK DocumentDB .NET. DocumentDB представляет собой службу базы данных документов NoSQL, у которой есть несколько API-интерфейсов и ряд SDK. Образцы кода в данной статье написаны на C# с использованием [DocumentDB .NET SDK](http://go.microsoft.com/fwlink/p/?linkid=402989), который поставляется в виде пакета NuGet. 

В данной статье описаны такие сценарии, как создание и настройка учетной записи DocumentDB, создание баз данных, создание коллекций и сохранение документов JSON в учетной записи. Каждый из этих образцов кода является частью комплексного решения, доступного на [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Вы можете [загрузить решение](#GetSolution) с целью просмотра примера кода в контексте или можете просто просматривать примеры в этой статье.

##Оглавление

-	[Подключение к учетной записи DocumentDB](#Connect)
-	[Создание базы данных](#CreateDB)
-	[Создание коллекции](#CreateColl)
-	[Создание документов](#CreateDoc)
-	[Запрос ресурсов DocumentDB](#Query)
-	[Получение полного решения](#GetSolution)
-	[Дальнейшие действия](#NextSteps)

##<a id="Connect"></a>Подключение к учетной записи DocumentDB

Мы начнем с создания нового экземпляра класса **DocumentClient** для установления подключения к нашей учетной записи DocumentDB.   Нам в нашем приложении C# потребуются следующие ссылки:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
Экземпляр **DocumentClient** может быть создан с использованием конечной точки учетной записи DocumentDB и первичного или вторичного ключа доступа, связанного с учетной записью.  

Конечную точку и ключи учетной записи DocumentDB можно получить в выноске учетной записи DocumentDB на [портале управления Azure предварительной версии](https://portal.azure.com/#gallery/Microsoft.DocumentDB). 

![][1]
 
>Обратите внимание, что ключи доступа DocumentDB, размещенные на выноске, предоставляют административный доступ к учетной записи DocumentDB и к ее ресурсам.  DocumentDB также поддерживает использование ключей ресурсов, позволяющих клиентам читать, записывать и удалять ресурсы в учетной записи DocumentDB в соответствии с предоставленными вами разрешениями без необходимости использования ключа учетной записи.    

Создайте клиентское приложение с использованием кода из следующего примера.  

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**Внимание!** Никогда не храните учетные данные в исходном коде. Для демонстрации данного примера учетные данные приведены в исходном коде. См. [Веб-сайты Microsoft Azure: принцип использования строк приложения и подключения](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) с описанием порядка хранения учетных данных. 

Теперь, когда вы знаете, как подключаться к учетной записи DocumentDB и создавать экземпляр **DocumentClient**, давайте рассмотрим работу с ресурсами DocumentDB.  

##<a id="CreateDB"></a>Создание базы данных
Базу данных DocumentDB можно создать, используя метод **CreateDatabaseAsync** класса DocumentClient с использованием пакета DocumentDB SDK .NET.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Создание коллекции  

Коллекцию DocumentDB можно создать, используя метод **CreateDocumentCollectionAsync** класса DocumentClient с использованием пакета DocumentDB SDK .NET.  База данных, созданная на предыдущем шаге, имеет несколько свойств, среди которых есть свойство **CollectionsLink**.  Имея данную информацию, мы можем создать коллекцию.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Создание документов	
Документ DocumentDB можно создать, используя метод **CreateDocumentAsync** класса DocumentClient с использованием пакета DocumentDB SDK .NET.  Коллекция, созданная на предыдущем шаге, имеет несколько свойств, среди которых есть свойство **DocumentsLink**.  Располагая этой информацией, мы можем добавить один или несколько документов.  В целях данного примера, мы подразумеваем, что у нас имеется класс Family, описывающий атрибуты членов семьи, такие как имя, пол и возраст.  

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
 

##<a id="Query"></a>Запрос ресурсов DocumentDB
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

##<a id="GetSolution"></a>Получение полного решения
Чтобы собрать решение GetStarted, которое содержит все примеры из данной статьи, вам понадобится следующее:

-   [Учетная запись DocumentDB][documentdb-create-account].
-   Решение [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) доступно на GitHub. 

Для восстановления ссылок на DocumentDB .NET SDK в Visual Studio 2013 щелкните правой кнопкой мыши решение "GetStarted" в обозревателе решений, а затем нажмите "Включить восстановление пакета NuGet", который восстановит ссылки. 

##<a id="NextSteps"></a>Дальнейшие действия
-	Узнайте, как [выполнять мониторинг учетной записи DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Подробные сведения о модели программирования см. в разделе "Разработка" на [странице документации по DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).


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

<!--HONumber=35.2-->
