---
title: "Отправка событий в концентраторы событий Azure с помощью .NET Standard | Документация Майкрософт"
description: "Узнайте, как начать работу с отправкой событий в концентраторы событий на платформе .NET Standard."
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
ms.date: 02/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 57175ddc53d5856cd3492d4c631a92d4bf9247c4
ms.openlocfilehash: a6c5ff034450c9c6a01feb4ae6d84cebd75a5682
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>Начало работы с отправкой событий в концентраторы событий на платформе .NET Standard

> [!NOTE]
> Этот пример можно найти на сайте [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender).

## <a name="what-will-be-accomplished"></a>Наши задачи:

В этом руководстве показано, как создать существующее решение **SampleSender** (в этой папке). Запустите это решение "как есть", заменив строки `EhConnectionString`, `EhEntityPath` и `StorageAccount` значениями, которые соответствуют вашему концентратору событий. Или следуйте инструкциям этого руководства, чтобы создать собственное решение.

В этом руководстве мы напишем консольное приложение .NET Core для отправки сообщений в концентратор событий.

## <a name="prerequisites"></a>Предварительные требования

1. [Visual Studio 2015](http://www.visualstudio.com).

2. [Инструментарий Visual Studio 2015 для .NET Core](http://www.microsoft.com/net/core).

3. Подписка Azure.

4. Пространство имен концентраторов событий.

## <a name="send-messages-to-an-event-hub"></a>Отправка сообщений в концентратор событий

Для отправки сообщений в концентратор событий мы напишем консольное приложение C# с помощью Visual Studio.

### <a name="create-a-console-application"></a>Создание консольного приложение

* Запустите Visual Studio и создайте консольное приложение .NET Core.

### <a name="add-the-event-hubs-nuget-package"></a>Добавление пакета NuGet для концентраторов событий

* Добавьте в проект пакет NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/).

### <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Написание кода для отправки сообщений в концентратор событий

1. Добавьте следующую инструкцию `using` в начало файла Program.cs.

    ```cs
    using Microsoft.Azure.EventHubs;
    ```

2. Добавьте в класс `Program` константы для строки подключения концентраторов событий и пути сущности (имя отдельного концентратора событий). Замените заполнители в скобках соответствующими значениями, которые были получены при создании концентратора событий.

    ```cs
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Добавьте новый метод с именем `MainAsync` в класс `Program`:

    ```cs
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
        // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();
    }
    ```
    
4. Добавьте новый метод с именем `SendMessagesToEventHub` в класс `Program`:

    ```cs
    // Creates an Event Hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. В метод `Main` в классе `Program` добавьте следующий код.

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Вот как будет выглядеть файл Program.cs.

    ```cs
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
    
        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
                // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };
    
                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
    
                await SendMessagesToEventHub(100);
    
                await eventHubClient.CloseAsync();
    
                Console.WriteLine("Press any key to exit.");
                Console.ReadLine();
            }
    
            // Creates an Event Hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }
    
                    await Task.Delay(10);
                }
    
                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```
  
6. Запустите программу и убедитесь в отсутствии ошибок.
  
Поздравляем! Теперь вы можете отправлять сообщения в концентратор событий.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Основные сведения о получении сообщений с помощью узла EventProcessorHost в .NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Обзор концентраторов событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)
