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
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: mimig
ms.openlocfilehash: b36de6bce597569b4e1eaa615860acdf28dfa798
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2017
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: создание базы данных документов с помощью Java и портала Azure

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. С помощью Azure Cosmos DB вы можете быстро создавать базы данных управляемых документов, таблиц и диаграмм и обращаться к ним.

Выполнив инструкции в этом кратком руководстве, вы создадите базу данных документов с помощью средств портала Azure для Azure Cosmos DB. Из этого краткого руководства вы также узнаете, как быстро создать консольное приложение Java с помощью [интерфейса API для DocumentDB на Java](documentdb-sdk-java.md). Указания в этом руководстве применимы к любой операционной системе, с которой может работать Java. Выполнив инструкции в этом руководстве, вы узнаете, как создавать и изменять ресурсы базы данных документов с помощью пользовательского интерфейса или программных средств.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Кроме того, сделайте следующее: 

* [Комплект разработчика Java (JDK 1.7+)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив [Maven](http://maven.apache.org/).
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git.](https://www.git-scm.com/)
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Перед созданием базы данных документов необходимо создать учетную запись базы данных SQL (DocumentDB) с Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Добавление коллекции

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Добавление демонстрационных данных

Теперь вы можете добавить данные в новую коллекцию с помощью обозревателя данных.

1. Разверните коллекцию **Элементы**, щелкните **Документы** > **Новый документ**.

   ![Создание документов в обозревателе данных на портале Azure](./media/create-documentdb-java/azure-cosmosdb-data-explorer-new-document.png)
  
2. Теперь добавьте в коллекцию документ со описанной ниже структурой и щелкните **Сохранить**.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

    ![Копирование данных JSON и нажатие кнопки "Сохранить" в обозревателе данных на портале Azure](./media/create-documentdb-java/azure-cosmosdb-data-explorer-save-document.png)

3.  Создайте и сохраните еще один документ, изменив в нем `id` на значение 2. Остальные свойства вы тоже можете изменить по своему усмотрению. Новые документы могут иметь любую структуру, так как Azure Cosmos DB не устанавливает определенные схемы данных.

## <a name="query-your-data"></a>Обращение к данным

Теперь вы можете применить запросы в обозревателе данных для получения и фильтрации данных.

1. Как вы видите, по умолчанию выбран запрос `SELECT * FROM c`. Этот запрос по умолчанию извлекает и отображает все документы в коллекции. 

    ![Стандартный запрос SELECT * FROM c в обозревателе данных](./media/create-documentdb-java/azure-cosmosdb-data-explorer-query.png)

2. Чтобы изменить запрос, нажмите кнопку **Изменить фильтр** и добавьте `ORDER BY c._ts DESC` в поле предиката запроса, а затем выберите действие **Применить фильтр**.

    ![Изменение запроса по умолчанию с добавлением предиката ORDER BY c._ts DESC и применение фильтра](./media/create-documentdb-java/azure-cosmosdb-data-explorer-edit-query.png)

Новый запрос перечисляет все документы в порядке убывания метки времени, поэтому теперь первым в списке будет второй документ. Если вы знакомы с синтаксисом SQL, используйте в этом поле любой поддерживаемый [SQL-запрос](documentdb-sql-query.md). 

На этом мы завершим работу с обозревателем данных. Прежде чем продолжить работу с кодом, мы хотим напомнить вам вот о чем. В обозревателе данных вы можете создавать хранимые процедуры, триггеры и определяемые пользователем функции, чтобы реализовать бизнес-логику на стороне сервера и масштабировать пропускную способность. Это средство позволяет использовать все встроенные возможности программного доступа к данным, доступные в API-интерфейсах, к которым вы можете быстро получить доступ на портале Azure.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом. Давайте клонируем приложение API DocumentDB из GitHub, зададим строку подключения и выполним ее. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте командную строку, создайте папку git-samples, а затем закройте окно командной строки.

    ```bash
    md "C:\git-samples"
    ```

2. Откройте окно терминала git, например git bash, и выполните команду `cd`, чтобы перейти в новую папку для установки примера приложения. 

    ```bash
    cd "C:\git-samples"
    ```

3. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>Просмотр кода

Этот шаг не является обязательным. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Они взяты из файла `Program.java`, который устанавливается в папку C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted. Если вас это не интересует, можете сразу переходить к разделу [Обновление строки подключения](#update-your-connection-string). 

* Инициализация `DocumentClient`. [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) является логическим представлением службы баз данных Azure Cosmos DB на стороне клиента. Этот клиент позволяет настраивать и выполнять запросы к службе.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* Создание [Database](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._database).

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* Создание [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection).

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Создание документа с помощью метода [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument).

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* SQL-запросы к JSON можно выполнить с помощью метода [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

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

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение. Так вы обеспечите обмен данными между приложением и размещенной базой данных.

1. На [портале Azure](http://portal.azure.com/) щелкните **Ключи**. 

    Используйте кнопки копирования в правой части экрана, чтобы скопировать верхнее значение URI.

    ![Просмотр и копирование ключа доступа на портале Azure, страница "Ключи"](./media/create-documentdb-java/keys.png)

2. Откройте файл `Program.java` из папки C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted. 

3. Вставьте полученное на портале значение URI над элементом `https://FILLME.documents.azure.com` в строке 45.

4. Вернитесь на портал и скопируйте значение первичного ключа, как показано на этом снимке экрана. Вставьте полученное на портале значение первичного ключа над элементом `FILLME` в строке 46.

    Метод getStartedDemo теперь должен выглядеть примерно так: 
    
    ```java
    private void getStartedDemo() throws DocumentClientException, IOException {
        this.client = new DocumentClient("https://youraccountname.documents.azure.com:443/",
                "your-primary-key...RJhQrqQ5QQ==", 
                new ConnectionPolicy(),
                ConsistencyLevel.Session);
    ```

5. Сохраните файл Program.java.

## <a name="run-the-app"></a>Запуск приложения

1. В окне терминала git перейдите к папке azure-cosmos-db-documentdb-java-getting-started при помощи команды `cd`.

    ```git
    cd "C:\git-samples\azure-cosmos-db-documentdb-java-getting-started"
    ```

2. В окне терминала Git введите приведенную ниже команду, чтобы установить необходимые пакеты Java.

    ```
    mvn package
    ```

3. В окне терминала Git выполните следующую команду, чтобы запустить приложение Java.

    ```
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    В окне терминала отобразится уведомление о создании базы данных FamilyDB. 
    
4. Нажмите клавишу, чтобы создать коллекцию. 

5. Вернитесь в обозреватель данных и убедитесь, что в нем теперь находится база данных FamilyDB.
    
6. Продолжайте нажимать клавиши в окне консоли, чтобы код создал документы и выполнил запрос.
    
    В конце программы все ресурсы этого приложения будут удалены из учетной записи, чтобы вам не пришлось их оплачивать. 

    ![Вывод на консоль](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы узнали, как создать учетную запись Azure Cosmos DB, базу данных документов и коллекцию с помощью обозревателя данных, а также как запустить приложение, чтобы эти операции выполнялись программой. Теперь вы можете импортировать дополнительные данные в коллекцию Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Импорт данных в DocumentDB с помощью средства миграции базы данных](import-data.md)


