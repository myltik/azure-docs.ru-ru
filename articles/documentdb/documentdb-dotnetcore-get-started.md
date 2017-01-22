---
title: "Руководство по NoSQL: пакет SDK для .NET Core в DocumentDB | Документация Майкрософт"
description: "Руководство по NoSQL, в котором создается оперативная база данных и консольное приложение C# с использованием пакета SDK для .NET Core для DocumentDB. DocumentDB — это база данных NoSQL для JSON."
keywords: "руководство nosql, оперативная база данных, консольное приложение c#"
services: documentdb
documentationcenter: .net
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 16bff1b5708652a75ea603f596c864901b12a88d
ms.openlocfilehash: 60d4fec828d620d067b7eb9d0e3cb7e57d1be506


---
# <a name="nosql-tutorial-build-a-documentdb-c-console-application-on-net-core"></a>Руководство по NoSQL. Создание консольного приложения DocumentDB на языке C# для .NET Core
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Добро пожаловать в руководство по NoSQL, посвященное пакету SDK для .NET Core для Azure DocumentDB. После изучения этого учебника e вас будет консольное приложение, которое создает ресурсы DocumentDB и отправляет запросы к ним.

Мы рассмотрим следующие вопросы:

* создание учетной записи DocumentDB и подключение к ней;
* настройка решения Visual Studio;
* Создание оперативной базы данных
* создание коллекции;
* создание документов JSON;
* выполнение запросов к коллекции;
* замена документа;
* удаление документа;
* удаление базы данных.

У вас нет времени? Не беспокойтесь! Полное решение доступно на [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). Краткие инструкции см. в разделе [Получение полного решения NoSQL для этого руководства](#GetSolution).

После этого используйте кнопки голосования в верхней или нижней части этой страницы, чтобы отправить нам отзыв. Если вы хотите, чтобы мы связались с вами, укажите ваш электронный адрес в комментариях.

А теперь приступим к работе!

## <a name="prerequisites"></a>Предварительные требования
Убедитесь, что у вас есть указанные ниже компоненты.

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/). 
    * Кроме того, в этом руководстве можно использовать [эмулятор Azure DocumentDB](documentdb-nosql-local-emulator.md).
