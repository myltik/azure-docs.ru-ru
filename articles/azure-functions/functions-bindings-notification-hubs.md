---
title: "Выходная привязка центра уведомлений для Функций Azure | Документация Майкрософт"
description: "Узнайте, как использовать привязки центра уведомлений Azure в функциях Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: glenga
ms.openlocfilehash: 02d01d0f6e945ed54dbe766aec2a0fd7c17c510f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-notification-hub-output-binding"></a>Выходная привязка центра уведомлений для функций Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать привязки центра уведомлений Azure в функциях Azure. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Функции могут отправлять push-уведомления всего несколькими строками кода, используя настроенный центр уведомлений Azure. Однако центр уведомлений Azure нужно настроить для служб уведомлений платформы (PNS), которые необходимо использовать. Чтобы получить дополнительные сведения о настройке центра уведомлений Azure и разработке клиентских приложений, которые регистрируют для получения уведомлений, перейдите к статье [Приступая к работе с центрами уведомлений](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) и щелкните необходимую клиентскую платформу вверху.

Можно отправлять собственные или шаблонные уведомления. Собственные уведомления нацелены на определенную платформу уведомлений, указанную в свойстве `platform` привязки для вывода. Шаблонное уведомление можно использовать для нескольких платформ.   

## <a name="notification-hub-output-binding-properties"></a>Свойства привязки для вывода центра уведомлений
Файл function.json содержит следующие свойства:


|Свойство  |Описание  |
|---------|---------|
|**name** | Имя переменной, используемой в коде функции для сообщения Центра уведомлений. |
|**type** | Нужно задать значение `notificationHub`. |
|**tagExpression** | Выражения тегов позволяют указать, что уведомления должны отправляться на устройства, зарегистрированные для получения уведомлений, соответствующих выражению тега.  Дополнительные сведения см. в статье [Маршрутизация и выражения тегов](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | Имя ресурса Центра уведомлений на портале Azure. |
|**подключение** | Строка подключения **параметра приложения** со значением *DefaultFullSharedAccessSignature* Центра уведомлений. |
|**direction** | Нужно задать значение `out`. | 
|**platform** | Свойство platform указывает целевую платформу уведомлений ваших уведомлений. По умолчанию, если свойство платформы отсутствует в выходной привязке, шаблонные уведомления могут использоваться для любой платформы, настроенной в концентраторе уведомлений Azure. Дополнительные общие сведения об использовании шаблонов для отправки кроссплатформенных уведомлений с помощью центра уведомлений Azure см. в разделе [Шаблоны](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Если свойство _platform_ задано, оно должно иметь одно из следующих значений: <ul><li><code>apns</code>&mdash; — Cлужба push-уведомлений Apple. Дополнительные сведения о настройке Центра уведомлений для APNs и получении уведомлений в клиентском приложении см. в статье [Отправка push-уведомлений с помощью Центра уведомлений Azure в iOS](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash; — [Amazon Device Messaging](https://developer.amazon.com/device-messaging). Дополнительные сведения о настройке Центра уведомлений для ADM и получении уведомлений в приложении Kindle см. в статье [Приступая к работе с Центрами уведомлений для приложений Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash; — [Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Также поддерживается новая версия GCM, Firebase Cloud Messaging. Дополнительные сведения см. в статье [Отправка push-уведомлений в приложения Android с помощью Центра уведомлений Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash; — [службы Microsoft SQL Server Notification Services](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) для различных платформ Windows. Службы WNS поддерживают также Windows Phone 8.1 и более поздние версии. Дополнительные сведения см. в статье [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash; — [служба push-уведомлений (Майкрософт)](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Данная платформа поддерживает Windows Phone 8 и более ранние версии платформ Windows Phone. Дополнительные сведения см. в статье [Отправка push-уведомлений в приложения Windows Phone с помощью Центров уведомлений Azure](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

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
Для использования привязки для вывода центра уведомлений необходимо настроить отдельную строку подключения. Вы можете создать Центр уведомлений на вкладке *Интегрировать* функции или выбрать имеющийся. Кроме того, строку подключения можно настроить вручную. 

Настройка строки подключения к имеющемуся Центру уведомлений

1. Перейдите к Центру уведомлений на [портале Azure](https://portal.azure.com). Выберите **Политики доступа**, а затем нажмите кнопку копирования рядом с политикой **DefaultFullSharedAccessSignature**. Это позволит скопировать строку подключения политики *DefaultFullSharedAccessSignature* к вашему Центру уведомлений. Эта строка подключения предоставляет разрешение на доступ к функции для отправки сообщений с уведомлениями. 
    ![Копирование строки подключения к Центру уведомлений](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Перейдите к приложению-функции на портале Azure. Выберите **Параметры приложения**, добавьте ключ, например `MyHubConnectionString`, вставьте скопированную строку подключения политики *DefaultFullSharedAccessSignature* к Центру уведомлений как значение, а затем нажмите кнопку **Сохранить**.

Теперь вы можете использовать этот параметр приложения, определяющий подключение Центра уведомлений в выходной привязке.

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

