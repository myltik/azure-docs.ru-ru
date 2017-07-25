---
title: "Создание приложения Java Azure Cosmos DB с помощью API Graph | Документация Майкрософт"
description: "В этой статье представлен пример кода Java, который можно использовать для подключения и выполнения запросов к данным графа в Azure Cosmos DB с помощью Gremlin."
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
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: ru-ru
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB. Создание приложения Java с помощью API Graph

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования Azure Cosmos DB. 

В этом кратком руководстве показано, как создать учетную запись Azure Cosmos DB для API Graph (предварительная версия), базу данных и граф с использованием портала Azure. Затем вы можете создать и запустить консольное приложение, используя драйвер [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) OSS.  

## <a name="prerequisites"></a>Предварительные требования

* Для выполнения этого примера вам потребуется:
   * пакет JDK версии не ниже 1.7 (если у вас нет пакета JDK, выполните команду `apt-get install default-jdk`) и настройка переменных среды, например `JAVA_HOME`;
   * Maven (если у вас нет Maven, выполните `apt-get install maven`).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Добавление графа

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение API Graph (предварительная версия) из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте окно терминала Git, например Git Bash, и выполните команду `cd`, чтобы перейти в рабочий каталог.  

2. Выполните команду ниже, чтобы клонировать репозиторий с примером. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Просмотр кода

Сделаем краткий обзор того, что происходит в приложении. Откройте файл `Program.java`, и вы увидите приведенные ниже строки кода. 

* Инициализация Gremlin `Client` из конфигурации в файле `src/remote.yaml`.

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* Выполнение шагов Gremlin с использованием метода `client.submit`.

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>Обновление строки подключения

1. Откройте файл src/remote.yaml. 

3. Укажите *узел*, *порт*, *имя пользователя*, *пароль*, *пул подключений* и *сериализатор* в файле src/remote.yaml.

    Настройка|Рекомендуемое значение|Описание
    ---|---|---
    Узлы|[***.graphs.azure.com]|Просмотрите указанный ниже снимок экрана. Это значение Gremlin URI на странице обзора портала Azure, заключенное в квадратные скобки и без окончания 443/.<br><br>Его также можно получить на вкладке "Ключи" с помощью значения URI, удалив https://, изменив документы на диаграммы и удалив окончание 443/.
    Порт|443|Задайте значение 443.
    Имя пользователя|*Имя пользователя*|Ресурс в формате `/dbs/<db>/colls/<coll>`, где `<db>` — это имя базы данных, а `<coll>` — имя коллекции.
    Пароль|*Первичный главный ключ*|Просмотрите второй снимок экрана ниже. Это первичный ключ, который можно получить на странице "Ключи" на портале Azure в поле "Первичный ключ". Скопируйте значение с помощью кнопки копирования в левой части поля.
    Пул подключений|{enableSsl: true}|Параметр пула подключений для SSL.
    serializer|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Задайте это значение и удалите все разрывы строк `\n` при вставке значения.

    Для значений узлов скопируйте значение **Gremlin URI** со страницы **обзора**: ![Просмотр и копирование значения Gremlin URI на странице обзора на портале Azure](./media/create-graph-java/gremlin-uri.png)

    Для значений пароля скопируйте **первичный ключ** со страницы **Ключи**: ![Просмотр и копирование первичного ключа на портале Azure из страницы "Ключи"](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Запуск консольного приложения

1. В окне терминала выполните команду `mvn package`, чтобы установить необходимые пакеты Java.

2. В окне терминала запустите `mvn exec:java -D exec.mainClass=GetStarted.Program`, чтобы запустить приложение Java.

Вернитесь в обозреватель данных, где вы можете просматривать, запрашивать и изменять новые данные, а также работать с ними. 

## <a name="browse-using-the-data-explorer"></a>Просмотр с помощью обозревателя данных

Теперь вернитесь в обозреватель данных на портале Azure. Здесь вы можете просмотреть и запросить новые данные графа.

* В обозревателе данных новая база данных отображается в области "Коллекции". Разверните **graphdb** и **graphcoll**, а затем щелкните **График**.

    Данные, созданные в примере приложения, отображаются на панели Graphs (Графы).

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


