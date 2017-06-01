---
title: "Использование MongoChef c Azure Cosmos DB | Документация Майкрософт"
description: "Узнайте, как использовать MongoChef с учетной записью API для MongoDB в Azure Cosmos DB"
keywords: MongoChef
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 54c9799bd646b827f602e2ea2f9a15a4fc853f00
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017


---
# <a name="use-mongochef-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Использование MongoChef с учетной записью API для MongoDB в Azure Cosmos DB

Чтобы подключиться к учетной записи API для MongoDB в Azure Cosmos DB, необходимо:

* скачать и установить [MongoChef](http://3t.io/mongochef)
* Подготовить сведения о [строке подключения](connect-mongodb-account.md) для учетной записи API для MongoDB в Azure Cosmos DB.

## <a name="create-the-connection-in-mongochef"></a>создать подключение в MongoChef.
Чтобы добавить в диспетчер подключений MongoChef учетную запись API для MongoDB в Azure Cosmos DB, выполните следующие действия.

1. Извлеките сведения о подключении API для MongoDB в Azure Cosmos DB. Ознакомьтесь с инструкциями [здесь](connect-mongodb-account.md).

    ![Снимок экрана, колонка строки подключения](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Щелкните **Connect** (Подключиться), чтобы открыть диспетчер подключений, и нажмите кнопку **New Connection** (Новое подключение).

    ![Снимок экрана диспетчера подключений MongoChef](./media/mongodb-mongochef/ConnectionManager.png)
3. В окне **Новое подключение** на вкладке **Сервер** введите узел (полное доменное имя) учетной записи API для MongoDB в Azure Cosmos DB.

    ![Снимок экрана диспетчера подключений MongoChef, вкладка серверов](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. В окне **New Connection** (Новое подключение) на вкладке **Authentication** (Аутентификация) выберите режим аутентификации **Standard (MONGODB-CR or SCARM-SHA-1)** (Стандартная (MONGODB CR или SCARM-SHA-1)), а также введите имя пользователя и пароль.  Подтвердите базу данных по умолчанию для проверки подлинности (admin) или укажите другое значение.

    ![Снимок экрана диспетчера подключений MongoChef, вкладка проверки подлинности](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. В окне **New Connection** (Новое подключение) на вкладке **SSL** установите флажок **Use SSL protocol to connect** (Использовать для подключения протокол SSL) и переключатель **Accept server self-signed SSL certificates** (Принимать самозаверяющие SSL-сертификаты сервера).

    ![Снимок экрана диспетчера подключений MongoChef, вкладка SSL](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Нажмите кнопку **Test Connection** (Проверить подключение), чтобы проверить сведения о подключении. Затем нажмите кнопку **ОК**, чтобы вернуться в окно "New Connection" (Новое подключение), а затем нажмите кнопку **Save** (Сохранить).

    ![Снимок экрана окна тестового подключения MongoChef](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>Использование MongoChef для создания базы данных, коллекции и документов
Чтобы создать базу данных, коллекцию и документы с помощью MongoChef, выполните следующие действия.

1. В **диспетчере подключений** выделите нужное подключение и щелкните **Connect** (Подключиться).

    ![Снимок экрана диспетчера подключений MongoChef](./media/mongodb-mongochef/ConnectToAccount.png)
2. Щелкните узел правой кнопкой мыши и выберите **Добавить базу данных**.  Укажите имя базы данных и нажмите кнопку **ОК**.

    ![Снимок экрана MongoChef, параметр "Добавление базы данных"](./media/mongodb-mongochef/AddDatabase1.png)
3. Щелкните правой кнопкой мыши базу данных и выберите **Добавить коллекцию**.  Укажите имя коллекции и нажмите кнопку **Создать**.

    ![Снимок экрана MongoChef, параметр "Добавление коллекции"](./media/mongodb-mongochef/AddCollection.png)
4. Щелкните пункт меню **Collection** (Коллекция), затем щелкните **Add Document** (Добавить документ).

    ![Снимок экрана MongoChef, элемент меню "Добавление документа"](./media/mongodb-mongochef/AddDocument1.png)
5. В диалоговом окне "Добавление документа" вставьте следующий текст и щелкните **Добавить документ**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Добавьте еще один документ со следующим содержимым.

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Выполните пробный запрос. Например, попробуйте найти семьи с фамилией Andersen и вернуть для них поля parents и state.

    ![Снимок экрана Mongo Chef, результаты запроса](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с [примерами](mongodb-samples.md) API для MongoDB в Azure Cosmos DB.

