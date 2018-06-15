---
title: Получение событий от концентраторов событий Azure с помощью библиотеки .NET Standard | Документация Майкрософт
description: Основные сведения о получении сообщений с помощью узла EventProcessorHost в .NET Standard
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: 5eb5c2d1f0b85c907f788fb6ac752488601f613a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29389841"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Основные сведения о получении сообщений с помощью узла EventProcessorHost в .NET Standard

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver).

В этом руководстве показано, как создать консольное приложение .NET Core для получения сообщений из концентратора событий с помощью библиотеки **узла обработчика событий**. Вы можете запустить решение [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) "как есть", заменив соответствующие строки своими значениями для концентратора событий и учетной записи хранения. Или следуйте инструкциям этого руководства, чтобы создать собственное решение.

## <a name="prerequisites"></a>предварительным требованиям

* [Microsoft Visual Studio 2015 или Microsoft Visual Studio 2017](http://www.visualstudio.com). В примерах в этом руководстве используется Visual Studio 2017, но также поддерживается Visual Studio 2015.
* [Инструментарий Visual Studio 2015 или Visual Studio 2017 для .NET Core](http://www.microsoft.com/net/core).
* Подписка Azure.
* Пространство имен концентраторов событий Azure.
* Учетная запись хранения Azure.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Создание пространства имен концентраторов событий и концентратора событий  

Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен концентраторов событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните процедуру, описанную в [этой статье](event-hubs-create.md), а затем перейдите к этому руководству.  

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure  

1. Войдите на [портале Azure](https://portal.azure.com).  
2. В области навигации слева на странице портала последовательно выберите **Создать ресурс**, **Хранилище** и **Учетная запись хранения**.  
3. Заполните поля в окне учетной записи хранения и нажмите кнопку **Создать**.

    ![Создать учетную запись хранения][1]

4. Получив сообщение об **успешном выполнении развертывания**, щелкните имя новой учетной записи хранения. В окне **Основные компоненты** щелкните **BLOB-объекты**. В верхней части открывшегося диалогового окна **Служба BLOB-объектов** щелкните **+ Container** (+ Контейнер). Присвойте контейнеру имя, а затем закройте окно **Служба BLOB-объектов**.  
5. Щелкните элемент **Ключи доступа** в окне слева и скопируйте имя контейнера хранения, а также значение **key1**. Сохраните на время эти значения в Блокноте или любом другом месте.  

## <a name="create-a-console-application"></a>Создание консольного приложение

Запустите Visual Studio. В меню **Файл** выберите команду **Создать**, а затем — **Проект**. Создайте консольное приложение .NET Core.

![Новый проект][2]

## <a name="add-the-event-hubs-nuget-package"></a>Добавление пакета NuGet для концентраторов событий

Чтобы добавить в проект пакеты NuGet стандартной библиотеки .NET [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) и [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/), выполните следующие действия: 

1. Щелкните созданный проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
2. Откройте вкладку **Обзор**, а затем выполните поиск по фразе **Microsoft.Azure.EventHubs** и выберите пакет **Microsoft.Azure.EventHubs**. Щелкните **Установить** , чтобы выполнить установку, а затем закройте это диалоговое окно.
3. Повторите шаги 1 и 2 и установите пакет **Microsoft.Azure.EventHubs.Processor**.

## <a name="implement-the-ieventprocessor-interface"></a>Реализация интерфейса IEventProcessor

1. В обозревателе решений щелкните правой кнопкой мыши проект, выберите **Добавить**, а затем щелкните **Класс**. Назовите новый класс **SimpleEventProcessor**.

2. Откройте файл SimpleEventProcessor.cs и добавьте в его начало следующие операторы `using`.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
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
    using System.Threading.Tasks;
    ```

2. Добавьте в класс `Program` константы для строки подключения концентраторов событий, имени концентратора событий, имени контейнера учетной записи хранения, имени учетной записи хранения и ключа учетной записи хранения. Добавьте следующий код, заменив заполнители соответствующими значениями.

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

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Event Hubs overview](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
