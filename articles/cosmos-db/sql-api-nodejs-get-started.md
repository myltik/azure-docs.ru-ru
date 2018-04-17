---
title: Руководство по Node.js для API SQL в Azure Cosmos DB | Документация Майкрософт
description: В этом руководстве по Node.js создается база данных Cosmos DB с помощью API SQL.
keywords: node.js, руководство, база данных node
services: cosmos-db
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: article
ms.date: 08/14/2017
ms.author: anhoh
ms.openlocfilehash: de1ac7cb11f3e0d9c2de6dd3e5f635a9c508b2d2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="nodejs-tutorial-use-the-sql-api-in-azure-cosmos-db-to-create-a-nodejs-console-application"></a>Руководство по Node.js: использование API SQL в Azure Cosmos DB для создания консольного приложения Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js для MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Добро пожаловать в руководство по Node.js, посвященное пакету Azure SDK для Node.js Cosmos DB. После изучения этого руководства у вас будет консольное приложение, которое создает ресурсы Azure Cosmos DB и отправляет запросы к ним.

Мы рассмотрим следующие вопросы:

* создание учетной записи Azure Cosmos DB и подключение к ней;
* Настройка приложения
* Создание базы данных Node
* создание коллекции;
* создание документов JSON;
* выполнение запросов к коллекции;
* замена документа;
* удаление документа;
* Удаление базы данных Node

