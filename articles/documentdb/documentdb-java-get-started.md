---
title: "Руководство по NoSQL. Пакет SDK для Java в Azure DocumentDB | Документация Майкрософт"
description: "Руководство по NoSQL, в котором создается оперативная база данных и консольное приложение Java с помощью пакета SDK для Java в DocumentDB. Azure DocumentDB — это база данных NoSQL для JSON."
keywords: "руководство nosql, оперативная база данных, консольное приложение java"
services: cosmosdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e54ca49e46b7e15e7adb306c5f578d8b53255010
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>Руководство по NoSQL. Создание консольного приложения DocumentDB на языке Java
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js для MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Добро пожаловать в руководство по NoSQL, посвященное пакету SDK для Java в Azure DocumentDB. После изучения этого учебника e вас будет консольное приложение, которое создает ресурсы DocumentDB и отправляет запросы к ним.

Мы рассмотрим:

* создание учетной записи Azure Cosmos DB и подключение к ней;
* настройка решения Visual Studio;
* Создание оперативной базы данных
* создание коллекции;
* создание документов JSON;
* выполнение запросов к коллекции;
* создание документов JSON;
* выполнение запросов к коллекции;
* замена документа;
* удаление документа;
* удаление базы данных.

А теперь приступим к работе!

## <a name="prerequisites"></a>Предварительные требования
Вам потребуются:

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/). Кроме того, в этом руководстве можно использовать [эмулятор Azure Cosmos DB](documentdb-nosql-local-emulator.md).
* [Git.](https://git-scm.com/downloads)
* [Комплект разработчика Java (JDK 7 +)](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Шаг 1. Создание учетной записи Azure Cosmos DB
Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к [клонированию проекта GitHub](#GitClone). При использовании эмулятора Azure Cosmos DB выполните действия, описанные в [этой статье](documentdb-nosql-local-emulator.md), чтобы настроить эмулятор и сразу перейти к [клонированию проекта GitHub](#GitClone).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="GitClone"></a>Этап 2. Клонирование проекта GitHub
[Чтобы приступить к работе с Azure Cosmos DB и Java](https://github.com/Azure-Samples/documentdb-java-getting-started), сначала клонируйте репозиторий GitHub. Например, чтобы получить образец проекта в локальной среде, в локальном каталоге выполните следующую команду.

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

В этом каталоге находится файл `pom.xml` проекта и папка `src` с исходным кодом Java. Кроме того, здесь также находится файл `Program.java` с инструкцией по выполнению простых операций с помощью Azure DocumentDB, таких как создание документов и запрос данных в коллекции. В файле `pom.xml` содержится зависимость от [пакета Java SDK для DocumentDB в Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Шаг 3. Подключение к учетной записи Azure Cosmos DB
Далее вернитесь на [портал Azure](https://portal.azure.com), чтобы получить конечную точку и первичный главный ключ. Конечная точка и первичный ключ Azure Cosmos DB позволяют приложению предоставлять данные о расположении, в котором будет устанавливаться подключение, делая подключение вашего приложения доверенным для Azure Cosmos DB.

На портале Azure перейдите к учетной записи Azure Cosmos DB, а затем щелкните **Ключи**. Скопируйте универсальный код ресурса (URI) с портала и вставьте его в параметр `<your endpoint URI>` в файле Program.java. Затем скопируйте на портале значение поля "Первичный ключ" и вставьте его в параметр `<your key>`.

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Снимок экрана портала Azure в ходе работы с руководством по NoSQL при создании консольного приложения Java. Отображается учетная запись Azure Cosmos DB со следующими выделенными элементами: активный концентратор, кнопка "Ключи" в колонке учетной записи Azure Cosmos DB, а также значение универсального кода ресурса (URI), первичный и вторичный ключи в колонке "Ключи".][keys]

## <a name="step-4-create-a-database"></a>Этап 4: создание базы данных
[Базу данных](documentdb-resources.md#databases) Azure Cosmos DB можно создать с помощью метода [createDatabase](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) класса **DocumentClient**. База данных представляет собой логический контейнер для хранения документов JSON, разделенных между коллекциями.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Этап 5: создание коллекции
> [!WARNING]
> Элемент **createCollection** создает коллекцию с зарезервированной пропускной способностью и соответствующей ценой. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Вы можете создать [коллекцию](documentdb-resources.md#collections), используя метод [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) класса **DocumentClient**. Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Шаг 6. Создание документов JSON
[Документ](documentdb-resources.md#documents) можно создать с помощью метода [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-) класса **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь мы можем добавить один или несколько документов. Если у вас уже есть данные, которые необходимо хранить в базе данных, вы можете использовать [средство миграции данных](documentdb-import-data.md) DocumentDB, чтобы импортировать данные в базу данных.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![На схеме представлены иерархические отношения между учетной записью, оперативной базой данных, коллекцией и документами, используемыми в руководстве по NoSQL при создании консольного приложения Java.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Шаг 7. Запрос ресурсов Azure Cosmos DB
Azure Cosmos DB поддерживает [полнофункциональные запросы](documentdb-sql-query.md) к документам JSON, хранящимся в каждой коллекции.  Ниже приведен образец кода, который позволяет запросить документы в Azure Cosmos DB, используя синтаксис SQL с методом [queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Шаг 8. Замена документа JSON
Azure Cosmos DB поддерживает обновление документов JSON с помощью метода [replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Шаг 9. Удаление документа JSON
Аналогичным образом Azure Cosmos DB поддерживает удаление документов JSON с помощью метода [deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Шаг 10. Удаление базы данных
Удаление созданной базы данных влечет удаление всех ее дочерних ресурсов (коллекций, документов и т. д.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Этап 11. Запуск консольного приложения Java
Чтобы запустить приложение в окне консоли, сначала скомпилируйте его с помощью Maven.
    
    mvn package

Команда `mvn package` позволяет скачать последнюю версию библиотеки Azure Cosmos DB из Maven и возвращает `GetStarted-0.0.1-SNAPSHOT.jar`. Затем запустите приложение, выполнив следующую команду:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Поздравляем! Вы завершили работу с руководством по NoSQL и создали работающее консольное приложение Java.

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о создании веб-приложения Java См. сведения в статье о [создании веб-приложения Java с использованием Azure Cosmos DB](documentdb-java-application.md).
* Узнайте, как выполнять [мониторинг учетной записи Azure Cosmos DB](documentdb-monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
* Дополнительные сведения о модели программирования см. в разделе "Разработка" [на странице документации Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

