---
title: Начало работы с разделами и подписками служебной шины Azure | Документация Майкрософт
description: Написание консольного приложения C# .NET Core, которое использует обмен сообщениями служебной шины для разделов и подписок.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/6/2017
ms.author: sethm
ms.openlocfilehash: aa75ac48d650f28d4aaeb612f2900d705cf71b5b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29396947"
---
# <a name="get-started-with-service-bus-topics"></a>Начало работы с разделами служебной шины

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

В этом руководстве рассматриваются следующие действия:

1. Создание пространства имен служебной шины с помощью портала Azure.
2. Создание раздела служебной шины с помощью портала Azure.
3. Создание подписки на этот раздел служебной шины с помощью портала Azure.
4. Написание консольного приложения .NET Core для отправки набора сообщений в раздел.
5. Написание консольного приложения .NET Core для получения этих сообщений из подписки.

## <a name="prerequisites"></a>предварительным требованиям

1. [Visual Studio 2017 с обновлением 3 (версия 15.3, 26730.01)](http://www.visualstudio.com/vs) или более новая версия.
2. [Пакет SDK для .NET Core](https://www.microsoft.com/net/download/windows) версии 2.0 или более новой.
2. Подписка Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Создание пространства имен с помощью портала Azure

> [!NOTE] 
> Вы также можете создать пространство имен служебной шины и сущности обмена сообщениями [с помощью PowerShell](/powershell/azure/get-started-azureps). Дополнительные сведения см. в статье [Управление ресурсами служебной шины с помощью модуля PowerShell](service-bus-manage-with-ps.md).

Если пространство имен для обмена сообщениями служебной шины уже создано, перейдите к разделу [Создание раздела с помощью портала Azure](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Создание раздела с помощью портала Azure

1. Войдите на [портал Azure][azure-portal].
2. В левой области навигации портала щелкните **Служебная шина**. Если элемент **Служебная шина** не отображается, щелкните **Все службы** или **All resources** (Все ресурсы). Щелкните пространство имен, в котором хотите создать раздел. 
3. Откроется окно обзора пространства имен. Щелкните **Разделы**.
   
    ![Создание раздела][createtopic1]
4. Нажмите кнопку **+ Раздел**.
   
    ![Выбор разделов][createtopic2]
5. Введите имя раздела. Для других параметров оставьте значения по умолчанию.
   
    ![Нажмите кнопку "Создать"][createtopic3]
6. Нажмите кнопку **Создать** в нижней части диалогового окна.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Создание подписки на раздел

1. В области ресурсов на портале выберите пространство имен, созданное на шаге 1, выберите **Разделы** и щелкните имя раздела, созданное на шаге 2.
2. В верхней части панели обзора щелкните **+ Подписка**, чтобы добавить подписку в этот раздел.

    ![Создание подписки][createtopic4]

3. Введите имя подписки. Для других параметров оставьте значения по умолчанию.

## <a name="4-send-messages-to-the-topic"></a>4. Отправка сообщений в раздел

Для отправки сообщений в раздел создайте консольное приложение C# с помощью Visual Studio.

### <a name="create-a-console-application"></a>Создание консольного приложение

Откройте Visual Studio и создайте проект **Консольное приложение (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Получение пакета NuGet для служебной шины

1. Щелкните созданный проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
2. Откройте вкладку **Обзор**, выполните поиск по фразе **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** и выберите элемент **Microsoft.Azure.ServiceBus**. Щелкните **Установить** , чтобы выполнить установку, а затем закройте это диалоговое окно.
   
    ![Установка пакета NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Написание кода для отправки сообщений в раздел

1. В файл Program.cs добавьте следующие операторы `using` в начале определения пространства имен перед объявлением класса.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. В классе `Program` объявите приведенные ниже переменные. Укажите для переменной `ServiceBusConnectionString` строку подключения, полученную при создании пространства имен, и задайте для `TopicName` имя раздела, которое использовалось при его создании.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Замените содержимое объекта `Main()` по умолчанию следующей строкой кода:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```
   
4. Сразу после `Main()` добавьте следующий асинхронный метод `MainAsync()`, который позволяет вызвать метод отправки сообщений:

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```

5. Сразу после метода `MainAsync()` добавьте следующий метод `SendMessagesAsync()`, который позволяет отправлять такое число сообщений, как задано для параметра `numberOfMessagesToSend` (текущее значение — 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Вот как будет выглядеть файл Program.cs отправителя:
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Запустите программу и перейдите на портал Azure. В окне **Обзор** пространства имен щелкните имя раздела. Отобразится экран раздела **Основное**. Обратите внимание, что теперь для подписки внизу окна параметр **Число сообщений** должен иметь значение **10**. Каждый раз при запуске приложения отправителя без получения сообщений (как указано в следующем разделе) это значение увеличивается на 10. А при каждом добавлении сообщения в раздел текущий размер раздела увеличивается, как и значение параметра **Текущий** в колонке **Основное**.
   
      ![Размер сообщения][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Получение сообщений из подписки

Чтобы получить только что отправленные сообщения, создайте другое консольное приложение .NET Core и установите пакет NuGet **Microsoft.Azure.ServiceBus**, как для предыдущего приложения отправителя.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Написание кода для получения сообщений из подписки

1. В файл Program.cs добавьте следующие операторы `using` в начале определения пространства имен перед объявлением класса.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. В классе `Program` объявите приведенные ниже переменные. Укажите для переменной `ServiceBusConnectionString` строку подключения, полученную при создании пространства имен, и задайте для `TopicName` имя раздела, которое использовалось при его создании, а для `SubscriptionName` задайте имя подписки на раздел, которое использовалось при ее создании.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Замените содержимое объекта `Main()` по умолчанию следующей строкой кода:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Сразу после `Main()` добавьте следующий асинхронный метод `MainAsync()`, который позволяет вызвать метод `RegisterOnMessageHandlerAndReceiveMessages()`.

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
    }
    ```

5. Сразу после метода `MainAsync()` добавьте следующий метод, который позволяет зарегистрировать обработчик сообщений и получать сообщения, отправленные приложением-отправителем:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Сразу после предыдущего метода добавьте следующий метод `ProcessMessagesAsync()` для обработки полученных сообщений:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Наконец, добавьте следующий метод для обработки исключений, которые могут возникнуть:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. Вот как будет выглядеть файл Program.cs получателя:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Запустите программу и перейдите на портал еще раз. Обратите внимание, что параметры **Число сообщений** и **Текущий** имеют значение **0**.
   
    ![Длина раздела][topic-message-receive]

Поздравляем! С помощью библиотеки .NET Standard вы только что создали раздел и подписку, отправили 10 сообщений и получили их.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с [примерами в репозитории GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples), демонстрирующими расширенные возможности обмена сообщениями служебной шины.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
