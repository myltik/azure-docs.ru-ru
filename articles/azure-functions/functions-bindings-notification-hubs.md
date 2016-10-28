<properties
	pageTitle="Выходная привязка центра уведомлений для функций Azure | Microsoft Azure"
	description="Узнайте, как использовать привязки центра уведомлений Azure в функциях Azure."
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
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Выходная привязка центра уведомлений для функций Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать привязки центра уведомлений Azure в функциях Azure.

[AZURE.INCLUDE [общие сведения](../../includes/functions-bindings-intro.md)]

Функции могут отправлять push-уведомления в нескольких строках кода, используя настроенный центр уведомлений Azure. Однако центр уведомлений нужно настроить для служб уведомлений платформы (PNS), которые необходимо использовать. Чтобы получить дополнительные сведения о настройке центра уведомлений Azure и разработке клиентских приложений, которые регистрируют для получения уведомлений, перейдите к статье [Приступая к работе с центрами уведомлений](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) и щелкните необходимую клиентскую платформу вверху.

## Файл function.json для выходной привязки центра уведомлений Azure

Файл function.json содержит следующие свойства:

- `name` — имя переменной, используемой в коде функции для сообщения центра уведомлений.
- `type` — для этого свойства следует задать значение *notificationHub*.
- `tagExpression` — с помощью выражений тегов можно указать, что уведомления должны отправляться на устройства, зарегистрированные для получения уведомлений, соответствующих выражению тега. Дополнительные сведения см. в статье [Маршрутизация и выражения тегов](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName` — имя ресурса центра уведомлений на портале Azure.
- `connection` — строка подключения **параметра приложения**, для которой задано значение *DefaultFullSharedAccessSignature* для центра уведомлений.
- `direction` — для этого свойства необходимо задать значение *out*.
 
Пример файла function.json:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

## Настройка строки подключения к центру уведомлений Azure

Для использования выходной привязки центра уведомлений необходимо настроить отдельную строку подключения. Это можно сделать на вкладке *Интеграция*, просто выбрав центр уведомлений или создав его.

Можно также вручную добавить строку подключения для имеющегося центра, указав ее в качестве значения *DefaultFullSharedAccessSignature* для центра уведомлений. Эта строка подключения предоставляет разрешение на доступ к функции для отправки сообщений с уведомлениями. Для доступа к строке подключения *DefaultFullSharedAccessSignature* нажмите кнопку **Ключи** в главной колонке для ресурса центра уведомлений на портале Azure. Чтобы добавить строку подключения для центра вручную, сделайте следующее:

1. В колонке **Приложение-функция** на портале Azure щелкните **Function App Settings > Go to App Service settings** (Параметры приложений-функций > Перейти к параметрам службы приложений).

2. В колонке **Параметры** щелкните раздел **Параметры приложения**.

3. Прокрутите вниз до раздела **Строки подключения** и добавьте именованную запись в качестве значения *DefaultFullSharedAccessSignature* для центра уведомлений. Измените тип на **Пользовательский**.
4. Укажите имя строки подключения в выходных привязках, Это имя должно быть аналогично строке **MyHubConnectionString**, использованной в приведенном выше примере.

## Пример кода для центра уведомлений Azure для триггера таймера Node.js 

В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), содержащее свойства `location` и `message`.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

## Пример кода для центра уведомлений Azure для триггера таймера F#

В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), содержащее свойства `location` и `message`.

	let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
	    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]

## Пример кода для центра уведомлений Azure для триггера очереди C#

В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), содержащее свойство `message`.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), содержащее свойство `message`, с использованием действительной строки JSON.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

## Пример кода C# для триггера очереди для центра уведомлений Azure с использованием типа Notification

В этом примере показано, как использовать тип `Notification`, определенный в [библиотеке центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Чтобы использовать этот тип и библиотеку, необходимо отправить файл *project.json* для приложения-функции. Файл project.json — это текстовый JSON-файл, который будет выглядеть следующим образом:

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

Дополнительные сведения об отправке файла project.json см. в разделе об [отправке файла project.json](functions-reference.md#fileupdate).

Пример кода:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## Дальнейшие действия

[AZURE.INCLUDE [дальнейшие действия](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->