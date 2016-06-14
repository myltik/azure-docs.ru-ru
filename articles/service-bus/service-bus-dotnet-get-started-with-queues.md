<properties
   pageTitle="Начало работы с очередями служебной шины | Microsoft Azure"
   description="Как написать консольное приложение C# для обмена сообщениями служебной шины."
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Начало работы с очередями служебной шины
[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

##Что будет выполнено
В этом руководстве описывается следующее:

1. Создание пространства имен служебной шины с помощью портала Azure.

2. Создание очереди сообщений служебной шины с помощью портала Azure.

3. Создание консольного приложения для отправки сообщения.

4. Создание консольного приложения для получения сообщения.

##Предварительные требования
1. [Visual Studio 2013 или 2015](http://www.visualstudio.com).

2. Подписка Azure

##1\. Создание пространства имен с помощью портала Azure
Если у вас уже есть созданное пространство имен служебной шины, приступайте к [созданию очереди с помощью портала Azure](#2-create-a-queue-using-the-azure-portal).

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

##2\. Создание очереди с помощью портала Azure
Если у вас уже есть созданная очередь служебной шины, приступайте к [отправке сообщений в очередь](#3-sending-messages-to-the-queue).

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

##3\. Отправка сообщений в очередь
Для отправки сообщений в очередь мы создадим консольное приложение C# с помощью Visual Studio.

###Создание консольного приложение
1. Откройте Visual Studio и создайте консольное приложение.

###Получение пакета NuGet для служебной шины
1. Щелкните созданный проект правой кнопкой мыши и выберите пункт **Управление пакетами Nuget**.

2. Выполните поиск по фразе "служебная шина Microsoft Azure" и выберите элемент **Служебная шина Microsoft Azure**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте это диалоговое окно.

    ![Установка пакета NuGet][1]

###Написание кода для отправки сообщения в очередь
1. Добавьте следующую инструкцию using в начало файла Program.cs.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Добавьте следующий код в метод Main и укажите в качестве переменной **connectionString** строку подключения, полученную при создании пространства имен, а в качестве переменной **queueName** — имя очереди, использованное при создании очереди.

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
  
3. Запустите программу и проверьте портал Azure. Обратите внимание, что **длина очереди** теперь должна иметь значение 1.
    
      ![Длина очереди][2]
    
##4\. Получение сообщений из очереди
1. Создайте консольное приложение и добавьте ссылку на пакет NuGet служебной шины в соответствии с инструкциями для передающего приложения (см. выше).

2. Добавьте следующую инструкцию using в начало файла Program.cs.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Добавьте следующий код в метод Main и укажите в качестве переменной **connectionString** строку подключения, полученную при создании пространства имен, а в качестве переменной **queueName** — имя очереди, использованное при создании очереди.

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
  
4. Запустите программу и проверьте портал Azure. Обратите внимание, что **длина очереди** теперь должна иметь значение 0.

    ![Длина очереди][3]
  
Поздравляем! Вы создали очередь, а также отправили и получили сообщение.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##Дальнейшие действия

В нашем репозитории GitHub доступны образцы, демонстрирующие расширенные возможности обмена сообщениями служебной шины Azure: [https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples].

<!--Image references-->

[1]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[2]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-send.png
[3]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->