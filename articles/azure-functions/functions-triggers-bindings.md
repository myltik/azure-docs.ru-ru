---
title: "Триггеры и привязки в Функциях Azure | Документация Майкрософт"
description: "Узнайте, как использовать триггеры и привязки в Функциях Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: d809636cae48dd0ccca4c99370f41996430d89e4
ms.openlocfilehash: b5d8d38f03514d89bf6c0b5e36adf0379f1bef1a


---
# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Справочник разработчика по триггерам и привязкам в Функциях Azure
Этот раздел содержит общие справочные сведения о триггерах и привязках. В нем также рассматриваются некоторые расширенные функции привязки и синтаксис, поддерживаемые всеми типами привязки.  

Если же вам нужны подробные сведения о настройке и программировании определенного типа триггера или привязки, вы можете щелкнуть какой-либо триггер или привязку из указанных ниже.

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этих статьях предполагается, что вы уже прочли [справочник разработчика по функциям Azure](functions-reference.md), а также справочники разработчика по [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) и [Node.js](functions-reference-node.md).

## <a name="overview"></a>Обзор
Триггеры позволяют реагировать на события, активируя пользовательский код. С их помощью можно реагировать на события платформы Azure или локальной среды. Привязки представляют собой необходимые метаданные, используемые для подключения вашего кода к нужному триггеру или связанным входным или выходным данным. Файл *Function.json* для каждой функции содержит все связанные привязки. Функция может содержать неограниченное число входных и выходных привязок. Однако для каждой функции поддерживается только одна привязка триггера.  

Чтобы получить более точное представление о различных привязках, которые можно интегрировать с приложением-функцией Azure, ознакомьтесь со следующей таблицей.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Чтобы лучше понять, что из себя представляют триггеры и привязки, предположим, что вам нужно выполнить код для обработки нового элемента, попавшего в очередь службы хранилища Azure. Для этого в Функциях Azure предоставлен триггер очереди Azure. Для наблюдения за очередью потребуются следующие сведения.

* Учетная запись хранения, в которой имеется очередь.
* Имя очереди.
* Имя переменной, которая будет указана в коде для ссылки на новый элемент, переданный в очередь.  

Привязка триггера очереди содержит эти сведения для функции Azure. Ниже приведен пример файла *function.json*, содержащего привязку триггера очереди. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ],
  "disabled": false
}
```

Ваш код может отправлять выходные данные разного типа в зависимости от того, как происходит обработка нового элемента очереди. Например, может потребоваться добавление новой записи в таблицу службы хранилища Azure.  Для этого можно настроить привязку для вывода для таблицы службы хранилища Azure. Ниже приведен пример файла *function.json*, содержащий привязку для вывода к таблице службы хранилища, которую может использовать триггер очереди. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "name": "myNewUserTableBinding",
      "tableName": "newUserTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Приведенная ниже функция C# реагирует на добавление нового элемента в очередь и добавляет новую запись пользователя в таблицу службы хранилища Azure.

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                TraceWriter log)
{
    // In this example the queue item is a JSON string representing an order that contains the name, 
    // address and mobile number of the new customer.
    dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);

    await myNewUserTableBinding.AddAsync(
        new Person() { 
            PartitionKey = "Test", 
            RowKey = Guid.NewGuid().ToString(), 
            Name = order.name,
            Address = order.address,
            MobileNumber = order.mobileNumber }
        );
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string MobileNumber { get; set; }
}
```

Дополнительные примеры кода и более подробные сведения о поддерживаемых типах службы хранилища Azure см. в разделе [Триггеры и привязки для службы хранилища Azure в функциях Azure](functions-bindings-storage.md).

Чтобы использовать расширенные возможности привязки на портале Azure, щелкните **Расширенный редактор** на вкладке **Интеграция** своей функции. Расширенный редактор позволяет редактировать файл *function.json* прямо на портале.

## <a name="random-guids"></a>Случайные значения GUID
Функции Azure предоставляют синтаксис для создания случайных значений GUID с использованием привязок. Ниже приведен синтаксис привязки, которая позволит записывать выходные данные в большой двоичный объект с уникальным именем в контейнере службы хранилища Azure. 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```


## <a name="returning-a-single-output"></a>Возвращение одного результата
В случаях, когда код функции возвращает один результат, можно использовать привязку для вывода `$return`, чтобы сохранить в коде более естественную сигнатуру функции. Это может использоваться только в языках, поддерживающих возвращаемое значение (C#, Node.js, F#). Привязка будет аналогична приведенной ниже привязке для вывода в большой двоичный объект, используемой для триггера очереди.

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "name": "input",
      "direction": "in",
      "queueName": "test-input-node"
    },
    {
      "type": "blob",
      "name": "$return",
      "direction": "out",
      "path": "test-output-node/{id}"
    }
  ]
}
```

Приведенный код C# возвращает выходные данные более естественным образом, не используя параметр `out` в сигнатуре функции.

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

