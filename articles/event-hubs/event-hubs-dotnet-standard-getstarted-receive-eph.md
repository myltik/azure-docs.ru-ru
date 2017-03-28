---
title: "Получение событий от концентраторов событий Azure с помощью .NET Standard | Документация Майкрософт"
description: "Основные сведения о получении сообщений с помощью узла EventProcessorHost в .NET Standard"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 7506430f4ec5522165274f05a2fd5b77e3d6252d
ms.lasthandoff: 03/06/2017

---

# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Основные сведения о получении сообщений с помощью узла EventProcessorHost в .NET Standard

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver).

В этом руководстве показано, как создать консольное приложение .NET Core для получения сообщений из концентратора событий с помощью узла **EventProcessorHost**. Запустите решение [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) "как есть", заменив строки значениями, которые соответствуют вашему концентратору событий и вашей учетной записи хранения. Или следуйте инструкциям этого руководства, чтобы создать собственное решение. 

## <a name="prerequisites"></a>Предварительные требования

1. [Microsoft Visual Studio 2015 или Microsoft Visual Studio 2017](http://www.visualstudio.com). В примерах в этом руководстве используется Visual Studio 2015, но также поддерживается Visual Studio 2017.
2. [Инструментарий Visual Studio 2015 или Visual Studio 2017 для .NET Core](http://www.microsoft.com/net/core).
3. Подписка Azure.
4. Пространство имен концентраторов событий.
5. Учетная запись хранения Azure.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Создание пространства имен концентраторов событий и концентратора событий  
  
Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен типа концентраторов событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните процедуру, описанную в [этой статье](event-hubs-create.md), а затем перейдите к следующим действиям.  

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure  

1. Войдите на [портал Azure](https://portal.azure.com).  
2. В области навигации слева на странице портала щелкните **Создать** > **Хранилище** > **Учетная запись хранения**.  
3. Заполните поля в колонке учетной записи хранения и нажмите кнопку **Создать**.
  
    ![][1]

4. Получив сообщение об **успешном выполнении развертывания**, щелкните имя новой учетной записи хранения, затем в колонке **Основные компоненты** щелкните **BLOB-объекты**. В верхней части открывшейся колонки **Служба BLOB-объектов** щелкните **+ Container** (+ Контейнер). Присвойте контейнеру имя, а затем закройте колонку **Служба BLOB-объектов**.  
5. Щелкните элемент **Ключи доступа** в колонке слева и скопируйте имя контейнера хранения, а также значение **key1**. Сохраните на время эти значения в Блокноте или любом другом месте.  
    
## <a name="create-a-console-application"></a>Создание консольного приложение

1. Запустите Visual Studio. В меню "Файл" выберите команду **Создать**, а затем — **Проект**. Создайте консольное приложение .NET Core.

    ![][2]

2. В окне обозревателя решений дважды щелкните файл **project.json**, чтобы открыть его в редакторе Visual Studio.
3. Добавьте строку `"portable-net45+win8"` в объявление `"imports"` в разделе `"frameworks`. Этот раздел теперь должен выглядеть следующим образом. Эта строка необходима из-за зависимости хранилища Azure от OData:

    ```json
    "frameworks": {
      "netcoreapp1.0": {
        "imports": [
          "dnxcore50",
          "portable-net45+win8"
        ]
      }
    }
    ```

4. В меню "Файл"выберите команду **Сохранить все**.

Обратите внимание, что в этом учебнике показано, как создать приложение .NET Core, но если вы хотите охватить всю платформу .NET Framework, добавьте следующую строку кода в файл project.json в разделе `"frameworks"`:

```json
"net451": {
},
``` 

## <a name="add-the-event-hubs-nuget-package"></a>Добавление пакета NuGet для концентраторов событий

* Добавьте в проект следующие пакеты NuGet:
  * [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
  * [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

## <a name="implement-the-ieventprocessor-interface"></a>Реализация интерфейса IEventProcessor

1. В обозревателе решений щелкните правой кнопкой мыши проект, выберите **Добавить**, а затем щелкните **Класс**. Назовите новый класс **SimpleEventProcessor**.

2. Откройте файл SimpleEventProcessor.cs и добавьте в его начало следующие операторы `using`.

    ```csharp
    using System.Text;
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

3. Реализуйте интерфейс `IEventProcessor`. Замените все содержимое класса `SimpleEventProcessor` следующим кодом:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }
    
        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }
    
        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }
    
        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }
    
            return context.CheckpointAsync();
        }
    }
    ```

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Написание метода главной консоли, использующего класс SimpleEventProcessor для получения сообщений

1. Добавьте следующие операторы `using` в начало файла program.cs.
  
    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. Добавьте в класс `Program` константы для строки подключения концентраторов событий, имени концентратора событий, имени контейнера хранилища, имени учетной записи хранения и ключа учетной записи хранения. Добавьте следующий код, заменив заполнители соответствующими значениями.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Добавьте новый метод с именем `MainAsync` в класс `Program`, как показано далее:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Добавьте в метод `Main` следующую строку кода:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Вот как будет выглядеть файл Program.cs.

    ```csharp
    namespace SampleEphReceiver
    {
    
        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";
    
            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");
    
                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);
    
                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
    
                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();
    
                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```
  
4. Запустите программу и убедитесь в отсутствии ошибок.
  
Поздравляем! Теперь вы можете получать сообщения из концентратора событий с помощью узла EventProcessorHost.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Обзор концентраторов событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
