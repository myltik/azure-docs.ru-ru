---
title: Тестирование Функций Azure | Документация Майкрософт
description: Тестирование функций Azure с помощью Postman, cURL и Node.js.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, webhook, динамические вычисления, независимая архитектура, тестирование
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41796a8cdde0756e5157ba276463a56b07679d04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "22992199"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Методика тестирования кода с помощью Функций Azure

В этом разделе показаны различные способы тестирования функций, включая следующие общие подходы:

+ инструменты на основе HTTP, например cURL, Postman и даже веб-браузер для веб-триггеров;
+ Azure Storage Explorer для тестирования триггеров на основе службы хранилища Azure;
+ вкладка тестирования на портале функций Azure;
+ активируемая по таймеру функция;
+ приложение или платформа для тестирования.

Все эти методы тестирования используют функцию триггера HTTP, которая принимает входные данные через параметр строки запроса или текст запроса. Эта функция создается в первом разделе.

## <a name="create-a-function-for-testing"></a>Создание функции для тестирования
В большей части этого руководства мы будем использовать немного измененную версию шаблона функции JavaScript HttpTrigger, который доступен при создании функции. Если вам нужна помощь при создании функции, просмотрите этот [учебник](functions-create-first-azure-function.md). Выберите шаблон **HttpTrigger- JavaScript** при создании тестовой функции на [портал Azure].

Используемый по умолчанию шаблон функции по сути представляет собой функцию hello world, выводящую на экран имя из текста запроса или параметра строки запроса `name=<your name>`.  Мы обновим код, позволив указать имя и адрес в качестве содержимого JSON в тексте запроса. Затем функция выведет эти значения обратно на клиент при его наличии.   

Добавьте в функцию следующий код, который будет использоваться для тестирования:

```javascript
module.exports = function (context, req) {
    context.log("HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
    context.log("Request Headers = " + JSON.stringify(req.headers));
    var res;

    if (req.query.name || (req.body && req.body.name)) {
        if (typeof req.query.name != "undefined") {
            context.log("Name was provided as a query string param...");
            res = ProcessNewUserInformation(context, req.query.name);
        }
        else {
            context.log("Processing user info from request body...");
            res = ProcessNewUserInformation(context, req.body.name, req.body.address);
        }
    }
    else {
        res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done(null, res);
};
function ProcessNewUserInformation(context, name, address) {
    context.log("Processing user information...");
    context.log("name = " + name);
    var echoString = "Hello " + name;
    var res;

    if (typeof address != "undefined") {
        echoString += "\n" + "The address you provided is " + address;
        context.log("address = " + address);
    }
    res = {
        // status: 200, /* Defaults to 200 */
        body: echoString
    };
    return res;
}
```

## <a name="test-a-function-with-tools"></a>Тестирование функции с помощью инструментов
За пределами портала Azure существуют различные инструменты, которые можно использовать для активации функций в целях тестирования. К ним относятся инструменты тестирования HTTP на основе пользовательского интерфейса или командной строки, средства доступа к службе хранилища Azure и даже простой веб-браузер.

### <a name="test-with-a-browser"></a>Тестирование с помощью браузера
Веб-браузер — это простой способ активации функций с помощью протокола HTTP. Браузер можно использовать для запросов GET, в которых не требуются полезные данные текста, а применяются только параметры строки запроса.

Чтобы протестировать определенную выше функцию, скопируйте **URL-адрес функции** с портала. В нем используется следующий формат:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Добавьте параметр `name` к строке запроса. Используйте фактическое имя для заполнителя `<Enter a name here>`.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Вставьте URL-адрес в браузер. Должен отобразиться примерно такой ответ.

![Снимок экрана: вкладка браузера Chrome с ответом теста](./media/functions-test-a-function/browser-test.png)

В данном примере используется браузер Chrome, который заключает возвращаемую строку в формат XML. Другие браузеры отображают только строковое значение.

