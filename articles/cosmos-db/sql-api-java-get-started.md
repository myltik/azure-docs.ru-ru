---
title: Руководство по NoSQL. Пакет SDK для создания приложения Java с помощью API-интерфейса SQL для Azure Cosmos DB | Документация Майкрософт
description: Руководство по NoSQL, в котором создается оперативная база данных и консольное приложение Java с помощью API-интерфейса SQL для Azure Cosmos DB. Azure SQL — это база данных NoSQL для JSON.
keywords: руководство nosql, оперативная база данных, консольное приложение java
services: cosmos-db
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/22/2017
ms.author: arramac
ms.openlocfilehash: 5052c3cdeabd5001c6d0144dc77401a9495ba887
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>Руководство по NoSQL. Создание консольного приложения Java с помощью API-интерфейса SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js для MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Приветствуем вас в разделе руководства по NoSQL, посвященного пакету SDK для создания консольного приложения Java с помощью API-интерфейса SQL для Azure Cosmos DB. После изучения этого руководства у вас будет консольное приложение, которое создает ресурсы Azure Cosmos DB и отправляет запросы к ним.

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

## <a name="prerequisites"></a>предварительным требованиям
Вам потребуются:

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Комплект разработчика Java (JDK 7 +)](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Шаг 1. Создание учетной записи Azure Cosmos DB
Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к [клонированию проекта GitHub](#GitClone). При использовании эмулятора Azure Cosmos DB выполните действия, описанные в [этой статье](local-emulator.md), чтобы настроить эмулятор и сразу перейти к [клонированию проекта GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Этап 2. Клонирование проекта GitHub
[Чтобы приступить к работе с Azure Cosmos DB и Java](https://github.com/Azure-Samples/documentdb-java-getting-started), сначала клонируйте репозиторий GitHub. Например, чтобы получить образец проекта в локальной среде, в локальном каталоге выполните следующую команду.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

В этом каталоге находится файл проекта `pom.xml` и папка с исходным кодом Java, `src`. Кроме того, здесь также находится файл `Program.java` с инструкциями по выполнению простых операций с Azure Cosmos DB, таких как создание документов и запрос данных в коллекции. В файле `pom.xml` содержится зависимость от [пакета Java SDK для Azure Cosmos DB в Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Шаг 3. Подключение к учетной записи Azure Cosmos DB
Далее вернитесь на [портал Azure](https://portal.azure.com), чтобы получить конечную точку и первичный главный ключ. Конечная точка и первичный ключ Azure Cosmos DB позволяют приложению предоставлять данные о расположении, в котором будет устанавливаться подключение, делая подключение вашего приложения доверенным для Azure Cosmos DB.

На портале Azure перейдите к учетной записи Azure Cosmos DB, а затем щелкните **Ключи**. Скопируйте универсальный код ресурса (URI) с портала и вставьте его в параметр `https://FILLME.documents.azure.com` в файле Program.java. Затем скопируйте на портале значение поля "Первичный ключ" и вставьте его в параметр `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Снимок экрана портала Azure в ходе работы с руководством по NoSQL при создании консольного приложения Java. Отображается учетная запись Azure Cosmos DB со следующими выделенными элементами: активный концентратор, кнопка "Ключи" в колонке учетной записи Azure Cosmos DB, а также значение универсального кода ресурса (URI), первичный и вторичный ключи в колонке "Ключи".][keys]

## <a name="step-4-create-a-database"></a>Этап 4: создание базы данных
[Базу данных](sql-api-resources.md#databases) Azure Cosmos DB можно создать с помощью метода [createDatabase](/java/api/com.microsoft.azure.documentdb._document_client.createdatabase) класса **DocumentClient**. База данных представляет собой логический контейнер для хранения документов JSON, разделенных между коллекциями.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Этап 5: создание коллекции
> [!WARNING]
> Элемент **createCollection** создает коллекцию с зарезервированной пропускной способностью и соответствующей ценой. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Вы можете создать [коллекцию](sql-api-resources.md#collections), используя метод [createCollection](/java/api/com.microsoft.azure.documentdb._document_client.createcollection) класса **DocumentClient**. Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Шаг 6. Создание документов JSON
[Документ](sql-api-resources.md#documents) можно создать с помощью метода [createDocument](/java/api/com.microsoft.azure.documentdb._document_client.createdocument) класса **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь мы можем добавить один или несколько документов. Если у вас уже есть данные, которые вы хотите хранить в базе данных, вы можете использовать [средство миграции данных](import-data.md) Azure Cosmos DB, чтобы импортировать эти данные в базу данных.

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

![На схеме представлены иерархические отношения между учетной записью, оперативной базой данных, коллекцией и документами, используемыми в руководстве по NoSQL при создании консольного приложения Java.](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Шаг 7. Запрос ресурсов Azure Cosmos DB
Azure Cosmos DB поддерживает [полнофункциональные запросы](sql-api-sql-query.md) к документам JSON, хранящимся в каждой коллекции.  Ниже приведен образец кода, который позволяет запросить документы в Azure Cosmos DB, используя синтаксис SQL с методом [queryDocuments](/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Шаг 8. Замена документа JSON
Azure Cosmos DB поддерживает обновление документов JSON с помощью метода [replaceDocument](/java/api/com.microsoft.azure.documentdb._document_client.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Шаг 9. Удаление документа JSON
Аналогичным образом Azure Cosmos DB поддерживает удаление документов JSON с помощью метода [deleteDocument](/java/api/com.microsoft.azure.documentdb._document_client.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Шаг 10. Удаление базы данных
Удаление созданной базы данных влечет удаление всех ее дочерних ресурсов (коллекций, документов и т. д.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Этап 11. Запуск консольного приложения Java
Чтобы запустить приложение из консоли, перейдите в папку проекта и выполните компиляцию с помощью Maven:
    
    mvn package

Команда `mvn package` позволяет скачать последнюю версию библиотеки Azure Cosmos DB из Maven и возвращает `GetStarted-0.0.1-SNAPSHOT.jar`. Затем запустите приложение, выполнив следующую команду:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Поздравляем! Вы завершили работу с руководством по NoSQL и создали работающее консольное приложение Java.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о создании веб-приложения Java См. сведения в статье о [создании веб-приложения Java с использованием Azure Cosmos DB](sql-api-java-application.md).
* Узнайте, как выполнять [мониторинг учетной записи Azure Cosmos DB](monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
