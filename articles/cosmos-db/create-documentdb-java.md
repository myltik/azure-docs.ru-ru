---
title: "Создание базы данных документов Azure Cosmos DB с помощью Java | Документация Майкрософт"
description: "В этой статье представлен пример кода Java, который можно использовать для подключения и выполнения запросов к API DocumentDB в Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 08/02/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: df1a25d703a7b8082bdabb4f7d593cb005d416fe
ms.contentlocale: ru-ru
ms.lasthandoff: 08/17/2017

---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: создание базы данных документов с помощью Java и портала Azure

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования Azure Cosmos DB. 

Выполнив инструкции в этом кратком руководстве, вы создадите базу данных документов с помощью средств портала Azure для Azure Cosmos DB. Из этого краткого руководства вы также узнаете, как быстро создать консольное приложение Java с помощью [интерфейса API для DocumentDB на Java](documentdb-sdk-java.md). Указания в этом руководстве применимы к любой операционной системе, с которой может работать Java. Выполнив инструкции в руководстве, вы узнаете, как создавать и изменять ресурсы базы данных документов с помощью пользовательского интерфейса или программных средств.

## <a name="prerequisites"></a>Предварительные требования

* [Комплект разработчика Java (JDK 1.7+)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив [Maven](http://maven.apache.org/).
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git.](https://www.git-scm.com/)
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Перед созданием базы данных документов необходимо создать учетную запись базы данных SQL (DocumentDB) с Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Добавление коллекции

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Добавление демонстрационных данных

Теперь вы можете добавить данные в новую коллекцию с помощью обозревателя данных.

1. В обозревателе данных новая база данных отображается в области "Коллекции". Разверните базу данных **Задачи** и коллекцию **Элементы**, выберите **Документы**, а затем щелкните **Новые документы**. 

   ![Создание документов в обозревателе данных на портале Azure](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. Теперь можно добавить документ в коллекцию со следующей структурой:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Добавив данные JSON на вкладке **Документы**, щелкните **Сохранить**.

    ![Копирование данных JSON и нажатие кнопки "Сохранить" в обозревателе данных на портале Azure](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4.  Создайте и сохраните один или несколько документов, задав уникальное значение для свойства `id`, и измените другие свойства по своему усмотрению. Новые документы могут иметь любую структуру, так как Azure Cosmos DB не устанавливает определенные схемы данных.

     Теперь можно выполнять запросы в обозревателе данных и получать данные при помощи кнопок **Изменить фильтр** и **Применить фильтр**. Чтобы получить все документы в коллекции, обозреватель данных по умолчанию использует запрос `SELECT * FROM c`. Но вы можете изменить его на другой [запрос SQL](documentdb-sql-query.md), например `SELECT * FROM c ORDER BY c._ts DESC`, который возвращает все документы в убывающем порядке по метке времени. 
 
     В обозревателе данных также можно создавать хранимые процедуры, триггеры и определяемые пользователем функции, чтобы реализовать бизнес-логику на стороне сервера и масштабировать пропускную способность. Это средство позволяет использовать все встроенные возможности программного доступа к данным, доступные в API-интерфейсах, к которым вы можете быстро получить доступ на портале Azure.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом. Давайте клонируем приложение API DocumentDB из GitHub, зададим строку подключения и выполним ее. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте окно терминала Git, например Git Bash, и выполните команду `CD`, чтобы перейти в рабочий каталог.  

2. Выполните команду ниже, чтобы клонировать репозиторий с примером. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>Просмотр кода

Сделаем краткий обзор того, что происходит в приложении. Откройте файл `Program.java` из папки \src\GetStarted. Вы увидите строки кода, из которых создаются ресурсы Azure Cosmos DB. 

* Инициализация `DocumentClient`.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* Создание базы данных.

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* Создание коллекции.

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Создание нескольких документов.

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* Выполнение запроса SQL через JSON.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение. Они обеспечат связь вашего приложения с размещенной базой данных.

1. На [портале Azure](http://portal.azure.com/) перейдите к учетной записи базы данных Azure Cosmos DB и на левой панели навигации щелкните **Ключи**, а затем выберите **Ключи записи-чтения**. На следующем шаге используйте кнопку копирования в правой части экрана, чтобы скопировать универсальный код ресурса (URI) и ПЕРВИЧНЫЙ КЛЮЧ в файл `Program.java`.

    ![Просмотр и копирование ключа доступа на портале Azure, колонка "Ключи"](./media/create-documentdb-dotnet/keys.png)

2. Открыв файл `Program.java`, скопируйте значение URI на портале (с помощью кнопки копирования) и добавьте его в качестве значения конечной точки в конструктор DocumentClient в файле `Program.java`. 

    `"https://FILLME.documents.azure.com"`

4. Затем скопируйте значение ПЕРВИЧНОГО КЛЮЧА на портале и вставьте его в качестве значения второго параметра FILLME в конструктор DocumentClient. Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 
    
## <a name="run-the-app"></a>Запуск приложения

1. В окне терминала git перейдите к папке azure-cosmos-db-documentdb-java-getting-started при помощи команды `cd`.

2. В окне терминала git введите команду `mvn package`, чтобы установить необходимые пакеты Java.

3. В окне терминала git выполните команду `mvn exec:java -D exec.mainClass=GetStarted.Program`, чтобы запустить приложение Java.

    В окне терминала отобразится уведомление о том, что база данных FamilyDB создана, и вам будет предложено нажать клавишу для продолжения. Нажмите клавишу, чтобы создать базу данных. Затем перейдите в обозреватель данных, и убедитесь, что в нем теперь содержится база данных FamilyDB. Продолжайте нажимать клавиши, чтобы создать коллекцию и документы, а затем выполнить запрос. После реализации проекта ресурсы будут удалены из вашей учетной записи. 

    ![Просмотр и копирование ключа доступа на портале Azure, колонка "Ключи"](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, сделав следующее:

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса. 
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы узнали, как создать учетную запись Azure Cosmos DB, базу данных документов и коллекцию с помощью обозревателя данных, а также как запустить приложение, чтобы эти операции выполнялись программой. Теперь можно импортировать дополнительные данные в учетную запись Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Импорт данных в DocumentDB с помощью средства миграции базы данных](import-data.md)



