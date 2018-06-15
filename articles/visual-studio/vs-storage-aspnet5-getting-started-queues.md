---
title: Приступая к работе с хранилищем очередей и подключенными службами Visual Studio (ASP.NET Core) | Документация Майкрософт
description: Начало работы с хранилищем очередей Azure в проекте ASP.NET Core в Visual Studio.
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e56f79334aa85d9a0c81bed4f00664fee5789676
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793993"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Приступая к работе с хранилищем очередей и подключенными службами Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

В этой статье описывается, как приступить к использованию хранилища очередей Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее в проекте ASP.NET Core с помощью функции **подключенных служб** в Visual Studio. Операция **подключенных служб** устанавливает соответствующие пакеты NuGet для доступа к службе хранилища Azure в вашем проекте и добавляет строку подключения для учетной записи хранения в файлы конфигурации проекта. Общие сведения о службе хранилища Azure см. в [документации по службе хранилища](https://azure.microsoft.com/documentation/services/storage/).

Хранилище очередей Azure — это служба хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколу HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений, ограничиваемых общей емкостью учетной записи хранения. Дополнительные сведения о выполнении программных операций с очередями см. в статье [Приступая к работе с хранилищем очередей Azure с помощью .NET](../storage/queues/storage-dotnet-how-to-use-queues.md).

Чтобы приступить к работе, сначала необходимо создать очередь Azure в учетной записи хранения. Затем в этой статье объясняется, как создать очередь с помощью языка C# и как выполнять базовые операции с очередями, например добавлять, изменять, считывать и удалять сообщения в очередях.  В коде используется клиентская библиотека службы хранилища Microsoft Azure для .NET. Дополнительные сведения см. на сайте [ASP.NET](http://www.asp.net).

Некоторые интерфейсы API службы хранилища Azure являются асинхронными, и в коде, приведенном в этой статье, предполагается, что используются асинхронные методы. Дополнительные сведения см. в статье [Асинхронное программирование](https://docs.microsoft.com/dotnet/csharp/async).

## <a name="access-queues-in-code"></a>Доступ к очередям в коде

Для доступа к очередям в проектах ASP.NET Core необходимо добавить следующие элементы во все файлы исходного кода C#, которые обращаются к хранилищу очередей Azure. Вставьте весь этот код перед кодом из следующих разделов.

1. Добавьте необходимые инструкции `using`.
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Получите объект `CloudStorageAccount`, представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Используйте объект `CloudQueueClient` для ссылки на объекты очереди в учетной записи хранения.

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Получите объект `CloudQueue` для ссылки на определенную очередь.

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Создание очереди в коде

Чтобы создать очередь Azure в коде, добавьте вызов CreateIfNotExistsAsync.

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Добавление сообщения в очередь

Чтобы вставить сообщение в существующую очередь, создайте объект `CloudQueueMessage`, а затем вызовите метод `AddMessageAsync`. Для создания объекта `CloudQueueMessage` можно использовать либо строку (в формате UTF-8), либо массив байтов.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Чтение сообщения в очереди

Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод `PeekMessageAsync`.

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Чтение и удаление сообщения в очереди

Ваш код может удалить сообщение из очереди в два этапа.

1. Вызовите метод `GetMessageAsync`, чтобы получить следующее сообщение в очереди. Сообщение, возвращаемое методом `GetMessageAsync`, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд.
1. Чтобы завершить удаление сообщения из очереди, вызовите метод `DeleteMessageAsync`.

Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Указанный ниже код вызывает метод `DeleteMessageAsync` сразу после обработки сообщения.

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Дополнительные варианты удаления сообщений из очереди

Извлечение сообщений из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод `GetMessages` используется для получения 20 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла `foreach`. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии 5 минут все сообщения, которые не были удалены, снова становятся видимыми.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод `FetchAttributes` отправляет в службу очередей запрос на извлечение атрибутов очереди, включая количество сообщений. Свойство `ApproximateMethodCount` возвращает последнее значение, полученное с использованием метода `FetchAttributes`, без вызова службы очередей.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Использование схемы Async-Await со стандартными API очереди

В этом примере показано использование схемы Async-Await со стандартными API очередей, заканчивающимися на `Async`. При использовании асинхронного метода схема Async-Await приостанавливает локальное выполнение процесса до завершения вызова. Благодаря этому текущий поток может выполнять другие задачи, что позволяет избежать возникновения узких мест и повысить общую скорость реагирования приложения.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Удаление очереди

Чтобы удалить очередь и все сообщения в ней, вызовите метод `Delete` для объекта очереди.

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
