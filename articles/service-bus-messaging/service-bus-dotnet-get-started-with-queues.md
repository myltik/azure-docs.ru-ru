<properties
    pageTitle="Начало работы с очередями служебной шины | Microsoft Azure"
    description="Как написать консольное приложение C# для обмена сообщениями служебной шины."
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/23/2016"
    ms.author="jotaub;sethm"/>

# Начало работы с очередями служебной шины

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## Что будет выполнено

В этом руководстве описывается следующее:

1. Создание пространства имен служебной шины с помощью портала Azure.

2. Создание очереди сообщений служебной шины с помощью портала Azure.

3. Создание консольного приложения для отправки сообщения.

4. Создание консольного приложения для получения сообщения.

## Предварительные требования

1. [Visual Studio 2013 или Visual Studio 2015](http://www.visualstudio.com). В описанных в этом руководстве примерах используется Visual Studio 2015.

2. Подписка Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1\. Создание пространства имен с помощью портала Azure

Если у вас уже есть пространство имен служебной шины, приступайте к [созданию очереди с помощью портала Azure](#2-create-a-queue-using-the-azure-portal).

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 2) Создание очереди с помощью портала Azure

Если у вас уже есть очередь служебной шины, приступайте к [отправке сообщений в очередь](#3-send-messages-to-the-queue).

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## 3\. Отправка сообщений в очередь

Для отправки сообщений в очередь мы создадим консольное приложение C# с помощью Visual Studio.

### Создание консольного приложение

1. Откройте Visual Studio и создайте консольное приложение.

### Получение пакета NuGet для служебной шины

1. Щелкните созданный проект правой кнопкой мыши и выберите пункт **Управление пакетами Nuget**.

2. Откройте вкладку **Обзор**, а затем выполните поиск по фразе "служебная шина Microsoft Azure" и выберите элемент **Служебная шина Microsoft Azure**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте это диалоговое окно.

    ![Установка пакета NuGet][nuget-pkg]

### Написание кода для отправки сообщения в очередь

1. Добавьте следующую инструкцию using в начало файла Program.cs.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Добавьте следующий код в метод `Main` и укажите в качестве переменной **connectionString** строку подключения, полученную при создании пространства имен, а в качестве переменной **queueName** — имя очереди, использованное при создании очереди.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```

    Вот как будет выглядеть файл Program.cs.

    ```
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
  
3. Запустите программу и проверьте портал Azure. Щелкните имя очереди в колонке **Обзор** пространства имен. Обратите внимание, что значение параметра **Количество активных сообщений** должно равняться единице.
    
      ![Количество сообщений][queue-message]
    
## 4\. Получение сообщений из очереди

1. Создайте консольное приложение и добавьте ссылку на пакет NuGet служебной шины в соответствии с инструкциями для предыдущего приложения-отправителя.

2. Добавьте следующую инструкцию `using` в начало файла Program.cs.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Добавьте следующий код в метод `Main` и укажите в качестве переменной **connectionString** строку подключения, полученную при создании пространства имен, а в качестве переменной **queueName** — имя очереди, использованное при создании очереди.

    ```
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

    ```
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
  
4. Запустите программу и проверьте портал. Обратите внимание, что **длина очереди** теперь должна иметь значение 0.

    ![Длина очереди][queue-message-receive]
  
Поздравляем! Вы создали очередь, а также отправили и получили сообщение.

## Дальнейшие действия

Ознакомьтесь с [примерами в нашем репозитории GitHub](https://github.com/Azure-Samples/azure-servicebus-messaging-samples), демонстрирующими расширенные возможности обмена сообщениями служебной шины Azure.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0928_2016-->