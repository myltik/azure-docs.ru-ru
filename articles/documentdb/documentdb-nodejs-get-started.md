<properties
	pageTitle="Руководство по NoSQL Node.js для DocumentDB | Microsoft Azure"
	description="Руководство по NoSQL Node.js, в котором создается база данных Node и консольное приложение с помощью пакета SDK для Node.js DocumentDB. DocumentDB — это база данных NoSQL для JSON."
    keywords="node.js, руководство, база данных node"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# Руководство по NoSQL Node.js: консольное приложение Node.js DocumentDB  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Добро пожаловать в руководство по Node.js, посвященное пакету SDK для Node.js DocumentDB. После изучения этого руководства у вас будет консольное приложение, которое создает ресурсы DocumentDB, в том числе базу данных Node, и отправляет запросы к ним.

Мы рассмотрим следующие вопросы:

- создание учетной записи DocumentDB и подключение к ней;
- Настройка приложения
- Создание базы данных Node
- создание коллекции;
- создание документов JSON;
- выполнение запросов к коллекции;
- Удаление базы данных Node

У вас нет времени? Не беспокойтесь! Завершенное решение доступно на [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Краткие инструкции см. в разделе [Получение завершенного решения](#GetSolution).

После ознакомления с руководством по Node.js воспользуйтесь кнопками голосования в верхней или нижней части этой страницы, чтобы отправить нам отзыв. Если вы хотите, чтобы мы связались с вами, укажите ваш электронный адрес в комментариях.

А теперь приступим к работе!

## Предварительные требования для работы с руководством по Node.js

Убедитесь, что у вас есть указанные ниже компоненты.

- Активная учетная запись Azure. Если у вас нет такой учетной записи, вы можете зарегистрироваться для использования [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node.js](https://nodejs.org/) версии v0.10.29 или выше.

## Этап 1: создание учетной записи DocumentDB

Создадим учетную запись DocumentDB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка приложения Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Шаг 2. Настройка приложения Node.js

1. Откройте удобный для вас терминал.
2. Перейдите в папку или каталог, где вы хотите сохранить приложение Node.js.
3. Создайте два пустых файла JavaScript с помощью следующих команд:
	- Windows:    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux или OS X: 
	    * ```touch app.js```
        * ```touch config.js```
4. Установите модуль documentdb через npm. Используйте следующую команду:
    * ```npm install documentdb --save```

Отлично! Теперь, когда настройка завершена, можно начать писать код.

##<a id="Config"></a> Шаг 3. Настройка конфигурации приложения

Откройте файл ```config.js``` в предпочитаемом текстовом редакторе.

Создайте пустой объект с именем ```config``` и задайте свойства ```config.endpoint``` и ```config.authKey``` для конечной точки и ключа авторизации DocumentDB. Обе эти конфигурации можно найти на [портале Azure](https://portal.azure.com).

![Руководство по Node.js (база данных Node): снимок экрана портала Azure, на котором отображена учетная запись DocumentDB с выделенным АКТИВНЫМ концентратором, выделенной кнопкой "Ключи" в колонке учетной записи DocumentDB и выделенными универсальным кодом ресурса, ПЕРВИЧНЫМ КЛЮЧОМ И ВТОРИЧНЫМ КЛЮЧОМ в колонке "Ключи".][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Теперь добавим ```database id```, ```collection id``` и ```JSON documents``` в объект ```config```. Ниже, задавая свойства ```config.endpoint``` и ```config.authKey```, добавьте следующий код. Если у вас уже есть данные, которые необходимо хранить в базе данных, вместо добавления определений документа можно использовать [средство переноса данных](documentdb-import-data.md) в DocumentDB.

    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl",
        "partitionKey": { "paths": ["/district"], "kind": "Hash" }
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "district": "WA5",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "district": "NY23",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


База данных, коллекция и определения документа будут выступать в качестве ```database id```, ```collection id``` и данных документов в DocumentDB.

Наконец экспортируйте объект ```config```, чтобы на него можно было ссылаться в файле ```app.js```.

    module.exports = config;

##<a id="Connect"></a> Шаг 4. Подключение к учетной записи DocumentDB

Откройте пустой файл ```app.js``` в текстовом редакторе. Импортируйте модуль ```documentdb``` и только что созданный модуль ```config```.

    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Теперь создайте DocumentClient, используя ранее сохраненные значения ```config.endpoint``` и ```config.authKey```.

    var client = new documentClient(config.endpoint, { "masterKey": config.authKey });

После того как вы подключились к учетной записи DocumentDB, рассмотрим принципы работы с ресурсами DocumentDB.

## Шаг 5. Создание базы данных Node
[Базу данных](documentdb-resources.md#databases) можно создать, используя функцию [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) класса **DocumentClient**. База данных представляет собой логический контейнер для хранения документов, разделенных между коллекциями. Добавьте функцию создания новой базы данных в файле app.js с ```id```, указанным в объекте ```config```. Сначала убедитесь, что база данных с таким же идентификатором ```FamilyRegistry``` еще не создана. Если она существует, мы вернем ее вместо создания новой базы данных.

    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    /**
     * Get the database by ID, or create if it doesn't exist.
     * @param {string} database - The database to get or create
     */
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }
##<a id="CreateColl"></a>Шаг 6. Создание коллекции  

> [AZURE.WARNING] Элемент **CreateDocumentCollectionAsync** создаст новую коллекцию, с которой связаны ценовые требования. Дополнительные сведения см. на нашей [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).

Вы можете создать [коллекцию](documentdb-resources.md#collections), используя функцию [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) класса **DocumentClient**. Коллекция представляет собой контейнер документов JSON и связанное приложение логики JavaScript. Добавьте функцию для создания новой коллекции в файле app.js с ```id```, указанным в объекте ```config```. Теперь снова убедитесь, что коллекция с таким идентификатором ```FamilyCollection``` еще не создана. Если она существует, мы вернем ее вместо создания новой коллекции.

    /**
     * Get the collection by ID, or create if it doesn't exist.
     */
    function getCollection() {
        console.log(`Getting collection:\n${collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

##<a id="CreateDoc"></a>Шаг 7. Создание документа
Вы можете создать [документ](documentdb-resources.md#documents) с помощью функции [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) класса **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь можно вставить документ в DocumentDB.

Затем добавьте в файл app.js функцию создания документов, содержащих данные JSON, сохраненные в объекте ```config```. Теперь нужно снова убедиться в том, что документ с таким идентификатором не существует.

    /**
     * Get the document by ID, or create if it doesn't exist.
     * @param {function} callback - The callback function on completion
     */
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Поздравляем! Теперь у вас есть функции для создания базы данных, коллекции и документов в DocumentDB.

![Руководство по Node.js (база данных Node): на схеме представлены иерархические отношения между учетной записью, базой данных, коллекцией и документами.](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Шаг 8. Запросы к ресурсам DocumentDB

DocumentDB поддерживает [полнофункциональные запросы](documentdb-sql-query.md) к документам JSON, хранящимся в каждой коллекции. В следующем примере кода показан выполняемый запрос к документам в коллекции. Добавьте в файл ```app.js``` следующую функцию. DocumentDB поддерживает SQL-подобные запросы, как показано ниже. Дополнительные сведения о построении сложных запросов см. на [площадке для запросов](https://www.documentdb.com/sql/demo) и в [документации по запросам](documentdb-sql-query.md).

    /**
     * Query the collection using SQL
     */
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.address.city FROM root r WHERE r.lastName = "Andersen"',
                { enableCrossPartitionQuery: true }
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        console.log(`Query returned ${queryResult}`);
                    }
                    resolve(results);
                }
            });
        });
    };


На приведенной ниже схеме демонстрируется вызов синтаксиса запроса DocumentDB SQL к созданной коллекции.

![Руководство по Node.js (база данных Node): на схеме представлен масштаб и значение запроса.](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

Ключевое слово [FROM](documentdb-sql-query.md/#from-clause) использовать в запросе необязательно, так как запросы DocumentDB заранее привязаны к одной коллекции. Поэтому слова "FROM Families f" можно заменить на "FROM root r" или любое другое имя переменной. Поведение службы DocumentDB будет таким, будто Families, root или любая другая переменная указывает на текущую коллекцию по умолчанию.

##<a id="DeleteDatabase"></a>Шаг 9. Удаление базы данных Node

Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (коллекций, документов и т. д.). Можно удалить базу данных, добавив следующий фрагмент кода.

    /**
     * Cleanup the database and collection on completion
     */
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

##<a id="Build"></a>Шаг 10. Сборка

Теперь, когда вы настроили в приложении все необходимые функции, давайте их вызовем.

Добавьте следующий фрагмент кода в конец файла ```app.js```.

    /**
     * Exit the app with a prompt
     * @param {message} message - The message to display
     */
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
        .then(() => getCollection())
        .then(() => getFamilyDocument(config.documents.Andersen))
        .then(() => getFamilyDocument(config.documents.Wakefield))
        .then(() => queryCollection())
        .then(() => cleanup())
        .then(() => { exit(`Completed successfully`); })
        .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a> Шаг 11. Запуск приложения Node.js

Теперь все готово к запуску приложения Node.js.

В окне терминала перейдите к файлу ```app.js``` и выполните команду ```node app.js```.

Должны отобразиться выходные данные вашего приложения. Выходные данные должны соответствовать примеру текста ниже.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
      Query returned Seattle

    Cleaning up by deleting database FamilyDB

    Completed successfully
    Press any key to exit

Поздравляем! Вы ознакомились с руководством по Node.js и создали первое консольное приложение DocumentDB.

##<a id="GetSolution"></a> Получение полного решения для руководства по Node.js
Чтобы собрать решение GetStarted, которое содержит все примеры из данной статьи, вам понадобится следующее:

-   [учетная запись DocumentDB][documentdb-create-account];
-   решение [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started), доступное в GitHub.

Установите модуль **documentdb** с помощью npm. Используйте следующую команду:
* ```npm install documentdb --save```

Затем в файле ```config.js``` обновите значения config.endpoint и config.authKey, как описано в разделе [Шаг 3. Настройка конфигурации приложения](#Config).

## Дальнейшие действия

-   Требуется более сложный пример Node.js? См. статью [Создание веб-приложения Node.js с использованием DocumentDB](documentdb-nodejs-application.md).
-	Узнайте, как [контролировать учетную запись DocumentDB](documentdb-monitor-accounts.md).
-	Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
-	Дополнительные сведения о модели программирования см. в разделе «Разработка» [на странице документации DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->