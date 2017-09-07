---
title: "Создание приложения .NET Azure Cosmos DB с помощью API Graph | Документация Майкрософт"
description: "В этой статье представлен пример кода .NET, который можно использовать для подключения и выполнения запросов к Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/28/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 12c9bf626de8738fac95bd41965b0a2bf8758ed2
ms.contentlocale: ru-ru
ms.lasthandoff: 09/02/2017

---
# <a name="azure-cosmos-db-build-a-net-application-using-the-graph-api"></a>Azure Cosmos DB. Создание приложения .NET с помощью API Graph

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования Azure Cosmos DB. 

В этом кратком руководстве показано, как создать учетную запись Azure Cosmos DB, базу данных и граф (контейнер) с помощью портала Azure. Затем вы создадите и запустите консольное приложение на базе [API Graph](graph-sdk-dotnet.md) (предварительная версия).  

## <a name="prerequisites"></a>Предварительные требования

Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать **бесплатный** [выпуск Community для Visual Studio 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Добавление графа

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение API Graph из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте окно терминала Git, например Git Bash, и выполните команду `cd`, чтобы перейти в рабочий каталог.  

2. Выполните команду ниже, чтобы клонировать репозиторий с примером. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. Затем откройте файл решения в Visual Studio. 

## <a name="review-the-code"></a>Просмотр кода

Сделаем краткий обзор того, что происходит в приложении. Откройте файл Program.cs, и вы увидите, что эти строки кода создают ресурсы Azure Cosmos DB. 

* Инициализация экземпляра DocumentClient. В предварительной версии мы добавили API расширения графа в клиент Azure Cosmos DB. Мы работаем над автономным клиентом графа, не связанным с клиентом Azure Cosmos DB и ресурсами.

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* Создание базы данных.

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* Создание графа.

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* Выполнение шагов Gremlin с использованием метода `CreateGremlinQuery`.

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение.

1. В Visual Studio 2017 откройте файл App.config. 

2. На портале Azure в учетной записи Azure Cosmos DB щелкните **Ключи** в левой области навигации. 

    ![Просмотр и копирование первичного ключа на странице "ключи" портала Azure](./media/create-graph-dotnet/keys.png)

3. Скопируйте значение **URI** из портала и добавьте его в качестве значения параметра ключа конечной точки в файле App.config. Как показано на предыдущем снимке экрана, вы можете использовать кнопку "Копировать" для копирования значения.

    `<add key="Endpoint" value="https://FILLME.documents.azure.com:443" />`

4. Скопируйте значение **первичного ключа** с портала и добавьте его в качестве значения параметра ключа AuthKey в App.config, а затем сохраните изменения. 

    `<add key="AuthKey" value="FILLME" />`

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

## <a name="run-the-console-app"></a>Запуск консольного приложения

1. В Visual Studio в **обозревателе решений** щелкните проект **GraphGetStarted** правой кнопкой мыши и выберите **Управление пакетами NuGet**. 

2. В NuGet в поле **обзора** введите *Microsoft.Azure.Graphs* и установите флажок для поля **Includes prerelease** (Включить предварительный выпуск). 

3. В результатах поиска выберите библиотеку **Microsoft.Azure.Graphs** и установите ее. При этом установится пакет библиотеки расширений графов Azure Cosmos DB и все зависимые компоненты.

    Если появится сообщение о просмотре изменений в решении, нажмите кнопку **ОК**. Если появится сообщение о принятии условий лицензионного соглашения, щелкните **Принимаю**.

4. Нажмите клавиши CTRL+F5 для запуска приложения.

   В окне консоли появятся вершины и ребра, добавляемые в граф. После завершения сценария закройте окно консоли, дважды нажав клавишу ВВОД. 

## <a name="browse-using-the-data-explorer"></a>Просмотр с помощью обозревателя данных

Теперь вернитесь в обозреватель данных на портале Azure. Здесь вы можете просмотреть и запросить новые данные графа.

1. В обозревателе данных новая база данных отображается в области "Графы". Разверните **graphdb** и **graphcollz**, а затем щелкните **График**.

2. Нажмите кнопку **Применить фильтр**, чтобы использовать запрос по умолчанию для просмотра всех вершин графа. Данные, созданные в примере приложения, отображаются на панели Graphs (Графы).

    Вы можете масштабировать граф, развернуть отображаемое пространство, добавить дополнительные вершины и переместить их на поверхность отображения.

    ![Просмотр графа в обозревателе данных на портале Azure](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, сделав следующее: 

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса. 
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы узнали, как создать учетную запись Azure Cosmos DB, граф с помощью обозревателя данных, а также как запустить приложение. Теперь вы можете создавать более сложные запросы и внедрять эффективную логику обхода графа с помощью Gremlin. 

> [!div class="nextstepaction"]
> [Как выполнять запросы к данным в базе данных Azure Cosmos DB с помощью API Graph (предварительная версия)](tutorial-query-graph.md)


