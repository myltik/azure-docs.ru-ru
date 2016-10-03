<properties
	pageTitle="Привязки концентратора событий функций Azure | Microsoft Azure"
	description="Узнайте, как использовать привязки концентратора событий Azure в функциях Azure."
	services="functions"
	documentationCenter="na"
	authors="wesmc7777"
	manager="erikre"
	editor=""
	tags=""
	keywords="функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/22/2016"
	ms.author="wesmc"/>

# Привязки концентратора событий функций Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать привязки [концентратора событий Azure](../event-hubs/event-hubs-overview.md) для Функций Azure. Функции Azure поддерживают привязки триггера и выходные привязки для концентраторов событий Azure.

[AZURE.INCLUDE [общие сведения](../../includes/functions-bindings-intro.md)]


## Привязка триггера концентратора событий Azure

Триггер концентратора событий Azure может использоваться для ответа на событие, отправленное в поток событий концентратора событий. Чтобы настроить привязку триггера, необходимо иметь доступ для чтения к концентратору событий.

#### Файл function.json для привязки триггера концентратора событий

Файл *function.json* для триггера концентратора событий Azure указывает следующие свойства:

- `type` — для этого свойства необходимо задать значение *eventHubTrigger*.
- `name` — имя переменной, используемой в коде функции для сообщения концентратора событий.
- `direction` — для этого свойства нужно задать значение *in*.
- `path` — имя концентратора событий.
- `connection` — имя параметра приложения, содержащего строку подключения к пространству имен, в котором находится концентратор событий. Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для пространства имен, а не сам концентратор событий. Для активации триггера эта строка подключения должна обладать, по крайней мере, правами на чтение.

		{
		  "bindings": [
		    {
		      "type": "eventHubTrigger",
		      "name": "myEventHubMessage",
		      "direction": "in",
		      "path": "MyEventHub",
		      "connection": "myEventHubReadConnectionString"
		    }
		  ],
		  "disabled": false
		}

#### Пример кода на языке C# для триггера концентратора событий Azure
 
Используя пример файла function.json, приведенный выше, текст сообщения о событии будет записан в журнал с помощью следующего кода функции C#:
 
	using System;
	
	public static void Run(string myEventHubMessage, TraceWriter log)
	{
	    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
	}

#### Пример кода на языке F# для триггера концентратора событий Azure

В рамках примера файла function.json, приведенного выше, текст сообщения о событии будет записан в журнал с помощью следующего кода функции F#:

	let Run(myEventHubMessage: string, log: TraceWriter) =
	    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### Пример триггера концентратора событий Azure на платформе Node.js
 
Используя пример файла function.json, приведенный выше, текст сообщения о событии будет записан в журнал с помощью следующего кода функции Node.js:
 
	module.exports = function (context, myEventHubMessage) {
	    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);	
	    context.done();
	};


## Выходная привязка концентратора событий Azure

Выходная привязка концентратора событий Azure используется для записи событий в поток событий концентратора событий. Чтобы записывать события в концентратор событий, необходимо иметь разрешение на оправку в него событий.

#### Файл function.json для выходной привязки концентратора событий

Файл *function.json* для выходной привязки концентратора событий Azure указывает следующие свойства:

- `type` — для этого свойства необходимо задать значение *eventHub*.
- `name` — имя переменной, используемой в коде функции для сообщения концентратора событий.
- `path` — имя концентратора событий.
- `connection` — имя параметра приложения, содержащего строку подключения к пространству имен, в котором находится концентратор событий. Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для пространства имен, а не сам концентратор событий. Чтобы отправлять сообщения в поток концентратора событий, эта строка подключения должна иметь разрешения на оправку.
- `direction` — для этого свойства необходимо задать значение *out*.

	    {
	      "type": "eventHub",
	      "name": "outputEventHubMessage",
	      "path": "myeventhub",
	      "connection": "MyEventHubSend",
	      "direction": "out"
	    }


#### Пример кода на языке C# для выходной привязки концентратора событий Azure
 
Следующий пример кода функции на языке C# демонстрирует запись события в поток событий концентратора событий. В этом примере представлена выходная привязка концентратора событий, показанная выше, которую применили к триггеру таймера C#.
 
	using System;
	
	public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
	{
	    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
	
	    log.Verbose(msg);   
	    
	    outputEventHubMessage = msg;
	}

#### Пример кода на языке F# для выходной привязки концентратора событий Azure

Следующий пример кода функции на языке F# демонстрирует запись события в поток событий концентратора событий. В этом примере представлена выходная привязка концентратора событий, показанная выше, которую применили к триггеру таймера C#.

	let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
	    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
	    log.Verbose(msg);
	    outputEventHubMessage <- msg;

#### Пример кода на платформе Node.js для выходной привязки концентратора событий Azure
 
Следующий пример кода функции на платформе Node.js демонстрирует запись события в поток событий концентратора событий. В этом примере представлена выходная привязка концентратора событий, показанная выше, которую применили к триггеру таймера Node.js.
 
	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	    
	    if(myTimer.isPastDue)
	    {
	        context.log('TimerTriggerNodeJS1 is running late!');
	    }

	    context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
	    
	    context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
	
	    context.done();
	};

## Дальнейшие действия

[AZURE.INCLUDE [дальнейшие действия](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->