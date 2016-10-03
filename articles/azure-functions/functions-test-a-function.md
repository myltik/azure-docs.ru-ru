<properties
   pageTitle="Тестирование функций Azure | Microsoft Azure"
   description="Тестирование функций Azure с помощью Postman, cURL и Node.js."
   services="functions"
   documentationCenter="na"
   authors="wesmc7777"
   manager="erikre"
   editor=""
   tags=""
   keywords="функции azure, функции, обработка событий, webhook, динамические вычисления, независимая архитектура, тестирование"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="wesmc"/>

# Тестирование функций Azure

## Обзор

В этом руководстве мы рассмотрим различные подходы к тестированию функций. Мы определим функцию триггера http, которая принимает входные данные через параметр строки запроса или текст запроса. Используемый по умолчанию код шаблона **функции Node.js HttpTrigger** поддерживает параметр строки запроса `name`. Также мы добавим код для поддержки этого параметра вместе с данными `address` для пользователя в тексте запроса.

## Создание функции для тестирования

В большей части этого руководства мы будем использовать немного измененную версию шаблона **функции Node.js HttpTrigger**, который доступен при создании новой функции. Если вам нужна помощь при создании новой функции, просмотрите [руководство по созданию первой функции Azure](functions-create-first-azure-function.md). Просто выберите шаблон **HttpTrigger Node.js Function** (Функция Node.js HttpTrigger) при создании тестовой функции на [портале Azure].

Используемый по умолчанию шаблон функции по сути представляет собой функцию hello world, выводящую обратно имя из текста запроса или параметра строки запроса `name=<your name>`. Мы обновим код, позволив указать имя и адрес в качестве содержимого JSON в тексте запроса. Затем функция выведет их обратно на клиент при его наличии.

Добавьте в функцию следующий код, который будет использоваться для тестирования:

	module.exports = function(context, req) {
	    context.log("Node.js HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
	    context.log("Request Headers = " + JSON.stringify(req.headers));    
	
	    if (req.query.name || (req.body && req.body.name)) {
	        if (typeof req.query.name != "undefined") {
	            context.log("Name was provided as a query string param..."); 
	            ProcessNewUserInformation(context, req.query.name);
	        }
	        else {
	            context.log("Processing user info from request body..."); 
	            ProcessNewUserInformation(context, req.body.name, req.body.address);
	        }
	    }
	    else {
	        context.res = {
	            status: 400,
	            body: "Please pass a name on the query string or in the request body"
	        };
	    }
	    context.done();
	};
	
	function ProcessNewUserInformation(context, name, address)
	{    
	    context.log("Processing User Information...");            
	    context.log("name = " + name);            
	    echoString = "Hello " + name;
	    
	    if (typeof address != "undefined")
	    {
	        echoString += "\n" + "The address you provided is " + address;
	        context.log("address = " + address);            
	    }
	    
	    context.res = {
	            // status: 200, /* Defaults to 200 */
	            body: echoString
	        };
	}


## Тестирование функции с помощью инструментов

### Тестирование с помощью cURL

Часто при тестировании программного обеспечения для отладки приложения достаточно командной строки. То же самое справедливо и для функций.

Чтобы протестировать указанную выше функцию, скопируйте **URL-адрес функции** с портала. Он будет иметь следующий формат:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
Это URL-адрес для активации функции. Мы можем его протестировать с помощью команды cURL в командной строке, выполнив запрос Get (`-G` или `--get`) к нашей функции:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>
    
В приведенном выше конкретном примере требуется параметр строки запроса, который может быть передан как данные (`-d`) в команде cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>
    
Нажмите клавишу ВВОД, и вы увидите выходные данные функции в командной строке.

![](./media/functions-test-a-function/curl-test.png)

В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### Тестирование с помощью браузера

Функции, для которых не требуются параметры или требуются только параметры строки запроса, можно протестировать с помощью браузера.

Чтобы протестировать определенную выше функцию, скопируйте **URL-адрес функции** с портала. Он будет иметь следующий формат:

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Добавьте параметр строки запроса `name` следующим образом, используя фактическое имя вместо заполнителя `<Enter a name here>`.

	https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Вставьте URL-адрес в браузер. Должен отобразиться примерно такой ответ:

![](./media/functions-test-a-function/browser-test.png)

В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

	2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
	2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
	2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Wes from a browser
	2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"ru-RU"}
	2016-03-23T07:35:10.338 Name was provided as a query string param.
	2016-03-23T07:35:10.338 Processing User Information...
	2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### Тестирование с помощью приложения Postman