Пример асинхронной функции:

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```


Этот же подход демонстрируется ниже для Node.js.

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

Ниже также приведен пример для F#.

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

Кроме того, описанный подход можно использовать для нескольких параметров вывода, указав один результат с помощью `$return`.

## <a name="resolving-app-settings"></a>Разрешение параметров приложения
Рекомендуется сохранять конфиденциальные сведения как часть среды выполнения, используя параметры приложения. Не сохраняя конфиденциальные сведения в файлах конфигурации приложения, вы снижаете риск при использовании общедоступного репозитория для хранения файлов приложения.  

Среда выполнения Функций Azure разрешает параметры приложения к значениям, если перед именем параметра приложения и после него указан знак процента (`%your app setting%`). Следующая [привязка Twilio](functions-bindings-twilio.md) использует параметр приложения `TWILIO_ACCT_PHONE` для поля `from` привязки. 

```json
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received Node.js",
  "direction": "out"
},
```



## <a name="parameter-binding"></a>Привязка параметра
Вместо настройки статической конфигурации свойств привязки для вывода можно настроить динамическую зависимость свойств от данных, являющуюся частью привязки для ввода триггера. Рассмотрим ситуацию, когда новые заказы обрабатываются с использованием очереди службы хранилища Azure. Каждый новый элемент очереди представляет собой строку JSON, содержащую по крайней мере приведенные ниже свойства.

```json
{
  "name" : "Customer Name",
  "address" : "Customer's Address".
  "mobileNumber" : "Customer's mobile number in the format - +1XXXYYYZZZZ."
}
```

Вам может понадобиться отправить клиенту SMS-сообщение с помощью учетной записи Twilio, чтобы уведомить о получении заказа.  Можно настроить динамическую связь между полями `body` и `to` привязки для вывода Twilio и значениями `name` и `mobileNumber`, которые были частью входных данных, следующим образом.

```json
{
  "name": "myNewOrderItem",
  "type": "queueTrigger",
  "direction": "in",
  "queueName": "queue-newOrders",
  "connection": "orders_STORAGE"
},
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received",
  "direction": "out"
},
```

Теперь вашему коду функции достаточно инициализировать параметр вывода, как показано ниже. Во время выполнения свойства вывода будут привязаны к требуемым входным данным.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using System.Threading.Tasks;
using Newtonsoft.Json;
using Twilio;

public static async Task<SMSMessage> Run(string myNewOrderItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myNewOrderItem}");

    dynamic order = JsonConvert.DeserializeObject(myNewOrderItem);    

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // The following isn't needed since we use parameter binding for this
    //string msg = "Hello " + order.name + ", thank you for your order.";
    //smsText.Body = msg;
    //smsText.To = order.mobileNumber;

    return smsText;
}
```

Node.js:

```javascript
module.exports = function (context, myNewOrderItem) {    
    context.log('Node.js queue trigger function processed work item', myNewOrderItem);    

    // No need to set the properties of the text, we use parameters in the binding. We do need to 
    // initialize the object.
    var smsText = {};    

    context.done(null, smsText);
}
```

## <a name="advanced-binding-at-runtime-imperative-binding"></a>Расширенная привязка в среде выполнения (императивная привязка)

Стандартная входная и выходная привязка с использованием файла *function.json* называется [*декларативной*](https://en.wikipedia.org/wiki/Declarative_programming) привязкой. Такая привязка определяется объявлением JSON. Тем не менее, можно использовать [императивную](https://en.wikipedia.org/wiki/Imperative_programming) привязку. С использованием такого шаблона можно на лету выполнить привязку к поддерживаемым входным и выходным привязкам в любом количестве в коде функции.
Императивная привязка используется, если вычисление пути привязки или другие операции в функции должны осуществляться во время выполнения, а не разработки. 

Для выполнения императивной привязки сделайте следующее:

- **Не** добавляйте запись для нужных императивных привязок в файл *function.json*.
- Передайте входной параметр [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) или [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs). 
- Используйте следующий шаблон C# для привязки данных,

        using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
        {
                ...
        }

где `BindingTypeAttribute` — атрибут .NET, определяющий пользовательскую привязку, а `T` — входной или выходной тип, поддерживаемый этим типом привязки. `T` также не может быть параметром типа `out` (например, `out JObject`). Например, выходная привязка таблицы мобильных приложений поддерживает [шесть выходных типов](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), но для `T` можно использовать только [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) или [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs).
    
В следующем примере кода создается [выходная привязка большого двоичного объекта службы хранилища](functions-bindings-storage-blob.md#storage-blob-output-binding) с путем к большому двоичному объекту, определенному во время выполнения, а затем записывается строка в большой двоичный объект.

        using Microsoft.Azure.WebJobs;
        using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;
        
        public static async Task Run(string input, Binder binder)
        {
                using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
                {
                        writer.Write("Hello World!!");
                }
        }

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) определяет входную или выходную привязку [большого двоичного объекта службы хранилища](functions-bindings-storage-blob.md), а [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) представляет собой поддерживаемый тип выходной привязки.
Код возвращает параметр приложения по умолчанию для строки подключения к учетной записи службы хранилища (`AzureWebJobsStorage`). Вы можете указать пользовательский параметр приложения, который следует использовать, добавив [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) и передавая массив атрибутов в `BindAsync<T>()`. Например,

        using Microsoft.Azure.WebJobs;
        using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;
        
        public static async Task Run(string input, Binder binder)
        {
                var attributes = new Attribute[]
                {
                        new BlobAttribute("samples-output/path"),
                        new StorageAccountAttribute("MyStorageAccount")
                };
                using (var writer = await binder.BindAsync<TextWriter>(attributes))
                {
                        writer.Write("Hello World!");
                }
        }

В следующей таблице показан соответствующий атрибут .NET для каждого типа привязки, а также пакеты для ссылки.

| Привязка | Атрибут | Ссылка, которую нужно добавить |
|------|------|------|
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Концентраторы событий | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Мобильные приложения | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Центры уведомлений | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Служебная шина | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Очередь службы хранилища | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Большой двоичный объект службы хранилища | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Таблица службы хранилища | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions"` |



## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительных сведений см. следующие ресурсы:

* [Тестирование функции](functions-test-a-function.md)
* [Масштабирование функции](functions-scale.md)




<!--HONumber=Nov16_HO3-->


