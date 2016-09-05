.<properties 
	pageTitle="Подключение к учетной записи DocumentDB с поддержкой протокола MongoDB | Microsoft Azure" 
	description="Узнайте, как подключиться к учетной записи DocumentDB с поддержкой протокола MongoDB, доступной в предварительной версии. Подключитесь с помощью строки подключения MongoDB." 
	keywords="строка подключения MongoDB"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="stbaro"/>

# Подключение к учетной записи DocumentDB с поддержкой протокола MongoDB

Узнайте, как подключиться к учетной записи Azure DocumentDB с поддержкой протокола MongoDB, используя стандартный формат URI для строки подключения MongoDB.

## Получение строки подключения для учетной записи

1. В новом окне войдите на [портал Azure](https://portal.azure.com).
2. На панели **левой панели** в колонке учетной записи щелкните элемент **Строка подключения**. Чтобы перейти к **колонке учетной записи**, на панели быстрых переходов щелкните последовательно элементы **Другие службы**, **DocumentDB (NoSQL)** и выберите нужную учетную запись DocumentDB с поддержкой протокола MongoDB.

	![Снимок экрана, колонка "Все параметры"](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. Откроется колонка **Информация о строке подключения**, которая содержит все необходимые сведения для подключения к учетной записи с помощью драйвера для MongoDB. Здесь есть и автоматически созданная строка подключения.

	![Снимок экрана, колонка строки подключения](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## Требования к строке подключения

Важно отметить, что DocumentDB поддерживает стандартный формат URI для строки подключения MongoDB, налагая пару дополнительных условий. Для учетных записей DocumentDB требуется использовать проверку подлинности и безопасный обмен данными через SSL. Таким образом, строка подключения имеет следующий вид:

	mongodb://username:password@host:port/[database]?ssl=true

Значения для этой строки можно найти в колонке строки подключения, как показано выше.

- Имя пользователя (обязательно)
	- Имя учетной записи DocumentDB
- Пароль (обязательно)
	- Пароль для учетной записи DocumentDB
- Узел (обязательно)
	- Полное доменное имя учетной записи DocumentDB
- Порт (обязательно)
	- 10250
- База данных (необязательно)
	- База данных по умолчанию, используемая подключением
- ssl=true (обязательно)

Рассмотрим в качестве примера учетную запись, сведения о которой приведены выше в строке подключения. Допустимая строка подключения:
	
	mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## Подключение с использованием драйвера C# для MongoDB
Как уже упоминалось, для всех учетных записей DocumentDB требуется проверка подлинности и безопасный обмен данными через SSL. Хотя формат URI для строки подключения MongoDB поддерживает параметр запроса ssl=true, для работы драйвера C# для MongoDB при создании MongoClient необходимо использовать объект MongoClientSettings. В следующем фрагменте кода используются приведенные выше сведения об учетной записи для демонстрации подключения к учетной записи и работы с базой данных Tasks.

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
	

## Дальнейшие действия


- Узнайте, как [использовать MongoChef](documentdb-mongodb-mongochef.md) с учетной записью DocumentDB с поддержкой протокола MongoDB.
- Изучите [примеры](documentdb-mongodb-samples.md) использования DocumentDB с поддержкой протокола MongoDB.

 

<!---HONumber=AcomDC_0824_2016-->