Рекомендуемым средством для тестирования большинства функций является приложение Postman. Чтобы установить Postman, см. веб-страницу, на которой можно [получить приложение Postman](https://www.getpostman.com/). Приложение Postman позволяет контролировать намного больше атрибутов HTTP-запроса.

> [AZURE.TIP] Используйте клиент REST, с которым вам удобно работать. Вот некоторые альтернативы Postman:
> 
> * [Fiddler](http://www.telerik.com/fiddler)
> * [Paw.](https://luckymarmot.com/paw)

Чтобы протестировать функцию с текстом запроса в приложении Postman, выполните следующие действия.

1. Запустите Postman нажатием кнопки **Приложения** в левом верхнем углу окна браузера Chrome.
2. Скопируйте **URL-адрес функции** и вставьте его в Postman. В нем содержится параметр строки запроса — код доступа.
3. Замените метод HTTP на **POST**.
4. Щелкните **Body** > **raw** (Текст > Необработанный) и добавьте текст запроса JSON, подобный следующему:

		{
		    "name" : "Wes testing with Postman",
		    "address" : "Seattle, W.A. 98101"
		}

5. Нажмите кнопку **Отправить**.

На следующем рисунке показано тестирование простого примера эхо-функции из этого руководства.

![](./media/functions-test-a-function/postman-test.png)

В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

	2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
	2016-03-23T08:04:57.763 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
	2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"ru-RU"}
	2016-03-23T08:04:57.763 Processing user info from request body...
	2016-03-23T08:04:57.763 Processing User Information...
	2016-03-23T08:04:57.763 name = Wes testing with Postman
	2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
	2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    
### Тестирование триггера больших двоичных объектов с помощью обозревателя хранилищ

Функцию триггера больших двоичных объектов можно протестировать с помощью [обозревателя хранилищ Microsoft Azure](http://storageexplorer.com/).

1. На [портале Azure] для приложения "Функции" создайте новую функцию триггера BLOB-объектов на языке C#, F# или Node. Задайте путь для отслеживания имени контейнера больших двоичных объектов. Например:

		files

2. Нажмите кнопку **+**, чтобы выбрать или создать учетную запись хранения, которую следует использовать. Затем щелкните **Создать**.

3. Создайте текстовый файл со следующим содержимым и сохраните его:

		A text file for blob trigger function testing.

4. Запустите [обозреватель хранилищ Microsoft Azure](http://storageexplorer.com/) и подключитесь к контейнеру больших двоичных объектов в отслеживаемой учетной записи хранения.

5. Нажмите кнопку **Отправить** и отправьте текстовый файл.

	![](./media/functions-test-a-function/azure-storage-explorer-test.png)


	Используемый по умолчанию код функции триггера больших двоичных объектов сообщит об обработке большого двоичного объекта в журналах:

		2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
		2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
		2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
		2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## Тестирование функции в рамках функций

### Тестирование с помощью кнопки "Выполнить" на портале функций

На портале есть кнопка **Выполнить**, которая позволяет провести ограниченное тестирование. С помощью кнопки "Выполнить" можно указать текст запроса, но нельзя указать параметры строки запроса или обновить заголовки запроса.

Протестируйте функцию HTTP-триггера, созданную ранее, добавив в поле **Текст запроса** строку JSON, подобную указанной ниже, а затем нажав кнопку **Выполнить**.

	{
		"name" : "Wes testing Run button",
		"address" : "USA"
	} 

В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

	2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
	2016-03-23T08:03:18.697 Node.js HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
	2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"ru-RU"}
	2016-03-23T08:03:18.697 Processing user info from request body...
	2016-03-23T08:03:18.697 Processing User Information...
	2016-03-23T08:03:18.697 name = Wes testing Run button
	2016-03-23T08:03:18.697 address = USA
	2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### Тестирование с помощью триггера таймера

Некоторые функции нельзя полноценно протестировать с помощью упомянутых ранее средств. Например, функцию триггера очереди, которая выполняется при поступлении сообщения в [хранилище очередей Azure](../storage/storage-dotnet-how-to-use-queues.md). Можно всегда написать код, помещающий сообщение в очередь, пример которого указан ниже в консольном проекте. Однако существует другой подход, который можно использовать для непосредственного тестирования функций.

Можно использовать триггер таймера, настроенный с привязкой для вывода очереди. Такой код триггера таймера может затем записывать тестовые сообщения в очередь. В этом разделе будет поэтапно описан пример.

Более подробные сведения об использовании привязок с функциями Azure см. в статье [Azure Functions developer reference](functions-reference.md) (Справочник разработчика по функциям Azure).


#### Создание триггера очереди для тестирования

Чтобы продемонстрировать этот подход, мы сначала создадим функцию триггера очереди, которую нам нужно протестировать, для очереди с именем `queue-newusers`. Эта функция будет обрабатывать имя и адрес нового пользователя, помещенные в хранилище очередей Azure.

> [AZURE.NOTE] При использовании другого имени очереди убедитесь, что используемое имя соответствует правилам [именования очередей и метаданных](https://msdn.microsoft.com/library/dd179349.aspx). В противном случае вы получите код состояния HTTP 400 (недопустимый запрос).

1. На [портале Azure] для приложения "Функции" щелкните **Создать функцию** > **QueueTrigger - C#**.
2. Введите имя очереди, которое будет отслеживаться функцией очереди.

		queue-newusers 

3. Нажмите кнопку **+** (Добавить), чтобы выбрать или создать учетную запись хранения, которую следует использовать. Затем щелкните **Создать**.
4. Оставьте это окно браузера с порталом открытым, чтобы можно было отслеживать записи журнала на наличие кода шаблона функции очереди по умолчанию.


#### Создание триггера таймера для помещения сообщения в очередь

1. Откройте [портал Azure] в новом окне браузера и перейдите к приложению "Функции".
2. Щелкните **Создать функцию** > **TimerTrigger - C#**. Введите выражение CRON, чтобы задать частоту выполнения кода таймера при тестировании функции очереди. Затем щелкните **Создать**. Если тестирование нужно выполнять каждые 30 с, можно использовать следующее [выражение CRON](https://wikipedia.org/wiki/Cron#CRON_expression):

		*/30 * * * * *


2. Выберите вкладку **Интеграция** для нового триггера таймера.
3. В разделе **Выходные данные** нажмите кнопку **+ Новые выходные данные**. Затем щелкните **очередь** и нажмите кнопку **Выбрать**.
4. Запишите имя, используемое для **объекта сообщения очереди**, — его следует указать в коде функции таймера.

		myQueue

4. Введите имя очереди, в которую будет отправлено сообщение:

		queue-newusers 

3. Нажмите кнопку **+** (Добавить), чтобы выбрать учетную запись хранения, указанную ранее в триггере очереди. Нажмите кнопку **Сохранить**.
4. Выберите вкладку **Разработка** для триггера таймера.
5. Для функции таймера на C# можно использовать приведенный ниже код, если вы использовали то же имя объекта сообщения очереди, указанное выше. Затем нажмите кнопку **Сохранить**.

		using System;
		
		public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
		{
		    String newUser = 
		    "{"name":"User testing from C# timer function","address":"XYZ"}";
		
		    log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
		    log.Verbose($"{newUser}");   
		    
		    myQueue = newUser;
		}

На этом этапе функция таймера C# будет выполняться каждые 30 с, если вы использовали выражение CRON из примера. Журналы для функции таймера будут сообщать о каждом выполнении:

	2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
	2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
	2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

В окне браузера для функции очереди отобразится каждое обрабатываемое сообщение:

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	
## Тестирование функции с помощью кода

### Тестирование функции HTTP-триггера с помощью кода: Node.js

Для выполнения HTTP-запроса для тестирования функции Azure можно использовать код Node.js.

Не забудьте задать следующие значения:

- для `host` в параметрах запроса — узел приложения функции;
- имя функции в параметре `path`;
- код доступа (`<your code>`) в параметре `path`.

Пример кода:

	var http = require("http");
	
	var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";
	
	var nameBodyJSON = {
	    name : "Wes testing with Node.JS code",
	    address : "Dallas, T.X. 75201"
	};
	
	var bodyString = JSON.stringify(nameBodyJSON);
	
	var options = {
	  host: "functions841def78.azurewebsites.net",
	  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
	  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
	  method: "POST",
	  headers : {
	      "Content-Type":"application/json",
	      "Content-Length": Buffer.byteLength(bodyString)
	    }    
	};
	
	callback = function(response) {
	  var str = ""
	  response.on("data", function (chunk) {
	    str += chunk;
	  });
	
	  response.on("end", function () {
	    console.log(str);
	  });
	}
	
	var req = http.request(options, callback);
	console.log("*** Sending name and address in body ***");
	console.log(bodyString);
	req.end(bodyString);



Выходные данные:

	C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
	*** Sending name and address in body ***
	{"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
	Hello Wes testing with Node.JS code
	The address you provided is Dallas, T.X. 75201
		
В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

	2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
	2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
	2016-03-23T08:09:01.153 Node.js HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
	2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
	2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
	2016-03-23T08:09:01.153 Request Body Type = object
	2016-03-23T08:09:01.153 Request Body = [object Object]
	2016-03-23T08:09:01.153 Processing User Information...
	2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    

### Тестирование функции триггера очереди с помощью кода: C# #

Мы уже упоминали, что можно протестировать триггер очереди с помощью кода для помещения сообщения в очередь. Следующий пример кода основан на коде C#, представленном в руководстве [Приступая к работе с хранилищем очередей Azure с помощью .NET](../storage/storage-dotnet-how-to-use-queues.md). По этой ссылке также доступен код для других языков.

Чтобы протестировать этот код в консольном приложении, необходимо сделать следующее.

- [Настройте строку подключения хранилища в файле app.config](../storage/storage-dotnet-how-to-use-queues.md#setup-a-storage-connection-string).
- Этот код принимает имя и адрес для нового пользователя в качестве аргументов командной строки во время выполнения. Передайте `name` и `address` в качестве параметров в приложение. Например, `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`


Пример кода C#:

    static void Main(string[] args)
    {
        string name = null;
        string address = null;
        string queueName = "queue-newusers";
        string JSON = null;

        if (args.Length > 0)
        {
            name = args[0];
        }
        if (args.Length > 1)
        {
            address = args[1];
        }

        // Retrieve storage account from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

        // Create the queue client
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

        // Retrieve a reference to a queue
        CloudQueue queue = queueClient.GetQueueReference(queueName);

        // Create the queue if it doesn't already exist
        queue.CreateIfNotExists();

        // Create a message and add it to the queue.
        if (name != null)
        {
            if (address != null)
                JSON = String.Format("{{"name":"{0}","address":"{1}"}}", name, address);
            else
                JSON = String.Format("{{"name":"{0}"}}", name);
        }

        Console.WriteLine("Adding message to " + queueName + "...");
        Console.WriteLine(JSON);

        CloudQueueMessage message = new CloudQueueMessage(JSON);
        queue.AddMessage(message);
    }

В окне браузера для функции очереди отобразится каждое обрабатываемое сообщение:

	2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
	2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
	2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
	2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[портал Azure]: https://portal.azure.com
[портале Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0921_2016-->