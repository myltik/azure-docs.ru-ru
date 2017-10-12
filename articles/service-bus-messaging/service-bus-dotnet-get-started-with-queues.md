---
title: "Начало работы с очередями служебной шины Azure | Документация Майкрософт"
description: "Написание консольного приложения C#, которое использует очереди обмена сообщениями служебной шины."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.openlocfilehash: 99a377db6341d90d263b98e14227db61dd9beabd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-service-bus-queues"></a>Начало работы с очередями служебной шины
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>Что будет выполнено
В этом руководстве рассматриваются следующие действия:

1. Создание пространства имен служебной шины с помощью портала Azure.
2. Создание очереди служебной шины с помощью портала Azure.
3. Создание консольного приложения для отправки сообщения.
4. Написание консольного приложения для получения сообщений, отправленных на предыдущем шаге.

## <a name="prerequisites"></a>Предварительные требования
1. [Visual Studio 2015 или более поздней версии.](http://www.visualstudio.com) В описанных в этом руководстве примерах используется Visual Studio 2017.
2. Подписка Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Создание пространства имен с помощью портала Azure
Если пространство имен для обмена сообщениями служебной шины уже создано, перейдите к разделу [Создание очереди с помощью портала Azure](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2) Создание очереди с помощью портала Azure
Если очередь служебной шины уже создана, перейдите к разделу [Отправка сообщений в очередь](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Отправка сообщений в очередь
Для отправки сообщений в очередь мы создадим консольное приложение C# с помощью Visual Studio.

### <a name="create-a-console-application"></a>Создание консольного приложение

Откройте Visual Studio и создайте проект **Консольное приложение (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Получение пакета NuGet для служебной шины
1. Щелкните созданный проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
2. Откройте вкладку **Обзор**, выполните поиск по фразе **служебная шина Microsoft Azure** и выберите элемент **WindowsAzure.ServiceBus**. Щелкните **Установить** , чтобы выполнить установку, а затем закройте это диалоговое окно.
   
    ![Установка пакета NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Написание кода для отправки сообщения в очередь
1. Добавьте следующую инструкцию `using` в начало файла Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Добавьте в метод `Main` следующий код. Укажите переменную `connectionString` для строки подключения, полученной при создании пространства имен, и переменную `queueName` для имени очереди, использованного при создании очереди.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Вот как будет выглядеть файл Program.cs.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Запустите программу и перейдите на портал Azure. В колонке **Обзор** пространства имен щелкните имя очереди. Отобразится колонка очереди **Основные компоненты**. Обратите внимание, что значение параметра **Количество активных сообщений** должно равняться единице. Каждый раз при запуске приложения отправителя без получения сообщений это значение увеличивается на 1. Также обратите внимание, что текущий размер очереди увеличивается каждый раз, когда приложение добавляет сообщение в очередь.
   
      ![Размер сообщения][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Получение сообщений из очереди

1. Чтобы получить только что отправленные сообщения, создайте консольное приложение и добавьте ссылку на пакет NuGet служебной шины в соответствии с инструкциями для предыдущего приложения отправителя.
2. Добавьте следующую инструкцию `using` в начало файла Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Добавьте в метод `Main` следующий код. Укажите переменную `connectionString` для строки подключения, полученной при создании пространства имен, и переменную `queueName` для имени очереди, использованного при создании очереди.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
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
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. Запустите программу и перейдите на портал еще раз. Обратите внимание, что значения параметра **Количество активных сообщений** и **Текущий** равны нулю.
   
    ![Длина очереди][queue-message-receive]

Поздравляем! Вы создали очередь, а также отправили и получили сообщение.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [примерами в репозитории GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples), демонстрирующими расширенные возможности обмена сообщениями служебной шины.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
