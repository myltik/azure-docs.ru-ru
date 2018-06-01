---
title: Azure Cosmos DB. Разработка с помощью API Graph на .NET | Документация Майкрософт
description: Сведения о разработке с помощью API SQL службы Azure Cosmos DB на языке .NET
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: ''
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 1843e37d9baf1ab264db96109eb5ffd0704e35b7
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271295"
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB. Разработка с помощью API Graph на .NET
Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB. 

В этом руководстве показано, как создать учетную запись базы данных Azure Cosmos DB на портале Azure, а также базу данных графа и контейнер. Потом приложение создает обычную социальную сеть с четырьмя пользователями с помощью [API Graph](graph-sdk-dotnet.md), а затем обходит и запрашивает граф с использованием Gremlin.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos DB; 
> * создание базы данных графа и контейнера;
> * сериализация вершин и ребер в объекты .NET;
> * добавление вершин и ребер;
> * запрос графа с помощью Gremlin.

## <a name="graphs-in-azure-cosmos-db"></a>Графы в базе данных Azure Cosmos DB
Вы можете использовать базу данных Azure Cosmos DB, чтобы создавать, обновлять и запрашивать графы с помощью библиотеки [Microsoft.Azure.Graphs](graph-sdk-dotnet.md). Библиотека Microsoft.Azure.Graph предоставляет отдельный метод расширения `CreateGremlinQuery<T>` на базе класса `DocumentClient` для выполнения запросов Gremlin.

Gremlin — это функциональный язык программирования, поддерживающий операции записи (DML) и операции запросов и обхода. В этой статье мы рассмотрим несколько примеров, чтобы приступить к работе с Gremlin. Подробное пошаговое руководство о возможностях Gremlin, доступных в базе данных Azure Cosmos DB, см. в статье [Azure Cosmos DB Gremlin graph support](gremlin-support.md) (Поддержка графа Gremlin в базе данных Azure Cosmos DB). 

## <a name="prerequisites"></a>предварительным требованиям
Убедитесь, что у вас есть указанные ниже компоненты.

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/). 
    * Кроме того, в этом руководстве можно использовать [локальный эмулятор](local-emulator.md).
