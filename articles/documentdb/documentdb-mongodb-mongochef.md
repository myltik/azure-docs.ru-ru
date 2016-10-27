<properties 
    pageTitle="Использование MongoChef с учетной записью DocumentDB с поддержкой протокола MongoDB | Microsoft Azure" 
    description="Узнайте, как использовать MongoChef с учетной записью DocumentDB с поддержкой протокола MongoDB, доступной в предварительной версии." 
    keywords="MongoChef"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="anhoh"/>


# <a name="use-mongochef-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>Использование MongoChef с учетной записью DocumentDB с поддержкой протокола MongoDB

Для подключения с помощью MongoChef к учетной записи Azure DocumentDB с поддержкой протокола MongoDB вам потребуется:

- скачать и установить [MongoChef](http://3t.io/mongochef)
- подготовить сведения о [строке подключения](documentdb-connect-mongodb-account.md) для учетной записи DocumentDB с поддержкой протокола MongoDB;

## <a name="create-the-connection-in-mongochef"></a>создать подключение в MongoChef.  

Чтобы добавить в диспетчер подключений MongoChef учетную запись DocumentDB с поддержкой протокола MongoDB, сделайте следующее.

1. Извлеките сведения о подключении DocumentDB с поддержкой протокола MongoDB. Ознакомьтесь с инструкциями [здесь](documentdb-connect-mongodb-account.md).

    ![Снимок экрана, колонка строки подключения](./media/documentdb-mongodb-mongochef/ConnectionStringBlade.png)

2. Щелкните **Connect** (Подключиться), чтобы открыть диспетчер подключений, и нажмите кнопку **New Connection** (Новое подключение).

    ![Снимок экрана диспетчера подключений MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManager.png)
    
2. В окне **New Connection** (Новое подключение) на вкладке **Server** (Сервер) введите узел (полное доменное имя) учетной записи DocumentDB с поддержкой протокола MongoDB и порт для подключения.
    
    ![Снимок экрана диспетчера подключений MongoChef, вкладка серверов](./media/documentdb-mongodb-mongochef/ConnectionManagerServerTab.png)

3. В окне **New Connection** (Новое подключение) на вкладке **Authentication** (Аутентификация) выберите режим аутентификации **Standard (MONGODB-CR or SCARM-SHA-1)** (Стандартная (MONGODB CR или SCARM-SHA-1)), а также введите имя пользователя и пароль.  Подтвердите базу данных по умолчанию для проверки подлинности (admin) или укажите другое значение.

    ![Снимок экрана диспетчера подключений MongoChef, вкладка проверки подлинности](./media/documentdb-mongodb-mongochef/ConnectionManagerAuthenticationTab.png)

4. В окне **New Connection** (Новое подключение) на вкладке **SSL** установите флажок **Use SSL protocol to connect** (Использовать для подключения протокол SSL) и переключатель **Accept self-signed SSL certificates** (Принимать самозаверяющие SSL-сертификаты).

    ![Снимок экрана диспетчера подключений MongoChef, вкладка SSL](./media/documentdb-mongodb-mongochef/ConnectionManagerSSLTab.png)

5. Нажмите кнопку **Test Connection** (Проверить подключение), чтобы проверить сведения о подключении. Затем нажмите кнопку **ОК**, чтобы вернуться в окно "New Connection" (Новое подключение), а затем нажмите кнопку **Save** (Сохранить).

    ![Снимок экрана окна тестового подключения MongoChef](./media/documentdb-mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database,-collection,-and-documents"></a>Использование MongoChef для создания базы данных, коллекции и документов  

Чтобы создать базу данных, коллекцию и документы с помощью MongoChef, выполните следующие действия.

1. В **диспетчере подключений** выделите нужное подключение и щелкните **Connect** (Подключиться).

    ![Снимок экрана диспетчера подключений MongoChef](./media/documentdb-mongodb-mongochef/ConnectToAccount.png)

2. Щелкните узел правой кнопкой мыши и выберите **Добавить базу данных**.  Укажите имя базы данных и нажмите кнопку **ОК**.
    
    ![Снимок экрана MongoChef, параметр "Добавление базы данных"](./media/documentdb-mongodb-mongochef/AddDatabase1.png)

3. Щелкните правой кнопкой мыши базу данных и выберите **Добавить коллекцию**.  Укажите имя коллекции и нажмите кнопку **Создать**.

    ![Снимок экрана MongoChef, параметр "Добавление коллекции"](./media/documentdb-mongodb-mongochef/AddCollection.png)

4. Щелкните пункт меню **Collection** (Коллекция), затем щелкните **Add Document** (Добавить документ).

    ![Снимок экрана MongoChef, элемент меню "Добавление документа"](./media/documentdb-mongodb-mongochef/AddDocument1.png)

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

    ![Снимок экрана Mongo Chef, результаты запроса](./media/documentdb-mongodb-mongochef/QueryDocument1.png)
    

## <a name="next-steps"></a>Дальнейшие действия

- Изучите [примеры](documentdb-mongodb-samples.md)использования DocumentDB с поддержкой протокола MongoDB.

 



<!--HONumber=Oct16_HO2-->


