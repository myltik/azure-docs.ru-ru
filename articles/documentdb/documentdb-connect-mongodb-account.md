---
title: Подключение к учетной записи DocumentDB с поддержкой протокола MongoDB | Microsoft Docs
description: Узнайте, как подключиться к учетной записи DocumentDB с поддержкой протокола MongoDB, доступной в предварительной версии. Подключитесь с помощью строки подключения MongoDB.
keywords: строка подключения MongoDB
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: anhoh

---
# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>Подключение к учетной записи DocumentDB с поддержкой протокола MongoDB
Узнайте, как подключиться к учетной записи Azure DocumentDB с поддержкой протокола MongoDB, используя стандартный формат URI для строки подключения MongoDB.  

## <a name="get-the-account's-connection-string-information"></a>Получение строки подключения для учетной записи
1. В новом окне войдите на [портал Azure](https://portal.azure.com).
2. На **левой панели** в колонке учетной записи щелкните **Строка подключения**. Чтобы перейти к **колонке учетной записи**, на навигационной панели щелкните **Больше служб**, затем — **DocumentDB (NoSQL)**. Выберите нужную учетную запись DocumentDB с поддержкой MongoDB.
   
    ![Снимок экрана, колонка "Все параметры"](./media/documentdb-connect-mongodb-account/SettingsBlade.png)
3. Откроется колонка **Сведения о строке подключения**, которая содержит все необходимые сведения для подключения к учетной записи с помощью драйвера для MongoDB, включая автоматически сформированную строку подключения.
   
    ![Снимок экрана, колонка строки подключения](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Требования к строке подключения
Важно отметить, что DocumentDB поддерживает стандартный формат URI для строки подключения MongoDB, налагая пару дополнительных условий. Для учетных записей DocumentDB требуется использовать проверку подлинности и безопасный обмен данными через SSL.  Таким образом, строка подключения имеет следующий вид:

    mongodb://username:password@host:port/[database]?ssl=true

Значения для этой строки можно найти в колонке строки подключения, как показано выше.

* Имя пользователя (обязательно)
  * Имя учетной записи DocumentDB
* Пароль (обязательно)
  * Пароль для учетной записи DocumentDB
* Узел (обязательно)
  * Полное доменное имя учетной записи DocumentDB
* Порт (обязательно)
  * 10250
* База данных (необязательно)
  * База данных по умолчанию, используемая подключением
* ssl=true (обязательно)

Рассмотрим в качестве примера учетную запись, сведения о которой приведены выше в строке подключения.  Допустимая строка подключения:

    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c#-driver-for-mongodb"></a>Подключение с использованием драйвера C# для MongoDB
Как уже упоминалось, для всех учетных записей DocumentDB требуется проверка подлинности и безопасный обмен данными через SSL. Хотя формат URI для строки подключения MongoDB поддерживает параметр запроса ssl=true, для работы драйвера C# для MongoDB при создании MongoClient необходимо использовать объект MongoClientSettings.  В следующем фрагменте кода используются приведенные выше сведения об учетной записи для демонстрации подключения к учетной записи и работы с базой данных Tasks.

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);


## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [использовать MongoChef](documentdb-mongodb-mongochef.md) с учетной записью DocumentDB с поддержкой протокола MongoDB.
* Изучите [примеры](documentdb-mongodb-samples.md)использования DocumentDB с поддержкой протокола MongoDB.

<!--HONumber=Oct16_HO2-->


