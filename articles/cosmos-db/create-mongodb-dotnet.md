---
title: Создание веб-приложения с использованием .NET и API MongoDB в Azure Cosmos DB | Документация Майкрософт
description: В этой статье представлен пример кода .NET, который можно использовать для подключения и выполнения запросов к API MongoDB в Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/19/2018
ms.author: sngun
ms.openlocfilehash: ab14261e939063c5e50050774d1aae3edf1bef19
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-build-a-mongodb-api-web-app-with-net-and-the-azure-portal"></a>Azure Cosmos DB. Создание веб-приложения API MongoDB с использованием языка .NET и портала Azure

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB. 

В этом кратком руководстве показано, как c помощью портала Azure создать учетную запись [API MongoDB](mongodb-introduction.md) для Azure Cosmos DB, базу данных документов и коллекцию. Затем вы можете развернуть веб-приложение списка задач, созданное на основе [драйвера .NET MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Необходимые компоненты для запуска примера приложения

Чтобы запустить пример, вам понадобится [Visual Studio](https://www.visualstudio.com/downloads/) и действительная учетная запись Azure CosmosDB.

Если вы еще не установили Visual Studio 2017, скачайте [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) с установленной и настроенной рабочей нагрузкой **ASP.NET и веб-разработка**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-app"></a>Клонирования примера приложения

Сначала скачайте пример приложения API MongoDB из репозитория GitHub. Он реализует список задач с моделью хранения документов MongoDB.

1. Откройте окно терминала Git, например Git Bash, и выполните команду `cd`, чтобы перейти в рабочий каталог.
2. Выполните команду ниже, чтобы клонировать репозиторий с примером. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Если вы не хотите использовать Git, можно [скачать проект как ZIP-файл](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Просмотр кода

Сделаем краткий обзор того, что происходит в приложении. Откройте файл **Dal.cs** в каталоге **DAL**, и вы увидите, что эти строки кода создают ресурсы Azure Cosmos DB. 

* Инициализация клиента Mongo.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credentials = new List<MongoCredential>()
        {
            new MongoCredential("SCRAM-SHA-1", identity, evidence)
        };

        MongoClient client = new MongoClient(settings);
    ```

* Получение базы данных и коллекции.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Получение всех документов.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение.

1. На [портале Azure](http://portal.azure.com/) перейдите к учетной записи базы данных Azure Cosmos DB и на левой панели навигации щелкните **Строка подключения**, а затем выберите **Ключи записи-чтения**. На следующем шаге используйте кнопку копирования в правой части экрана, чтобы скопировать имя пользователя, пароль и узел в файл Dal.cs.

2. Откройте файл **Dal.cs** в каталоге **DAL**. 

3. Скопируйте **имя пользователя** c портала (с помощью кнопки копирования) и добавьте его в качестве значения параметра **username** в файле **Dal.cs**. 

4. Затем скопируйте значение **узла** с портала и добавьте его в качестве значения параметра **host** в файле **Dal.cs**. 

5. И наконец, скопируйте **пароль** с портала и добавьте его в качестве значения параметра **password** в файле **Dal.cs**. 

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 
    
## <a name="run-the-web-app"></a>Запуск веб-приложения

1. В Visual Studio в **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. 

2. В окне NuGet в поле **Обзор** введите *MongoDB.Driver*.

3. В результатах поиска найдите библиотеку **MongoDB.Driver** и установите ее. При этом будет установлен пакет MongoDB.Driver, а также все зависимости.

4. Нажмите клавиши CTRL+F5 для запуска приложения. Приложение откроется в браузере. 

5. Щелкните **Создать** в браузере и создайте несколько задач в приложении списка задач.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, сделав следующее:

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса. 
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как создать учетную запись Azure Cosmos DB и запустить веб-приложение, используя API MongoDB. Теперь можно импортировать дополнительные данные в учетную запись Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Перенос данных в DocumentDB с помощью mongoimport и mongorestore](mongodb-migrate.md)

