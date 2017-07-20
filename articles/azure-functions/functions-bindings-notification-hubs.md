---
title: "Выходная привязка центра уведомлений для Функций Azure | Документация Майкрософт"
description: "Узнайте, как использовать привязки центра уведомлений Azure в функциях Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: fa3d37b963c1bb6b58127b9180cd657d7b1dabcc
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017


---
# <a name="azure-functions-notification-hub-output-binding"></a>Выходная привязка центра уведомлений для функций Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать привязки центра уведомлений Azure в функциях Azure. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Функции могут отправлять push-уведомления всего несколькими строками кода, используя настроенный центр уведомлений Azure. Однако центр уведомлений Azure нужно настроить для служб уведомлений платформы (PNS), которые необходимо использовать. Чтобы получить дополнительные сведения о настройке центра уведомлений Azure и разработке клиентских приложений, которые регистрируют для получения уведомлений, перейдите к статье [Приступая к работе с центрами уведомлений](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) и щелкните необходимую клиентскую платформу вверху.

Можно отправлять собственные или шаблонные уведомления. Собственные уведомления нацелены на определенную платформу уведомлений, указанную в свойстве `platform` привязки для вывода. Шаблонное уведомление можно использовать для нескольких платформ.   

## <a name="notification-hub-output-binding-properties"></a>Свойства привязки для вывода центра уведомлений
Файл function.json содержит следующие свойства:

* `name` — имя переменной, используемой в коде функции для сообщения центра уведомлений.
* `type` — для этого свойства следует задать значение *notificationHub*.
* `tagExpression` — с помощью выражений тегов можно указать, что уведомления должны отправляться на устройства, зарегистрированные для получения уведомлений, соответствующих выражению тега.  Дополнительные сведения см. в статье [Маршрутизация и выражения тегов](../notification-hubs/notification-hubs-tags-segment-push-message.md).
* `hubName` — имя ресурса центра уведомлений на портале Azure.
* `connection` — строка подключения **параметра приложения** , для которой задано значение *DefaultFullSharedAccessSignature* для центра уведомлений.
* `direction` — для этого свойства необходимо задать значение *out*. 
* `platform` — свойство платформы, которое указывает целевую платформу уведомлений для ваших уведомлений. Необходимо установить одно из следующих значений.
  * По умолчанию, если свойство платформы отсутствует в выходной привязке, шаблонные уведомления могут использоваться для любой платформы, настроенной в концентраторе уведомлений Azure. Дополнительные общие сведения об использовании шаблонов для отправки кроссплатформенных уведомлений с помощью центра уведомлений Azure см. в разделе [Шаблоны](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
  * `apns` — служба push-уведомлений Apple. Дополнительные сведения о настройке центра уведомлений для APNs и получении уведомлений в клиентском приложении см. в разделе [Отправка push-уведомлений с помощью центров уведомлений Azure в iOS](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md). 
  * `adm` — [Amazon Device Messaging](https://developer.amazon.com/device-messaging). Дополнительные сведения о настройке центра уведомлений для ADM и получении уведомлений в приложении Kindle см. в разделе [Приступая к работе с Центрами уведомлений для приложений Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md). 
  * `gcm` — [Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Также поддерживается новая версия GCM, Firebase Cloud Messaging. Дополнительные сведения о настройке центра уведомлений для GCM или FCM и получении уведомлений в клиентском приложении Android см. в разделе [Отправка push-уведомлений в приложения Android с помощью центров уведомлений Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).
  * `wns` — [службы push-уведомлений Windows](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) для различных платформ Windows. Службы WNS поддерживают также Windows Phone 8.1 и более поздние версии. Дополнительные сведения о настройке центра уведомлений для WNS и получении уведомлений в приложении универсальной платформы Windows (UWP) см. в разделе [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
  * `mpns` — [служба push-уведомлений Майкрософт](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Данная платформа поддерживает Windows Phone 8 и более ранние версии платформ Windows Phone. Дополнительные сведения о настройке центра уведомлений для MPNS и получении уведомлений в приложении Windows Phone см. в разделе [Отправка push-уведомлений в приложения Windows Phone с помощью центров уведомлений Azure](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).

Пример файла function.json:

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>Настройка строки подключения центра уведомлений
Для использования привязки для вывода центра уведомлений необходимо настроить отдельную строку подключения. Это можно сделать на вкладке *Интеграция*, выбрав центр уведомлений или создав его. 

Можно также вручную добавить строку подключения для имеющегося центра, указав ее в качестве значения *DefaultFullSharedAccessSignature* для центра уведомлений. Эта строка подключения предоставляет разрешение на доступ к функции для отправки сообщений с уведомлениями. Для доступа к строке подключения *DefaultFullSharedAccessSignature* нажмите кнопку **Ключи** в главной колонке для ресурса центра уведомлений на портале Azure. Чтобы добавить строку подключения для центра вручную, сделайте следующее: 

1. В колонке **Приложение-функция** на портале Azure щелкните **Параметры приложения-функции > Перейти к параметрам службы приложений**.
2. В колонке **Параметры** щелкните раздел **Параметры приложения**.
3. Прокрутите страницу вниз до раздела **Параметры приложения** и добавьте именованную запись в качестве значения *DefaultFullSharedAccessSignature* для центра уведомлений.
4. Укажите имя строки параметров приложения в выходных привязках. Это имя должно быть аналогично строке **MyHubConnectionString**, использованной в приведенном выше примере.

## <a name="apns-native-notifications-with-c-queue-triggers"></a>Использование собственных уведомлений APNs с триггерами очереди на языке C#
В этом примере показано, как использовать типы, определенные в [библиотеке центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/), для отправки собственного уведомления APNs. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>Использование собственных уведомлений GCM с триггерами очереди на языке C#
В этом примере показано, как использовать типы, определенные в [библиотеке центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/), для отправки собственного уведомления GCM. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>Использование собственных уведомлений WNS с триггерами очереди на языке C#
В этом примере показано, как использовать типы, определенные в [библиотеке центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/), для отправки всплывающего уведомления WNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Пример шаблона для триггеров таймера на основе Node.js
В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), содержащего свойства `location` и `message`.

```javascript
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
```

## <a name="template-example-for-f-timer-triggers"></a>Пример шаблона для триггеров таймера на языке F#
В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), содержащего свойства `location` и `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>Пример шаблона с параметром вывода
В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), содержащего заполнитель `message`.

```cs
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
```

## <a name="template-example-with-asynchronous-function"></a>Пример шаблона с асинхронной функцией
При использовании асинхронного кода параметры вывода не допускаются. В этом случае для возвращения шаблонного уведомления следует использовать `IAsyncCollector`. Ниже приведен пример асинхронного кода, описанного выше. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>Пример шаблона с использованием JSON
В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), содержащего заполнитель `message` и использующего допустимую строку JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>Пример шаблона, в котором используются типы из библиотеки центров уведомлений
В этом примере показано, как использовать типы, определенные в [библиотеке центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

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
```

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