* [Visual Studio 2015 обновление 3](https://go.microsoft.com/fwlink/?LinkId=691129) и [.NET Core 1.0.1 — VS 2015 Tooling предварительная версия 2](https://go.microsoft.com/fwlink/?LinkID=827546).
    * Если вы работаете в MacOS или Linux, приложения .NET Core можно разрабатывать с помощью командной строки, установив для этого пакет [SDK для .NET Core ](https://www.microsoft.com/net/core#macos) в соответствии с платформой. 
    * Если вы работаете в Windows, приложения .NET Core можно разрабатывать с помощью командной строки, установив для этого пакет [SDK для .NET Core](https://www.microsoft.com/net/core#windows). 
    * Можно использовать свой редактор или скачать бесплатный [Visual Studio Code](https://code.visualstudio.com/), который работает в Windows, Linux и MacOS. 

## <a name="step-1-create-a-documentdb-account"></a>Этап 1: создание учетной записи DocumentDB
Создадим учетную запись DocumentDB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка решения Visual Studio](#SetupVS). Если вы используете эмулятор DocumentDB, выполните действия, описанные в статье об [эмуляторе Azure DocumentDB](documentdb-nosql-local-emulator.md), чтобы настроить эмулятор и сразу перейти к [настройке решения Visual Studio](#SetupVS).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idsetupvsastep-2-setup-your-visual-studio-solution"></a><a id="SetupVS"></a> Шаг 2. Настройка решения Visual Studio
1. Откройте **Visual Studio 2015** у себя на компьютере.
2. В меню **Файл** выберите пункт **Создать**, а затем — **Проект**.
3. В диалоговом окне **Новый проект** выберите **Шаблоны** / **Visual C#** / **.NET Core**/**Консольное приложение (.NET Core)**, а затем укажите имя проекта и нажмите кнопку **ОК**.
   ![Снимок экрана: диалоговое окно «Новый проект»](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. В **обозревателе решений**щелкните правой кнопкой мыши новое консольное приложение (оно находится в решении Visual Studio).
5. Не выходя из меню, выберите **Управление пакетами NuGet...**
   ![Снимок экрана с контекстным меню проекта](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. На вкладке **Nuget** щелкните **Обзор** и в поле поиска введите **azure documentdb**.
7. В результатах найдите **Microsoft.Azure.DocumentDB.Core** и нажмите кнопку **Установить**.
   Идентификатором пакета для клиентской библиотеки DocumentDB является [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core).

Отлично! Теперь, когда мы завершили настройку, начнем писать код. Вы можете найти проект готового кода для этого руководства в [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a name="a-idconnectastep-3-connect-to-a-documentdb-account"></a><a id="Connect"></a> Шаг 3. Подключение к учетной записи DocumentDB
Во-первых, добавьте эти ссылки в начало приложения C# в файле Program.cs:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> Для работы с этим руководством по NoSQL добавьте описанные выше зависимости.
> 
> 

Теперь добавьте эти две константы и переменную *client* в открытый класс *Program*.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUri = "<your endpoint URI>";
        private const string PrimaryKey = "<your key>";
        private DocumentClient client;

Затем войдите на [портал Azure](https://portal.azure.com) , чтобы получить URI и первичный ключ. Универсальный код ресурса (URI) DocumentDB и первичный ключ необходимы, чтобы предоставить приложению данные о расположении, в котором будет устанавливаться подключение, и сделать подключение вашего приложения доверенным для DocumentDB.

На портале Azure перейдите к учетной записи DocumentDB и щелкните **Ключи**.

Скопируйте универсальный код ресурса (URI) с портала и вставьте его в параметр `<your endpoint URI>` в файле program.cs. Затем скопируйте на портале значение поля "Первичный ключ" и вставьте его в параметр `<your key>`. Если вы используете эмулятор Azure DocumentDB, укажите `https://localhost:443` как конечную точку, а также примените четко определенный ключ авторизации, описанный в статье о [разработке с помощью эмулятора DocumentDB](documentdb-nosql-local-emulator.md).

![Снимок экрана портала Azure в ходе работы с руководством по NoSQL при создании консольного приложения C#. Отображена учетная запись DocumentDB со следующими выделенными элементами: активный концентратор, кнопка "Ключи" в колонке учетной записи DocumentDB, а также значения универсального кода ресурса, первичный и вторичный ключи в колонке "Ключи".][keys]

Мы запустим ознакомительное приложение, создав экземпляр **DocumentClient**.

В методе **Main** добавьте эту новую асинхронную задачу с именем **GetStartedDemo**, которая создает экземпляр **DocumentClient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
    }

Добавьте указанный далее код, чтобы запустить асинхронную задачу из метода **Main** . Метод **Main** будет перехватывать исключения и записывать их в консоли.

    static void Main(string[] args)
    {
            // ADD THIS PART TO YOUR CODE
            try
            {
                    Program p = new Program();
                    p.GetStartedDemo().Wait();
            }
            catch (DocumentClientException de)
            {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
            }
            catch (Exception e)
            {
                    Exception baseException = e.GetBaseException();
                    Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
            }
            finally
            {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
            }

Нажмите клавишу **F5** , чтобы запустить приложение.

Поздравляем! Вы подключились к учетной записи DocumentDB. Теперь рассмотрим принципы работы с ресурсами DocumentDB.  

## <a name="step-4-create-a-database"></a>Этап 4: создание базы данных
Прежде чем добавлять код для создания базы данных, добавьте вспомогательный метод для записи на консоль.

Скопируйте и вставьте метод **WriteToConsoleAndPromptToContinue** под кодом метода **GetStartedDemo**.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

[Базу данных](documentdb-resources.md#databases) DocumentDB можно создать с помощью метода [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) класса **DocumentClient**. База данных представляет собой логический контейнер для хранения документов JSON, разделенных между коллекциями.

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом создания клиента. Будет создана база данных с именем *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

Нажмите клавишу **F5** , чтобы запустить приложение.

Поздравляем! База данных DocumentDB создана.  

## <a name="a-idcreatecollastep-5-create-a-collection"></a><a id="CreateColl"></a>Этап 5: создание коллекции
> [!WARNING]
> С использованием элемента **CreateDocumentCollectionAsync** можно создать новую коллекцию с зарезервированной пропускной способностью и соответствующей ценой. Дополнительные сведения см. на нашей [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Вы можете создать [коллекцию](documentdb-resources.md#collections), используя метод [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) класса **DocumentClient**. Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript.

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом создания базы данных. Будет создана коллекция документов с именем *FamilyCollection_oa*.

        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

        // ADD THIS PART TO YOUR CODE
        await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

Нажмите клавишу **F5** , чтобы запустить приложение.

Поздравляем! Коллекция документов DocumentDB создана.  

## <a name="a-idcreatedocastep-6-create-json-documents"></a><a id="CreateDoc"></a>Шаг 6. Создание документов JSON
Вы можете создать [документ](documentdb-resources.md#documents) с помощью метода [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx), который относится к классу **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь мы можем добавить один или несколько документов. Если у вас уже есть данные, которые необходимо хранить в базе данных, можно использовать [средство миграции данных](documentdb-import-data.md)в DocumentDB.

Сначала необходимо создать класс **Family** , который будет представлять объекты, хранящиеся в DocumentDB в этом примере. Мы также создадим подклассы **Parent**, **Child**, **Pet** и **Address**, используемые в классе **Family**. Обратите внимание, документы должны иметь свойство **Id**, сериализуемое как **id** в файле JSON. Для этого после метода **GetStartedDemo** необходимо добавить следующие подклассы.

Скопируйте и вставьте классы **Family**, **Parent**, **Child**, **Pet** и **Address** под кодом метода **WriteToConsoleAndPromptToContinue**.

    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }

    // ADD THIS PART TO YOUR CODE
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
                return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

Скопируйте и вставьте метод **CreateFamilyDocumentIfNotExists** под кодом метода **CreateDocumentCollectionIfNotExists**.

    // ADD THIS PART TO YOUR CODE
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
            }
            else
            {
                throw;
            }
        }
    }

Затем вставьте два документа, один для семьи Андерсен и один для семьи Вейкфилд.

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом создания коллекции документов.

    await this.CreateDatabaseIfNotExists("FamilyDB_oa");

    await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO YOUR CODE
    Family andersenFamily = new Family
    {
            Id = "Andersen.1",
            LastName = "Andersen",
            Parents = new Parent[]
            {
                    new Parent { FirstName = "Thomas" },
                    new Parent { FirstName = "Mary Kay" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FirstName = "Henriette Thaulow",
                            Gender = "female",
                            Grade = 5,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Fluffy" }
                            }
                    }
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

    Family wakefieldFamily = new Family
    {
            Id = "Wakefield.7",
            LastName = "Wakefield",
            Parents = new Parent[]
            {
                    new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                    new Parent { FamilyName = "Miller", FirstName = "Ben" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FamilyName = "Merriam",
                            FirstName = "Jesse",
                            Gender = "female",
                            Grade = 8,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Goofy" },
                                    new Pet { GivenName = "Shadow" }
                            }
                    },
                    new Child
                    {
                            FamilyName = "Miller",
                            FirstName = "Lisa",
                            Gender = "female",
                            Grade = 1
                    }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

Нажмите клавишу **F5** , чтобы запустить приложение.

Поздравляем! Созданы два документа DocumentDB.  

![На схеме представлены иерархические отношения между учетной записью, оперативной базой данных, коллекцией и документами, используемыми в руководстве по NoSQL при создании консольного приложения C#.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a name="a-idqueryastep-7-query-documentdb-resources"></a><a id="Query"></a>Этап 7: запросы ресурсов DocumentDB
Служба DocumentDB поддерживает функционально богатые [запросы](documentdb-sql-query.md) документов JSON, хранящихся в каждой коллекции.  Ниже приведены примеры кода разнообразных запросов с использованием как синтаксиса SQL DocumentDB, так и LINQ, которые можно запускать на добавленных на предыдущем шаге документах.

Скопируйте и вставьте метод **ExecuteSimpleQuery** под кодом метода **CreateFamilyDocumentIfNotExists**.

    // ADD THIS PART TO YOUR CODE
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

            // Here we find the Andersen family via its LastName
            IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                    .Where(f => f.LastName == "Andersen");

            // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
            Console.WriteLine("Running LINQ query...");
            foreach (Family family in familyQuery)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            // Now execute the same query via direct SQL
            IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                    "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                    queryOptions);

            Console.WriteLine("Running direct SQL query...");
            foreach (Family family in familyQueryInSql)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом создания второго документа.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

Нажмите клавишу **F5** , чтобы запустить приложение.

Поздравляем! Вы использовали в запросе коллекцию DocumentDB.

В приведенной ниже схеме демонстрируется, как для созданной коллекции вызывается синтаксис запроса DocumentDB SQL (этот принцип действует и при работе с запросами LINQ).

![На схеме представлен масштаб и значение запроса, используемого в руководстве по NoSQL при создании консольного приложения C#.](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

Ключевое слово [FROM](documentdb-sql-query.md#from-clause) использовать в запросе необязательно, так как запросы DocumentDB заранее привязаны к одной коллекции. Поэтому слова "FROM Families f" можно заменить на "FROM root r" или любое другое имя переменной. Поведение службы DocumentDB будет таким, будто Families, root или любая другая переменная указывает на текущую коллекцию по умолчанию.

## <a name="a-idreplacedocumentastep-8-replace-json-document"></a><a id="ReplaceDocument"></a>Шаг 8. Замена документа JSON
DocumentDB поддерживает замену документов JSON.  

Скопируйте и вставьте метод **ReplaceFamilyDocument** под кодом метода **ExecuteSimpleQuery**.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
        try
        {
            await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
            this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом выполнения запроса. После замены документа тот же запрос будет запущен повторно, чтобы вы могли просмотреть измененный документ.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

Нажмите клавишу **F5** , чтобы запустить приложение.

Поздравляем! Документ DocumentDB заменен.

## <a name="a-iddeletedocumentastep-9-delete-json-document"></a><a id="DeleteDocument"></a>Шаг 9. Удаление документа JSON
DocumentDB поддерживает удаление документов JSON.  

Скопируйте и вставьте метод **DeleteFamilyDocument** под кодом метода **ReplaceFamilyDocument**.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        try
        {
            await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
            Console.WriteLine("Deleted Family {0}", documentName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом выполнения второго запроса.

    await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

Нажмите клавишу **F5** , чтобы запустить приложение.

Поздравляем! Документ DocumentDB удален.

## <a name="a-iddeletedatabaseastep-10-delete-the-database"></a><a id="DeleteDatabase"></a>Шаг 10. Удаление базы данных
Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (коллекций, документов и т. д.).

Скопируйте и вставьте приведенный код в метод **GetStartedDemo** под кодом удаления документа, чтобы удалить всю базу данных и все дочерние ресурсы.

    this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

    await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));

Нажмите клавишу **F5** , чтобы запустить приложение.

Поздравляем! База данных DocumentDB удалена.

## <a name="a-idrunastep-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Шаг 11. Запуск консольного приложения C#
Чтобы создать приложение в режиме отладки, откройте Visual Studio и нажмите клавишу F5.

Должны отобразиться выходные данные вашего приложения. Они должны содержать результаты обработки добавленных запросов. При этом выглядеть они должны примерно так, как показано в примере ниже.

    Created FamilyDB_oa
    Press any key to continue ...
    Created FamilyCollection_oa
    Press any key to continue ...
    Created Family Andersen.1
    Press any key to continue ...
    Created Family Wakefield.7
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Replaced Family Andersen.1
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Deleted Family Andersen.1
    End of demo, press any key to exit.

Поздравляем! Вы завершили работу с руководством по NoSQL и создали работающее консольное приложение C#.

## <a name="a-idgetsolutiona-get-the-complete-nosql-tutorial-solution"></a><a id="GetSolution"></a> Получение полного решения NoSQL для этого руководства
Чтобы собрать решение GetStarted, которое содержит все примеры из данной статьи, вам понадобится следующее:

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Учетная запись DocumentDB][documentdb-create-account].
* решение [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) , доступное в GitHub.

Чтобы в Visual Studio восстановить ссылки на пакет SDK для .NET Core для DocumentDB, в обозревателе решений щелкните правой кнопкой мыши решение **GetStarted**, а затем выберите пункт **Enable NuGet Package Restore** (Включить восстановление пакета NuGet). Затем в файле program.cs обновите значения EndpointUrl и AuthorizationKey согласно инструкциям в разделе о [подключении к учетной записи DocumentDB](#Connect).

## <a name="next-steps"></a>Дальнейшие действия
* Требуется более подробное руководство по NoSQL для ASP.NET MVC? См. статью [Руководство по ASP.NET MVC. Разработка веб-приложения с использованием DocumentDB](documentdb-dotnet-application.md).
* Хотите выполнить проверку масштабирования и производительности с помощью DocumentDB? См. статью [Проверка производительности и масштабирования с помощью Azure DocumentDB](documentdb-performance-testing.md).
* Узнайте, как [контролировать учетную запись DocumentDB](documentdb-monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
* Дополнительные сведения о модели программирования см. в разделе "Разработка" [на странице документации DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png



<!--HONumber=Jan17_HO1-->