* [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Создание учетной записи базы данных

Сначала создадим учетную запись базы данных Azure Cosmos DB на портале Azure.  

> [!TIP]
> * Уже есть учетная запись Azure Cosmos DB? В таком случае перейдите к [настройке решения Visual Studio](#SetupVS).
> * Если вы используете эмулятор Azure Cosmos DB, выполните действия, описанные в статье об [эмуляторе Azure Cosmos DB](local-emulator.md), чтобы настроить эмулятор и сразу перейти к [настройке решения Visual Studio](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Настройка решения Visual Studio
1. Откройте **Visual Studio** у себя на компьютере.
2. В меню **Файл** выберите пункт **Создать**, а затем — **Проект**.
3. В диалоговом окне **Новый проект** выберите **Шаблоны** / **Visual C#** / **Консольное приложение (.NET Framework)**, а затем укажите имя проекта и нажмите кнопку **ОК**.
4. В **обозревателе решений** щелкните правой кнопкой мыши новое консольное приложение (оно находится в решении Visual Studio). Затем щелкните **Управление пакетами NuGet...**
5. На вкладке **NuGet** щелкните **Обзор** и в поле поиска введите **Microsoft.Azure.Graphs**. Затем установите флажок **Include prerelease versions** (Включить предварительные версии).
6. В результатах найдите **Microsoft.Azure.Graphs** и щелкните **Установить**.
   
   Если появится сообщение о просмотре изменений в решении, нажмите кнопку **ОК**. Если появится сообщение о принятии условий лицензионного соглашения, щелкните **Принимаю**.
   
    Библиотека `Microsoft.Azure.Graphs` предоставляет отдельный метод расширения `CreateGremlinQuery<T>` для выполнения операций Gremlin. Gremlin — это функциональный язык программирования, поддерживающий операции записи (DML) и операции запросов и обхода. В этой статье мы рассмотрим несколько примеров, чтобы приступить к работе с Gremlin. Подробное пошаговое руководство о возможностях Gremlin, доступных в базе данных Azure Cosmos DB, см. в статье [Azure Cosmos DB Gremlin graph support](gremlin-support.md) (Поддержка графа Gremlin в базе данных Azure Cosmos DB).

## <a id="add-references"></a>Подключение приложения

Добавьте эти две константы и переменную *client* в приложение. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Далее вернитесь на [портал Azure](https://portal.azure.com), чтобы получить URL-адрес конечной точки и первичный ключ. URL-адрес конечной точки и первичный ключ позволяют приложению предоставлять данные о расположении, в котором будет устанавливаться подключение, делая подключение вашего приложения доверенным для Azure Cosmos DB. 

На портале Azure перейдите к учетной записи Azure Cosmos DB, щелкните **Ключи**, а затем выберите **Ключи записи-чтения**. 

Скопируйте универсальный код ресурса (URI) с портала и вставьте его в параметр `Endpoint` в свойстве конечной точки, указанном выше. Затем скопируйте на портале значение поля "Первичный ключ" и вставьте его в приведенное выше свойство `AuthKey`. 

![Снимок экрана портала Azure в ходе работы с руководством при создании приложения C#. Отображена учетная запись базы данных Azure Cosmos DB со следующими выделенными элементами: кнопка "Ключи" в области навигации базы данных Azure Cosmos DB, а также значения URI и первичного ключа в колонке "Ключи"](./media/tutorial-develop-graph-dotnet/keys.png) 
 
## <a id="instantiate"></a>Создание экземпляра DocumentClient 
Создайте экземпляр **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Создание базы данных 

Теперь создайте [базу данных](sql-api-resources.md#databases) Azure Cosmos DB с помощью метода [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) или [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) класса **DocumentClient** из [пакета SDK .NET для SQL](sql-api-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Создание графа 

Создайте контейнер графа с помощью метода [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) или [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) класса **DocumentClient**. Коллекция — это контейнер сущностей графа. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 400 }); 
``` 

## <a id="serializing"></a>Сериализация вершин и ребер в объекты .NET
База данных Azure Cosmos DB использует [формат подключения GraphSON](gremlin-support.md), который определяет схему JSON для вершин, ребер и свойств. Пакет SDK для .NET базы данных Azure Cosmos DB включает JSON.NET в качестве зависимости. Это позволяет сериализовать и десериализировать объекты GraphSON в объекты .NET, с которыми можно работать в коде.

В качестве примера возьмем обычную социальную сеть с четырьмя пользователями. Мы рассмотрим создание вершин `Person`, добавление отношений `Knows` между ними, а также выполнение запроса и обхода графа для поиска дружеских связей. 

Пространство имен `Microsoft.Azure.Graphs.Elements` предоставляет классы `Vertex`, `Edge`, `Property` и `VertexProperty` для десериализации ответов GraphSON в четко определенные объекты .NET.

## <a name="run-gremlin-using-creategremlinquery"></a>Запуск Gremlin с помощью CreateGremlinQuery
Язык Gremlin, как и SQL, поддерживает операции чтения, записи и запроса. Например, в фрагменте ниже показано, как создать вершины, ребра, выполнить некоторые примеры запросов, используя `CreateGremlinQuery<T>`, а также как выполнить асинхронную итерацию по этим результатам с помощью `ExecuteNextAsync` и `HasMoreResults`.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Добавление вершин и ребер

Давайте более подробно рассмотрим инструкции Gremlin, приведенные в предыдущем разделе. Сначала добавим некоторые вершины с помощью метода Gremlin `addV`. Например, фрагмент кода ниже создает вершину Thomas Andersen типа Person со свойствами имени и возраста.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas').property('age', 44)");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Затем мы создадим некоторые ребра между этими вершинами с помощью метода Gremlin `addE`. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Имеющуюся вершину можно обновить, используя шаг `properties` в Gremlin. Мы пропустим вызов на выполнение запроса через `HasMoreResults` и `ExecuteNextAsync` для остальных примеров.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Вы можете удалять ребра и вершины с помощью шага Gremlin `drop`. Во фрагменте кода ниже показано, как удалить вершину и ребро. Обратите внимание, что удаление вершины приведет к каскадному удалению связанных ребер.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>Запрос графа

С помощью Gremlin также можно выполнять запросы и обходы графа. Например, в следующем фрагменте показано, как подсчитать количество вершин в графе:

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Вы можете выполнять фильтрацию с помощью шагов Gremlin `has` и `hasLabel`, а также объединять их с помощью операторов `and`, `or` и `not` для создания более сложных фильтров.

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Вы можете проецировать некоторые свойства в результатах запроса с помощью шага `values`:

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Пока мы видели только операторы запросов, которые работают в любой базе данных. Графы способны быстро и эффективно выполнять операции обхода, когда вам необходимо перейти к связанным ребрам и вершинам. Давайте найдем всех друзей Томаса. Мы сделаем это с помощью шага Gremlin `outE`, чтобы найти все исходящие от Томаса ребра, а затем переместимся к вершинам исходящих ребер с помощью шага Gremlin `inV`:

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

Следующий запрос выполняет два прыжка, чтобы найти всех друзей друзей Томаса, вызвав `outE` и `inV` два раза. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Вы можете создавать более сложные запросы и внедрять эффективную логику обхода графа с помощью Gremlin, включая сочетание выражений фильтров, выполнение цикла с помощью шага `loop` и реализацию условной навигации с помощью шага `choose`. Дополнительные сведения о возможностях, допустимых благодаря поддержке Gremlin, см. в [этой статье](gremlin-support.md).

Вот и все! С руководством по базе данных Azure Cosmos DB вы ознакомлены. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, выполнив следующие действия.  

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса. 
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * создали учетную запись Azure Cosmos DB; 
> * создали базу данных графа и контейнер;
> * сериализировали вершины и ребра в объекты .NET;
> * добавили вершины и ребра;
> * выполнили запрос графа с помощью Gremlin.

Теперь вы можете создавать более сложные запросы и внедрять эффективную логику обхода графа с помощью Gremlin. 

> [!div class="nextstepaction"]
> [Как выполнять запросы к данным в базе данных Azure Cosmos DB с помощью API Graph (предварительная версия)](tutorial-query-graph.md)
