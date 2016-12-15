---
title: "Начало работы с очередями служебной шины | Документация Майкрософт"
description: "Как написать консольное приложение C# для обмена сообщениями служебной шины."
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/30/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 560e73eb7129e665d254c3e93b08f29cfb6b1aa9
ms.openlocfilehash: d80bedba1909de671ef96ac5372d9ff15a90764e


---
# <a name="get-started-with-service-bus-queues"></a>Начало работы с очередями служебной шины
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>Что будет выполнено
В этом руководстве описывается следующее:

1. Создание пространства имен служебной шины с помощью портала Azure.
2. Создание очереди сообщений служебной шины с помощью портала Azure.
3. Создание консольного приложения для отправки сообщения.
4. Создание консольного приложения для получения сообщения.

## <a name="prerequisites"></a>Предварительные требования
1. [Visual Studio 2013 или Visual Studio 2015](http://www.visualstudio.com). В описанных в этом руководстве примерах используется Visual Studio 2015.
2. Подписка Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Создание пространства имен с помощью портала Azure
Если пространство имен служебной шины уже создано, перейдите к разделу [Создание очереди с помощью портала Azure](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2) Создание очереди с помощью портала Azure
Если очередь служебной шины уже создана, перейдите к разделу [Отправка сообщений в очередь](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Отправка сообщений в очередь
Для отправки сообщений в очередь мы создадим консольное приложение C# с помощью Visual Studio.

### <a name="create-a-console-application"></a>Создание консольного приложение

- Откройте Visual Studio и создайте консольное приложение.

### <a name="add-the-service-bus-nuget-package"></a>Получение пакета NuGet для служебной шины
1. Щелкните созданный проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
2. Откройте вкладку **Обзор**, а затем выполните поиск по фразе "служебная шина Microsoft Azure" и выберите элемент **Служебная шина Microsoft Azure**. Щелкните **Установить** , чтобы выполнить установку, а затем закройте это диалоговое окно.
   
    ![Установка пакета NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Написание кода для отправки сообщения в очередь
1. Добавьте следующую инструкцию using в начало файла Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Добавьте в метод `Main` следующий код и укажите в качестве переменной **connectionString** строку подключения, полученную при создании пространства имен, а в качестве переменной **queueName** — имя очереди, использованное при создании очереди.
   
    ```csharp
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```
   
    Вот как будет выглядеть файл Program.cs.
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";
   
                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");
   
                client.Send(message);
            }
        }
    }
    ```
3. Запустите программу и проверьте портал Azure. Щелкните имя очереди в колонке **Обзор** пространства имен. Обратите внимание, что значение параметра **Active message count** (Число активных сообщений) должно равняться единице.
   
      ![Количество сообщений][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Получение сообщений из очереди
1. Создайте консольное приложение и добавьте ссылку на пакет NuGet служебной шины в соответствии с инструкциями для предыдущего приложения-отправителя.
2. Добавьте следующую инструкцию `using` в начало файла Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Добавьте в метод `Main` следующий код и укажите в качестве переменной **connectionString** строку подключения, полученную при создании пространства имен, а в качестве переменной **queueName** — имя очереди, использованное при создании очереди.
   
    ```csharp
    var connectionString = "";
    var queueName = "samplequeue";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.ReadLine();
    ```
   
    Вот как будет выглядеть файл Program.cs.
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
   
          Console.ReadLine();
        }
      }
    }
    ```
4. Запустите программу и проверьте портал. Обратите внимание, что параметр **Длина очереди** теперь должен иметь значение 0.
   
    ![Длина очереди][queue-message-receive]

Поздравляем! Вы создали очередь, а также отправили и получили сообщение.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с [примерами в репозитории GitHub](https://github.com/Azure-Samples/azure-servicebus-messaging-samples), демонстрирующими расширенные возможности обмена сообщениями служебной шины Azure.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Dec16_HO1-->


