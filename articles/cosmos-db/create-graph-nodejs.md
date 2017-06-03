---
title: "Создание приложения Node.js Azure Cosmos DB с помощью API Graph | Документация Майкрософт"
description: "В этой статье представлен пример кода Node.js, который можно использовать для подключения и выполнения запросов к Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 5c996068ff5fbadda6730244c34c0d0d1f8fb447
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-using-the-graph-api"></a>Azure Cosmos DB. Создание приложения Node.js с помощью API Graph

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования Azure Cosmos DB. 

В этом кратком руководстве показано, как создать учетную запись Azure Cosmos DB для API Graph (предварительная версия), базу данных и граф с использованием портала Azure. Затем вы можете создать и запустить консольное приложение, используя драйвер [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure) OSS.  

> [!NOTE]
> Модуль NPM `gremlin-secure` — это измененная версия модуля `gremlin` с поддержкой SSL и SASL, необходимых для подключения к Azure Cosmos DB. Исходный код доступен на сайте [GitHub](https://github.com/CosmosDB/gremlin-javascript).
>

## <a name="prerequisites"></a>Предварительные требования

* Для выполнения этого примера вам потребуется:
    * [Node.js](https://nodejs.org/en/) версии 0.10.29 или более поздней.
    * [Git.](http://git-scm.com/)

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Затем откройте файл решения в Visual Studio. 

## <a name="review-the-code"></a>Просмотр кода

Сделаем краткий обзор того, что происходит в приложении. Откройте файл `app.js`, и вы увидите приведенные ниже строки кода. 

* Создание клиента Gremlin.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

Все конфигурации находятся в файле `config.js`, который мы изменим в следующем разделе.

* Выполнение шагов Gremlin с использованием метода `client.execute`.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение.

1. На [портале Azure](http://portal.azure.com/) перейдите к учетной записи базы данных Azure Cosmos DB и на левой панели навигации щелкните **Ключи**, а затем выберите **Ключи записи-чтения**. На следующем шаге используйте кнопку копирования в правой части экрана, чтобы скопировать универсальный код ресурса (URI) и первичный ключ в файл `app.js`.

    ![Просмотр и копирование ключа доступа на портале Azure, колонка "Ключи"](./media/create-documentdb-dotnet/keys.png)

2. Скопируйте значение Gremlin URI на портале (с помощью кнопки копирования) и добавьте его в качестве значения ключа `config.endpoint` в файле config.js. Конечная точка Gremlin должна состоять из имени узла без протокола или номера порта, например `mygraphdb.graphs.azure.com` (а не `https://mygraphdb.graphs.azure.com` или `mygraphdb.graphs.azure.com:433`).

    `config.endpoint = "GRAPHENDPOINT";`

3. Затем скопируйте значение первичного ключа с портала и добавьте его в качестве значения параметра config.primaryKey в файле config.js. Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

    `config.primaryKey = "PRIMARYKEY";`

4. Введите имя базы данных и графа (контейнера) для значения config.database и config.collection. 

Вот как должен выглядеть файл config.js:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "mygraphdb.graphs.azure.com";
config.primaryKey = "OjlhK6tjxfSXyKtrmCiM9O6gQQgu5DmgAoauzD1PdPIq1LZJmILTarHvrolyUYOB0whGQ4j21rdAFwoYep7Kkw==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Запуск консольного приложения

1. Откройте окно терминала. С помощью команды `cd` перейдите в каталог установки файла package.json, включенного в проект.  

2. Запустите `npm install`, чтобы установить необходимые модули npm. А именно `gremlin-secure`.

3. Запустите `node app.js` в окне терминала, чтобы запустить приложение Node.

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
