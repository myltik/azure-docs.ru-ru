<properties
	pageTitle="Руководство по NoSQL Node.js для DocumentDB | Microsoft Azure"
	description="Руководство по NoSQL Node.js, в котором создается база данных Node и консольное приложение с помощью пакета SDK для Node.js DocumentDB. DocumentDB — это база данных NoSQL для JSON."
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
	ms.date="11/18/2015"
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

## Предварительные требования

Убедитесь, что у вас есть указанные ниже компоненты.

- Активная учетная запись Azure. Если у вас нет такой учетной записи, вы можете зарегистрироваться для использования [бесплатной пробной версии Azure](http://azure.microsoft.com/pricing/free-trial/).
- [Node.js](https://nodejs.org/) версии v0.10.29 или выше.

## Этап 1: создание учетной записи DocumentDB

Создадим учетную запись DocumentDB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка приложения Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Шаг 2. Настройка приложения Node.js

1. Откройте удобный для вас терминал.
2. Перейдите в папку или каталог, где вы хотите сохранить приложение Node.js.
3. Создайте два пустых файла JavaScript с помощью следующих команд:
	- Windows:    
	    * **fsutil file createnew app.js 0**
        * **fsutil file createnew config.js 0**
	- Linux или OS X: 
	    * **touch app.js**
        * **touch config.js**
4. Установите модуль documentdb через npm. Используйте следующую команду:
    * **npm install documentdb --save**

Отлично! Теперь, когда настройка завершена, можно начать писать код.

##<a id="Config"></a> Шаг 3. Настройка конфигурации приложения

Откройте файл *config.js* в предпочитаемом текстовом редакторе.

Затем создайте пустой объект с именем *config* и задайте свойства *config.endpoint* и *config.authKey* для конечной точки и ключа авторизации DocumentDB. Обе эти конфигурации можно найти на [портале Azure](https://portal.azure.com).

![Снимок экрана портала Azure, на котором отображена учетная запись DocumentDB с выделенным АКТИВНЫМ концентратором, выделенной кнопкой «Ключи» в колонке учетной записи DocumentDB и выделенными универсальным кодом ресурса, ПЕРВИЧНЫМ КЛЮЧОМ И ВТОРИЧНЫМ КЛЮЧОМ в колонке «Ключи».][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Теперь добавим *идентификатор базы данных*, *идентификатор коллекции* и *документы JSON* в объект *config*. Указав свойства *config.endpoint* и *config.authKey*, добавьте приведенный ниже код. Если у вас уже есть данные, которые необходимо хранить в базе данных, вместо добавления определений документа можно использовать [средство переноса данных](documentdb-import-data.md) в DocumentDB.

    config.dbDefinition = {"id": "FamilyRegistry"};
    config.collDefinition = {"id": "FamilyCollection"};

    var documents = {
      "Andersen": {
        "id": "AndersenFamily",
        "LastName": "Andersen",
        "Parents": [
          {
            "FirstName": "Thomas"
          },
          {
            "FirstName": "Mary Kay"
          }
        ],
        "Children": [
          {
            "FirstName": "Henriette Thaulow",
            "Gender": "female",
            "Grade": 5,
            "Pets": [
              {
                "GivenName": "Fluffy"
              }
            ]
          }
        ],
        "Address": {
          "State": "WA",
          "County": "King",
          "City": "Seattle"
        }
      },
      "Wakefield":   {
          "id": "WakefieldFamily",
          "Parents": [
            {
              "FamilyName": "Wakefield",
              "FirstName": "Robin"
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Ben"
            }
          ],
          "Children": [
            {
              "FamilyName": "Merriam",
              "FirstName": "Jesse",
              "Gender": "female",
              "Grade": 8,
              "Pets": [
                {
                  "GivenName": "Goofy"
                },
                {
                  "GivenName": "Shadow"
                }
              ]
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Lisa",
              "Gender": "female",
              "Grade": 1
            }
          ],
          "Address": {
            "State": "NY",
            "County": "Manhattan",
            "City": "NY"
          },
          "IsRegistered": false
        }
    };

    config.docsDefinitions = documents;

База данных, коллекция и определения документа будут выступать в качестве *идентификатора базы данных*, *идентификатора коллекции* и данных документов в DocumentDB.

Наконец, экспортируйте объект *config*, чтобы на него можно ссылаться в файле *app.js*.

    module.exports = config;

##<a id="Connect"></a> Шаг 4. Подключение к учетной записи DocumentDB

Откройте пустой файл *app.js* в текстовом редакторе. Импортируйте модуль *documentdb* и только что созданный модуль *config*.

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");

Теперь создайте DocumentClient, используя ранее сохраненные значения *config.endpoint* и *config.authKey*.

    var client = new documentClient(config.endpoint, {"masterKey": config.authKey});

После того как вы подключились к учетной записи DocumentDB, рассмотрим принципы работы с ресурсами DocumentDB.

## Шаг 5. Создание базы данных
[Базу данных](documentdb-resources.md#databases) можно создать, используя функцию [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) класса **DocumentClient**. База данных представляет собой логический контейнер для хранения документов, разделенных между коллекциями. Добавьте функцию, чтобы создать новую базу данных в файле app.js с *идентификатором*, указанным в объекте *config*. Сначала мы убедимся в том, что база данных с таким же идентификатором *FamilyRegistry* не существует. Если она существует, мы вернем ее вместо создания новой базы данных.

    var getOrCreateDatabase = function(callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.dbDefinition.id
            }]
        };

        client.queryDatabases(querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createDatabase(config.dbDefinition, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateColl"></a>Шаг 6. Создание коллекции  

> [AZURE.WARNING]Элемент **CreateDocumentCollectionAsync** создаст новую коллекцию S1, с которой связаны ценовые требования. Дополнительные сведения см. на нашей [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).

Вы можете создать [коллекцию](documentdb-resources.md#collections), используя функцию [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) класса **DocumentClient**. Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript. Созданная коллекция будет сопоставлена с [уровнем производительности S1](documentdb-performance-levels.md). Добавьте функцию, чтобы создать новую коллекцию в файле app.js с *идентификатором*, указанным в объекте *config*. Теперь нужно снова убедиться в том, что коллекция с таким же идентификатором *FamilyCollection* не существует. Если она существует, мы вернем ее вместо создания новой коллекции.

    var getOrCreateCollection = function(callback) {

        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.collDefinition.id
            }]
        };

        var dbUri = "dbs/" + config.dbDefinition.id;

        client.queryCollections(dbUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createCollection(dbUri, config.collDefinition, function(err, created) {
                    if(err) return callback(err);
                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateDoc"></a>Шаг 7. Создание документа
[Документ](documentdb-resources.md#documents) можно создать с помощью функции [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) класса **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь можно вставить документ в DocumentDB.

Затем добавьте функцию в файл app.js для создания документов, содержащих данные JSON, сохраненные в объекте *config*. Теперь нужно снова убедиться в том, что документ с таким идентификатором не существует.

    var getOrCreateDocument = function(document, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: document.id
            }]
        };

        var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

        client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if(results.length === 0) {
                client.createDocument(collectionUri, document, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            } else {
                callback(null, results[0]);
            }
        });
    };

Поздравляем! Теперь у вас есть функции для создания базы данных, коллекции и документов в DocumentDB.

![Схема, иллюстрирующая иерархические отношения между учетной записью, базой данных, коллекцией и документами](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Шаг 8. Запросы ресурсов DocumentDB

DocumentDB поддерживает [полнофункциональные запросы](documentdb-sql-query.md) к документам JSON, хранящимся в каждой коллекции. В следующем примере кода показан выполняемый запрос к документам в коллекции. Добавьте в файл *app.js* следующую функцию. DocumentDB поддерживает SQL-подобные запросы, как показано ниже. Дополнительные сведения о построении сложных запросов см. на [площадке для запросов](https://www.documentdb.com/sql/demo) и в [документации по запросам](documentdb-sql-query.md).

    var queryCollection = function(documentId, callback) {
      var querySpec = {
          query: 'SELECT * FROM root r WHERE r.id=@id',
          parameters: [{
              name: '@id',
              value: documentId
          }]
      };

      var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

      client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
          if(err) return callback(err);

          callback(null, results);
      });
    };

На приведенной ниже схеме демонстрируется вызов синтаксиса запроса DocumentDB SQL к созданной коллекции.

![Схема, иллюстрирующая масштаб и значение запроса](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

Ключевое слово [FROM](documentdb-sql-query.md/#from-clause) использовать в запросе необязательно, так как запросы DocumentDB заранее привязаны к одной коллекции. Поэтому слова "FROM Families f" можно заменить на "FROM root r" или любое другое имя переменной. Поведение службы DocumentDB будет таким, будто Families, root или любая другая переменная указывает на текущую коллекцию по умолчанию.

##<a id="DeleteDatabase"></a>Шаг 9. Удаление базы данных Node

Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (коллекций, документов и т. д.). Можно удалить базу данных, добавив следующий фрагмент кода.

    // WARNING: this function will delete your database and all its children resources: collections and documents
    function cleanup(callback) {
        client.deleteDatabase("dbs/" + config.dbDefinition.id, function(err) {
            if(err) return callback(err);

            callback(null);
        });
    }

##<a id="Build"></a>Шаг 10. Сборка

Теперь, когда вы настроили в приложении все необходимые функции, давайте их вызовем.

Порядок вызова функций будет следующим: * *getOrCreateDatabase* * *getOrCreateCollection* * *getOrCreateDocument* * *getOrCreateDocument* * *queryCollection* * *cleanup*

Добавьте следующий фрагмент кода в конец файла *app.js*.

    getOrCreateDatabase(function(err, db) {
        if(err) return console.log(err);
        console.log('Read or created db:\n' + db.id + '\n');

        getOrCreateCollection(function(err, coll) {
            if(err) return console.log(err);
            console.log('Read or created collection:\n' + coll.id + '\n');

            getOrCreateDocument(config.docsDefinitions.Andersen, function(err, doc) {
                if(err) return console.log(err);
                console.log('Read or created document:\n' + doc.id + '\n');

                getOrCreateDocument(config.docsDefinitions.Wakefield, function(err, doc) {
                    if(err) return console.log(err);
                    console.log('Read or created document:\n' + doc.id + '\n');

                    queryCollection("AndersenFamily", function(err, results) {
                        if(err) return console.log(err);
                        console.log('Query results:\n' + JSON.stringify(results, null, '\t') + '\n');

                        cleanup(function(err) {
                            if(err) return console.log(err);
                            console.log('Done.');
                        });
                    });
                });
            });
        });
    });

##<a id="Run"></a> Шаг 11. Запуск приложения Node.js

Теперь все готово к запуску приложения Node.js.

В окне терминала перейдите к файлу *app.js* и выполните команду **node app.js**.

Должны отобразиться выходные данные вашего приложения. Выходные данные должны соответствовать примеру текста ниже.

    Read or created db:
    FamilyRegistry

    Read or created collection:
    FamilyCollection

    Read or created document:
    AndersenFamily

    Read or created document:
    WakefieldFamily

    Query results:
    [
            {
                    "id": "AndersenFamily",
                    "LastName": "Andersen",
                    "Parents": [
                            {
                                    "FirstName": "Thomas"
                            },
                            {
                                    "FirstName": "Mary Kay"
                            }
                    ],
                    "Children": [
                            {
                                    "FirstName": "Henriette Thaulow",
                                    "Gender": "female",
                                    "Grade": 5,
                                    "Pets": [
                                            {
                                                    "GivenName": "Fluffy"
                                            }
                                    ]
                            }
                    ],
                    "Address": {
                            "State": "WA",
                            "County": "King",
                            "City": "Seattle"
                    },
                    "_rid": "tOErANjwoQcBAAAAAAAAAA==",
                    "_ts": 1444327141,
                    "_self": "dbs/tOErAA==/colls/tOErANjwoQc=/docs/tOErANjwoQcBAAAAAAAAAA==/",
                    "_etag": ""00001200-0000-0000-0000-5616aee50000"",
                    "_attachments": "attachments/"
            }
    ]

    Done.

Поздравляем! Вы ознакомились с руководством по Node.js и создали первое консольное приложение DocumentDB.

##<a id="GetSolution"></a> Получение полного решения
Чтобы собрать решение GetStarted, которое содержит все примеры из данной статьи, вам понадобится следующее:

-   [учетная запись DocumentDB][documentdb-create-account];
-   решение [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started), доступное в GitHub.

Установите модуль **documentdb** с помощью npm. Используйте следующую команду: * **npm install documentdb --save**.

Затем в файле *config.js* обновите значения config.endpoint и config.authKey, как описано в разделе [Шаг 3. Настройка конфигурации приложения](#Config).

## Дальнейшие действия

-   Требуется более сложный пример Node.js? См. статью [Создание веб-приложения Node.js с использованием DocumentDB](documentdb-nodejs-application.md).
-	Узнайте, как [контролировать учетную запись DocumentDB](documentdb-monitor-accounts.md).
-	Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
-	Дополнительные сведения о модели программирования см. в разделе «Разработка» [на странице документации DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_1203_2015-->