В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Тестирование с помощью приложения Postman
Рекомендуемым инструментом для тестирования большинства функций является приложение Postman, которое интегрируется с браузером Chrome. Чтобы установить Postman, см. веб-страницу, на которой можно [получить приложение Postman](https://www.getpostman.com/). Приложение Postman позволяет контролировать намного больше атрибутов HTTP-запроса.

> [!TIP]
> Используйте инструмент для тестирования на основе HTTP, который для вас наиболее удобен. Вот некоторые альтернативы Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Paw.](https://luckymarmot.com/paw)  
>
>

Чтобы протестировать функцию с текстом запроса в приложении Postman, выполните следующие действия.

1. Запустите Postman нажатием кнопки **Приложения** в левом верхнем углу окна браузера Chrome.
2. Скопируйте **URL-адрес функции** и вставьте его в Postman. В нем содержится параметр строки запроса — код доступа.
3. Замените метод HTTP на **POST**.
4. Щелкните **Body** > **raw** (Текст > Необработанный) и добавьте текст запроса JSON, подобный следующему:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Нажмите кнопку **Send**(Отправить).

На следующем рисунке показано тестирование простого примера эхо-функции из этого руководства.

![Снимок экрана: пользовательский интерфейс Postman](./media/functions-test-a-function/postman-test.png)

В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>Тестирование с помощью cURL из командной строки
Часто при тестировании программного обеспечения для отладки приложения достаточно командной строки. То же самое справедливо и для функций. Обратите внимание, что инструмент cURL по умолчанию доступен в системах на основе Linux. При использовании Windows [инструмент cURL](https://curl.haxx.se/) необходимо сначала скачать и установить.

Чтобы протестировать определенную выше функцию, скопируйте **URL-адрес функции** с портала. В нем используется следующий формат:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Это URL-адрес для активации функции. Протестируйте его, используя команду cURL в командной строке, чтобы выполнить запрос GET (`-G` или `--get`) к функции:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

В этом конкретном примере требуется параметр строки запроса, который может быть передан как данные (`-d`) в команде cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Выполните команду, и вы увидите выходные данные функции в командной строке:

![Снимок экрана: выходные данные в командной строке](./media/functions-test-a-function/curl-test.png)

В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Тестирование триггера больших двоичных объектов с помощью Storage Explorer
Функцию триггера больших двоичных объектов можно протестировать с помощью [Azure Storage Explorer](http://storageexplorer.com/).

1. На [портал Azure] для приложения-функции создайте функцию триггера больших двоичных объектов на языке C#, F# или JavaScript. Задайте путь для отслеживания имени контейнера больших двоичных объектов. Например: 

        files
2. Нажмите кнопку **+** , чтобы выбрать или создать учетную запись хранения, которую следует использовать. Затем щелкните **Создать**.
3. Создайте текстовый файл со следующим содержимым и сохраните его.

        A text file for blob trigger function testing.
4. Запустите [Azure Storage Explorer](http://storageexplorer.com/) и подключитесь к контейнеру больших двоичных объектов в отслеживаемой учетной записи хранения.
5. Нажмите кнопку **Отправить**, чтобы отправить текстовый файл.

    ![Снимок экрана: Storage Explorer](./media/functions-test-a-function/azure-storage-explorer-test.png)

Используемый по умолчанию код функции триггера больших двоичных объектов сообщит об обработке большого двоичного объекта в журналах:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Тестирование функции в рамках функций
Портал Функций Azure разработан, чтобы помочь в тестировании функций, активируемых с помощью протокола HTTP или по таймеру. Также на нем можно создавать функции для активации других тестируемых функций.

### <a name="test-with-the-functions-portal-run-button"></a>Тестирование с помощью кнопки "Выполнить" на портале функций
На портале есть кнопка **Выполнить**, которая позволяет провести ограниченное тестирование. С помощью этой кнопки можно указать текст запроса, но нельзя указать параметры строки запроса или обновить заголовки запроса.

Протестируйте функцию HTTP-триггера, созданную ранее, добавив в поле **Текст запроса** строку JSON, подобную указанной ниже. Затем нажмите кнопку **Выполнить**.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Тестирование с помощью триггера таймера
Некоторые функции нельзя правильно протестировать с помощью упомянутых ранее средств. Например, функцию триггера очереди, которая выполняется при поступлении сообщения в [хранилище очередей Azure](../storage/queues/storage-dotnet-how-to-use-queues.md). Всегда можно написать код, помещающий сообщение в очередь, пример которого указан в консольном проекте далее в этой статье. Однако существует другой подход, который можно использовать для непосредственного тестирования функций.  

Можно использовать триггер таймера, настроенный с привязкой для вывода очереди. Такой код триггера таймера может затем записывать тестовые сообщения в очередь. В этом разделе описан пример.

Более подробные сведения об использовании привязок с Функциями Azure см. в статье [Справочник разработчика по Функциям Azure](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Создание триггера очереди для тестирования
Чтобы продемонстрировать этот подход, мы сначала создадим функцию триггера очереди, которую нам нужно протестировать, для очереди с именем `queue-newusers`. Эта функция обрабатывает имя и адрес нового пользователя, поступившие в хранилище очередей.

> [!NOTE]
> При использовании другого имени очереди убедитесь, что используемое имя соответствует правилам [именования очередей и метаданных](https://msdn.microsoft.com/library/dd179349.aspx). В противном случае возникает ошибка.
>
>

1. На [портал Azure] для приложения-функции щелкните **Новая функция** > **QueueTrigger — C#**.
2. Введите имя очереди, которое будет отслеживаться функцией очереди:

        queue-newusers
3. Нажмите кнопку **+** , чтобы выбрать или создать учетную запись хранения, которую следует использовать. Затем щелкните **Создать**.
4. Оставьте это окно браузера с порталом открытым, чтобы можно было отслеживать записи журнала на наличие стандартного кода шаблона функции очереди.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Создание триггера таймера для помещения сообщения в очередь
1. Откройте [портал Azure] в новом окне браузера и перейдите к приложению-функции.
2. Щелкните **Новая функция** > **TimerTrigger — C#**. Введите выражение CRON, чтобы задать частоту выполнения кода таймера при тестировании функции очереди. Затем щелкните **Создать**. Если тестирование нужно выполнять каждые 30 секунд, можно использовать следующее [выражение CRON](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Выберите вкладку **Интеграция** для нового триггера таймера.
4. В разделе **Выходные данные** щелкните **+ Новое выходное значение**. Затем последовательно щелкните **Очередь** и **Выбрать**.
5. Запишите имя, используемое для **объекта сообщения очереди**. Оно будет использоваться в коде функции таймера.

        myQueue
6. Введите имя очереди, в которую будет отправлено сообщение:

        queue-newusers
7. Нажмите кнопку **+**, чтобы выбрать учетную запись хранения, указанную ранее в триггере очереди. Нажмите кнопку **Сохранить**.
8. Выберите вкладку **Разработка** для триггера таймера.
9. Для функции таймера на языке C# можно использовать приведенный ниже код, если вы использовали то же имя объекта сообщения очереди, указанное выше. Нажмите кнопку **Сохранить**.

    ```cs
    using System;

    public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
    {
        String newUser =
        "{\"name\":\"User testing from C# timer function\",\"address\":\"XYZ\"}";

        log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
        log.Verbose($"{newUser}");   

        myQueue = newUser;
    }
    ```

На этом этапе функция таймера на C# выполняется каждые 30 секунд, если вы использовали выражение CRON из примера. Журналы для функции таймера сообщают о каждом выполнении:

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

В окне браузера для функции очереди отображается каждое обрабатываемое сообщение:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>Тестирование функции с помощью кода
Вам может потребоваться создать внешнее приложение или платформу для тестирования функций.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Тестирование функции HTTP-триггера с помощью кода: Node.js
Чтобы выполнить HTTP-запрос для тестирования функции, можно использовать приложение Node.js.
Не забудьте задать следующие значения:

* для `host` в параметрах запроса — узел приложения функции;
* имя функции в параметре `path`;
* код доступа (`<your code>`) в параметре `path`.

Пример кода:

```javascript
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
```


Выходные данные:

    C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
    *** Sending name and address in body ***
    {"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
    Hello Wes testing with Node.JS code
    The address you provided is Dallas, T.X. 75201

В окне **Журналы** на портале при выполнении функции регистрируются выходные данные, подобные следующим:

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>Тестирование функции триггера очереди с помощью кода: C# #
Мы уже упоминали, что триггер очереди можно протестировать с помощью кода, поместив сообщение в очередь. Следующий пример кода основан на коде C#, представленном в руководстве [Приступая к работе с хранилищем очередей Azure с помощью .NET](../storage/queues/storage-dotnet-how-to-use-queues.md). По этой ссылке также доступен код для других языков.

Чтобы протестировать этот код в консольном приложении, необходимо сделать следующее:

* [Настройте строку подключения хранилища в файле app.config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Передайте `name` и `address` в качестве параметров в приложение. Например, `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Этот код принимает имя и адрес для нового пользователя в качестве аргументов командной строки во время выполнения.)

Пример кода C#:

```cs
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
            JSON = String.Format("{{\"name\":\"{0}\",\"address\":\"{1}\"}}", name, address);
        else
            JSON = String.Format("{{\"name\":\"{0}\"}}", name);
    }

    Console.WriteLine("Adding message to " + queueName + "...");
    Console.WriteLine(JSON);

    CloudQueueMessage message = new CloudQueueMessage(JSON);
    queue.AddMessage(message);
}
```

В окне браузера для функции очереди отображается каждое обрабатываемое сообщение:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[портал Azure]: https://portal.azure.com
