<properties title="Начало работы с учетной записью DocumentDB" pageTitle="Начало работы с учетной записью DocumentDB | Azure" description="Узнайте, как создавать и настраивать учетную запись DocumentDB, создавать базы данных и коллекции, а также сохранять в учетной записи документы JSON." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

# Начало работы с учетной записью DocumentDB

Это руководство демонстрирует, как начать работу с **Azure DocumentDB (предварительная версия)**. Примеры написаны на C# и используют пакет SDK .NET DocumentDB. Здесь описаны такие сценарии, как создание и настройка учетной записи DocumentDB, создание баз данных, создание коллекций и сохранение документов JSON в учетной записи. За дополнительной информацией по использованию Azure DocumentDB обратитесь к разделу Дальнейшие действия.

Для использования данного руководства по началу работы необходимо иметь учетную запись DocumentDB и ключ доступа (первичный или вторичной) к этой учетной записи. Дополнительные сведения см. в разделе:

-   [Создание учетной записи DocumentDB][Создание учетной записи DocumentDB]

## Оглавление

-   [Подключение к учетной записи DocumentDB][Подключение к учетной записи DocumentDB]
-   [Создание базы данных][Создание базы данных]
-   [Создание коллекции][Создание коллекции]
-   [Создание документов][Создание документов]
-   [Запрос ресурсов DocumentDB][Запрос ресурсов DocumentDB]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="Connect"></span></a>Подключение к учетной записи DocumentDB

Для программной работы с DocumentDB доступны несколько пакетов SDK и интерфейсов API. Ниже приведены примеры, написанные на C# и использующие пакет SDK .NET DocumentDB.

Мы начнем с создания DocumentClient для подключения к нашей учетной записи DocumentDB. Нам в нашем приложении C# потребуются следующие ссылки:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;

Экземпляр DocumentClient может быть создан с использованием конечной точки учетной записи DocumentDB и первичного или вторичного ключа, связанного с учетной записью.

Конечную точку и ключи учетной записи DocumentDB можно получить в выноске учетной записи DocumentDB на портале управления Azure предварительной версии.

![][0]

> Обратите внимание, что ключи доступа DocumentDB, размещенные на выноске, предоставляют административный доступ к учетной записи DocumentDB и к ее ресурсам. DocumentDB также поддерживает использование ключей ресурсов, позволяющих клиентам читать, записывать и удалять ресурсы в учетной записи DocumentDB в соответствии с предоставленными вами разрешениями без необходимости использования ключа учетной записи.

Создайте клиентское приложение с использованием кода из следующего примера.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

    // Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**Предупреждение:**Никогда не сохраняйте учетные данные в исходном коде. Для сохранения простоты примера они показаны в исходном коде. См. раздел [Веб-сайты Windows Azure: как работают строки приложений и строки подключения][Веб-сайты Windows Azure: как работают строки приложений и строки подключения] для получения информации о том, как хранить учетные данные.

Теперь, когда вы знаете, как подключаться к учетной записи DocumentDB и создавать экземпляр DocumentClient, давайте посмотрим на работу с ресурсами DocumentDB.

## <span id="CreateDB"></span></a>Создание базы данных

Базу данных DocumentDB можно создать с помощью метода CreateDatabaseAsync класса DocumentClient с использованием пакета SDK .NET.

    // Create a Database
    Database database = await client.CreateDatabaseAsync(
        new Database
            {
                Id = "FamilyRegistry"
            });

## <span id="CreateColl"></span></a>Создание коллекции

Коллекцию DocumentDB можно создать с помощью метода CreateDocumentCollectionAsync класса DocumentClient с использованием пакета SDK .NET. База данных, созданная на предыдущем этапе, имеет несколько свойств, среди которых есть CollectionsLink. Имея данную информацию, мы можем создать коллекцию.

    // Create a document collection
    DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
        new DocumentCollection
            {
                Id = "FamilyCollection"
            });

## <span id="CreateDoc"></span></a>Создание документов

Документ DocumentDB можно создать с помощью метода CreateDocumentAsync класса DocumentClient с использованием пакета SDK .NET. Коллекция, созданная на предыдущем этапе, имеет несколько свойств, среди которых есть DocumentsLink. Располагая этой информацией, мы можем добавить один или несколько документов. В целях данного примера, мы подразумеваем, что у нас имеется класс Family, описывающий атрибуты членов семьи, такие как имя, пол и возраст.

    // Create the Andersen Family document
    Family andersenFamily = new Family
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, andersenFamily);

    // Create the WakeField Family document
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, wakefieldFamily);

## <span id="Query"></span></a>Запрос ресурсов DocumentDB

DocumentDB поддерживает функционально богатые запросы документов JSON, хранящихся в каждой коллекции. Ниже приведены примеры разнообразных запросов с использованием как синтаксиса SQL DocumentDB, так и LINQ, которые можно запускать на добавленных на предыдущем этапе документах.

    // Query the documents using DocumentDB SQL for the Andersen family
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQl with one join
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join
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

С полной версией примера начального уровня можно ознакомиться [здесь][здесь].

## <span id="NextSteps"></span></a>Дальнейшие действия

-   Узнайте, как [контролировать учетную запись DocumentDB][контролировать учетную запись DocumentDB].
-   Подробную информацию о модели программирования см. раздел Разработка на [странице документации DocumentDB][странице документации DocumentDB]

  [Создание учетной записи DocumentDB]: ../documentdb-create-account/
  [Подключение к учетной записи DocumentDB]: #Connect
  [Создание базы данных]: #CreateDB
  [Создание коллекции]: #CreateColl
  [Создание документов]: #CreateDoc
  [Запрос ресурсов DocumentDB]: #Query
  [Дальнейшие действия]: #NextSteps
  [0]: ./media/documentdb-get-started/gs1.png
  [Веб-сайты Windows Azure: как работают строки приложений и строки подключения]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [здесь]: https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started
  [контролировать учетную запись DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkId=402378
  [странице документации DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkID=402319