У вас нет времени? Не беспокойтесь! Полное решение доступно на [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Краткие инструкции см. в разделе [Получение полного решения для руководства по Node.js](#GetSolution).

После ознакомления с руководством по Node.js воспользуйтесь кнопками голосования в верхней или нижней части этой страницы, чтобы отправить нам отзыв. Если вы хотите, чтобы мы связались с вами, укажите ваш электронный адрес в комментариях.

А теперь приступим к работе!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Предварительные требования для работы с руководством по Node.js
Убедитесь, что у вас есть указанные ниже компоненты.

* Активная учетная запись Azure. Если у вас нет такой учетной записи, вы можете зарегистрироваться для использования [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) версии v0.10.29 или выше.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Шаг 1. Создание учетной записи Azure Cosmos DB
Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка приложения Node.js](#SetupNode). Если вы используете эмулятор Azure Cosmos DB, выполните действия, описанные в статье об [эмуляторе Azure Cosmos DB](local-emulator.md), чтобы настроить эмулятор и сразу перейти к [настройке приложения Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Шаг 2. Настройка приложения Node.js
1. Откройте удобный для вас терминал.
2. Перейдите в папку или каталог, где вы хотите сохранить приложение Node.js.
3. Создайте два пустых файла JavaScript с помощью следующих команд:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux или OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Установите модуль documentdb через npm. Используйте следующую команду:
   * ```npm install documentdb --save```

Отлично! Теперь, когда настройка завершена, можно начать писать код.

## <a id="Config"></a>Шаг 3. Настройка конфигурации приложения
Откройте файл ```config.js``` в предпочитаемом текстовом редакторе.

Скопируйте и вставьте приведенный ниже фрагмент кода, после чего задайте URI конечной точки и первичный ключ Azure Cosmos DB в качестве значений свойств ```config.endpoint``` и ```config.primaryKey```. Обе эти конфигурации можно найти на [портале Azure](https://portal.azure.com).

![Руководство по Node.js (база данных Node): снимок экрана портала Azure, на котором отображена учетная запись Azure Cosmos DB с выделенным активным концентратором, выделенной кнопкой "Ключи" в колонке учетной записи Azure Cosmos DB и выделенным универсальным кодом ресурса, первичным ключом и вторичным ключом в колонке "Ключи".][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Скопируйте ```database id```, ```collection id``` и ```JSON documents```, а затем вставьте их в объект ```config``` ниже, где задавались значения свойств ```config.endpoint``` и ```config.primaryKey```. Если у вас уже есть данные, которые необходимо хранить в базе данных, вместо добавления определений документа можно использовать [средство переноса данных](import-data.md) в Azure Cosmos DB.

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
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


База данных, коллекция и определения документа будут выступать в качестве ```database id```, ```collection id``` и данных документов Azure Cosmos DB.

Теперь экспортируйте объект ```config```, чтобы на него можно было ссылаться в файле ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>Шаг 4. Подключение к учетной записи Azure Cosmos DB
Откройте пустой файл ```app.js``` в текстовом редакторе. Скопируйте и вставьте следующий код, чтобы импортировать модуль ```documentdb``` и созданный модуль ```config```.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Скопируйте и вставьте код, чтобы создать DocumentClient с использованием ранее сохраненных значений ```config.endpoint``` и ```config.primaryKey```.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Теперь, когда у нас есть код для инициализации клиента Azure Cosmos DB, давайте рассмотрим принципы работы с ресурсами Azure Cosmos DB.

## <a name="step-5-create-a-node-database"></a>Шаг 5. Создание базы данных Node
Скопируйте и вставьте приведенный ниже код, чтобы задать значение Not Found для состояния HTTP, а также URL-адрес базы данных и URL-адрес коллекции. По этим URL-адресам клиент Azure Cosmos DB найдет нужные базу данных и коллекцию.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

[Базу данных](sql-api-resources.md#databases) можно создать, используя функцию [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) класса **DocumentClient**. База данных представляет собой логический контейнер для хранения документов, разделенных между коллекциями.

Скопируйте и вставьте функцию **getDatabase**, чтобы создать базу данных в файле app.js с параметром ```id```, указанным в объекте ```config```. Эта функция проверяет наличие базы данных с таким же идентификатором ```FamilyRegistry```. Если она существует, мы вернем ее вместо создания новой базы данных.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
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

Скопируйте и вставьте приведенный ниже код, в котором задается функция **getDatabase**, добавляющая вспомогательную функцию **exit**, которая распечатает сообщение о выходе, и вызов функции **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

В окне терминала перейдите к файлу ```app.js``` и выполните команду ```node app.js```.

Поздравляем! Вы успешно создали базу данных Azure Cosmos DB.

## <a id="CreateColl"></a>Шаг 6. Создание коллекции
> [!WARNING]
> Элемент **CreateCollection** создаст коллекцию, с которой связаны ценовые требования. Дополнительные сведения см. на нашей [странице цен](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Вы можете создать [коллекцию](sql-api-resources.md#collections), используя функцию [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) класса **DocumentClient**. Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript.

Скопируйте функцию **getCollection** и вставьте ее после функции **getDatabase** в файле app.js, чтобы создать коллекцию с параметром ```id```, указанным в объекте ```config```. Теперь нужно снова убедиться, что коллекция с таким идентификатором ```FamilyCollection``` еще не создана. Если она существует, мы вернем ее вместо создания новой коллекции.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

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

Скопируйте код и вставьте его после вызова функции **getDatabase**, чтобы выполнить функцию **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

В окне терминала перейдите к файлу ```app.js``` и выполните команду ```node app.js```.

Поздравляем! Вы успешно создали коллекцию Azure Cosmos DB.

## <a id="CreateDoc"></a>Шаг 7. Создание документа
Вы можете создать [документ](sql-api-resources.md#documents) с помощью функции [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) класса **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь можно вставить документ в Azure Cosmos DB.

Скопируйте функцию **getFamilyDocument** и вставьте ее после функции **getCollection**, чтобы создать документы, которые содержат данные JSON, сохраненные в объекте ```config```. Теперь нужно снова убедиться в том, что документ с таким идентификатором не существует.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, (err, result) => {
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

Скопируйте код и вставьте его после вызова функции **getCollection**, чтобы выполнить функцию **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

В окне терминала перейдите к файлу ```app.js``` и выполните команду ```node app.js```.

Поздравляем! Вы успешно создали документ Azure Cosmos DB.

![Руководство по Node.js (база данных Node): на схеме представлены иерархические отношения между учетной записью, базой данных, коллекцией и документами.](./media/sql-api-nodejs-get-started/node-js-tutorial-cosmos-db-account.png)

## <a id="Query"></a>Шаг 8. Запрос ресурсов Azure Cosmos DB
Azure Cosmos DB поддерживает [полнофункциональные запросы](sql-api-sql-query.md) к документам JSON, хранящимся в каждой коллекции. В следующем примере кода показан выполняемый запрос к документам в коллекции.

Скопируйте функцию **queryCollection** и вставьте ее после функции **getFamilyDocument** в файле app.js. Azure Cosmos DB поддерживает SQL-подобные запросы, как показано ниже. Дополнительные сведения о создании сложных запросов см. на [площадке для запросов](https://www.documentdb.com/sql/demo) и в [документации по запросам](sql-api-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


На приведенной ниже схеме показан вызов синтаксиса SQL-запроса Azure Cosmos DB к созданной коллекции.

![Руководство по Node.js (база данных Node): на схеме представлен масштаб и значение запроса.](./media/sql-api-nodejs-get-started/node-js-tutorial-collection-documents.png)

Ключевое слово [FROM](sql-api-sql-query.md#FromClause) использовать в запросе необязательно, так как запросы Azure Cosmos DB привязаны только к одной коллекции. Поэтому слова "FROM Families f" можно заменить на "FROM root r" или любое другое имя переменной. Поведение службы Azure Cosmos DB будет таким, будто Families, root или любая другая переменная указывает на текущую коллекцию по умолчанию.

Скопируйте код и вставьте его после вызова функции **getFamilyDocument**, чтобы выполнить функцию **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

В окне терминала перейдите к файлу ```app.js``` и выполните команду ```node app.js```.

Поздравляем! Вы успешно выполнили запрос к базе данных Azure Cosmos DB.

## <a id="ReplaceDocument"></a>Шаг 9. Замена документа
Azure Cosmos DB поддерживает замену документов JSON.

Скопируйте функцию **replaceFamilyDocument** и вставьте ее после функции **queryCollection** в файле app.js.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Скопируйте код и вставьте его после вызова функции **queryCollection**, чтобы выполнить функцию **replaceDocument**. Кроме того, добавьте код, позволяющий вызвать функцию **queryCollection**, еще раз, чтобы убедиться, что документ изменен успешно.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

В окне терминала перейдите к файлу ```app.js``` и выполните команду ```node app.js```.

Поздравляем! Вы успешно заменили документ Azure Cosmos DB.

## <a id="DeleteDocument"></a>Шаг 10. Удаление документа
Azure Cosmos DB поддерживает удаление документов JSON.

Скопируйте функцию **deleteFamilyDocument** и вставьте ее после функции **replaceFamilyDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Скопируйте код и вставьте его после вызова второй функции **queryCollection**, чтобы выполнить функцию **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

В окне терминала перейдите к файлу ```app.js``` и выполните команду ```node app.js```.

Поздравляем! Вы успешно удалили документ Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Шаг 11. Удаление базы данных Node
Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (коллекций, документов и т. д.).

Скопируйте функцию **cleanup** и вставьте ее после функции **deleteFamilyDocument**, чтобы удалить базу данных и все ее дочерние ресурсы.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Скопируйте код и вставьте его после вызова функции **deleteFamilyDocument**, чтобы выполнить функцию **cleanup**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a> Шаг 12. Запуск приложения Node.js
Полная последовательность для вызова функций должна выглядеть так:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

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
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Поздравляем! Вы ознакомились с руководством по Node.js и создали первое консольное приложение Azure Cosmos DB.

## <a id="GetSolution"></a>Получение полного решения для руководства по Node.js
Если вы не располагаете временем, чтобы выполнить шаги из этого руководства, или просто хотите скачать код, это можно сделать на портале [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started).

Чтобы запустить решение GetStarted, которое содержит все примеры из этой статьи, вам понадобится следующее:

* [Учетная запись Azure Cosmos DB][create-account].
* решение [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) , доступное в GitHub.

Установите модуль **documentdb** с помощью npm. Используйте следующую команду:

* ```npm install documentdb --save```

Затем в файле ```config.js``` обновите значения config.endpoint и config.primaryKey, как описано в разделе [Шаг 3. Настройка конфигурации приложения](#Config). 

Затем в окне терминала перейдите к файлу ```app.js``` и выполните команду ```node app.js```.

Теперь все готово. Выполните сборку и начинайте работу с решением. 

## <a name="next-steps"></a>Дополнительная информация
* Требуется более сложный пример Node.js? См. статью о [создании веб-приложения Node.js с использованием Azure Cosmos DB](sql-api-nodejs-application.md).
* Узнайте, как выполнять [мониторинг учетной записи Azure Cosmos DB](monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
