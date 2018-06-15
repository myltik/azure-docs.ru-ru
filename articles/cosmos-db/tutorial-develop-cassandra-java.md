---
title: Azure Cosmos DB. Разработка на Java с помощью API Cassandra | Документация Майкрософт
description: Сведения о разработке на Java с помощью API-интерфейса Cassandra для Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
tags: ''
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: java
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 379f1a369bd94f22736abfb766ba239c3f93acee
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798099"
---
# <a name="azure-cosmosdb-develop-with-the-cassandra-api-in-java"></a>Azure Cosmos DB. Разработка на Java с помощью API Cassandra

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB. 

В этом руководстве показано, как создать учетную запись Azure Cosmos DB на портале Azure, а затем таблицу Cassandra (sql-api-partition-data.md#partition-keys) с помощью [API-интерфейса Cassandra](cassandra-introduction.md). Если при создании таблицы вы определите первичный ключ, вы сможете легко масштабировать приложение по мере увеличения объема данных. 

В этом руководстве описано, как выполнить следующие задачи с помощью API-интерфейса Cassandra:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos DB;
> * создание пространства ключей и таблицы с первичным ключом;
> * Добавление данных
> * Запрос данных
> * просмотр соглашений об уровне обслуживания.

## <a name="prerequisites"></a>предварительным требованиям

Войдите в предварительную версию API Cassandra Azure Cosmos DB. Если вы еще не подали заявку на получение доступа, [зарегистрируйтесь сейчас](https://aka.ms/cosmosdb-cassandra-signup).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Кроме того, можно воспользоваться [бесплатной пробной версией Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) без подписки Azure, оплаты и каких-либо обязательств.

Кроме того, сделайте следующее: 

* [Комплект разработчика Java (JDK 1.7+)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив [Maven](http://maven.apache.org/).
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git](https://www.git-scm.com/)
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Прежде чем создавать базу данных документов, необходимо создать в Azure Cosmos DB учетную запись Cassandra.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом. Клонируйте приложение API Cassandra с GitHub, укажите строку подключения и запустите это приложение. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте окно терминала git, например git bash, и выполните команду `cd`, чтобы перейти в папку для установки примера приложения. 

    ```bash
    cd "C:\git-samples"
    ```

2. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Просмотр кода

Этот шаг не является обязательным. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Если вас это не интересует, можете сразу переходить к разделу [Обновление строки подключения](#update-your-connection-string). Эти фрагменты кода взяты из файла src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.  

* Здесь для Cassandra задаются узел, порт, имя пользователя, пароль и параметры SSL. Строку подключения вы можете получить на странице строки подключения на портале Azure.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* `cluster` подключается к API-интерфейсу Cassandra для Azure Cosmos DB и возвращает сеанс для доступа.

    ```java
    return cluster.connect();
    ```

Следующие фрагменты кода взяты из файла src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.

* Создайте пространство ключей.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Создайте таблицу.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Вставьте пользовательские сущности, используя подготовленный объект инструкций.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Выполните запрос на получение информации обо всех пользователях.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Выполните запрос на получение информации об одном пользователе.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение. Так вы обеспечите обмен данными между приложением и размещенной базой данных.

1. На [портале Azure](http://portal.azure.com/) щелкните **Строка подключения**. 

    ![Просмотрите и скопируйте имя пользователя на странице "Строка подключения" на портале Azure.](./media/tutorial-develop-cassandra-java/keys.png)

2. Вы можете использовать ![кнопку "Копировать"](./media/tutorial-develop-cassandra-java/copy.png) в правой части экрана, чтобы скопировать значение параметра CONTACT POINT.

3. Откройте файл `config.properties` из папки C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources. 

3. Вставьте полученное на портале значение параметра CONTACT POINT вместо элемента `<Cassandra endpoint host>` в строке 2.

    Теперь строка 2 в файле config.properties будет выглядеть примерно так: 

    `cassandra_host=cosmos-db-quickstarts.documents.azure.com`

3. Вернитесь на портал и скопируйте значение USERNAME. Вставьте полученное на портале значение USERNAME вместо элемента `<cassandra endpoint username>` в строке 4.

    Теперь строка 4 в файле config.properties будет выглядеть примерно так: 

    `cassandra_username=cosmos-db-quickstart`

4. Вернитесь на портал и скопируйте значение PASSWORD. Вставьте полученное на портале значение PASSWORD вместо элемента `<cassandra endpoint password>` в строке 5.

    Теперь строка 5 в файле config.properties будет выглядеть примерно так: 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Если вы хотите использовать определенный сертификат SSL, замените элемент `<SSL key store file location>` в строке 6 расположением своего SSL-сертификата. Если не указать это значение, будет использоваться сертификат JDK, установленный в папке <JAVA_HOME>/jre/lib/security/cacerts. 

6. Если вы изменили строку 6, чтобы использовать свой сертификат SSL, укажите пароль для этого сертификата в строке 7. 

7. Сохраните файл config.properties.

## <a name="run-the-app"></a>Запуск приложения

1. В окне терминала git перейдите к папке azure-cosmosdb-cassandra-java-getting-started\java-examples при помощи команды `cd`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. В окне терминала Git используйте следующую команду, чтобы создать файл cosmosdb-cassandra-examples.jar.

    ```git
    mvn clean install
    ```

3. В окне терминала Git выполните следующие команды, чтобы запустить приложение Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    Окно терминала отображает уведомления о создании пространства ключей и таблицы. Затем оно выбирает и отображает список всех пользователей в таблице, а затем — выбор и отображение строки по ее идентификатору.  
    
    Вернитесь в обозреватель данных, где вы можете просматривать, запрашивать и изменять новые данные, а также работать с ними. 

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos DB;
> * создание пространства ключей и таблицы с первичным ключом;
> * Добавление данных
> * Запрос данных
> * просмотр соглашений об уровне обслуживания.

Теперь вы можете импортировать дополнительные данные в коллекцию Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Импорт данных Cassandra в Azure Cosmos DB](cassandra-import-data.md)
