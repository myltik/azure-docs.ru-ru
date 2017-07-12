---
title: "Начало работы с разделами и подписками служебной шины Azure | Документация Майкрософт"
description: "Написание консольного приложения C#, которое использует обмен сообщениями служебной шины для разделов и подписок"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/30/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9401ada519f600b0d2817f06a396e16607a24129
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017


---
<a id="get-started-with-service-bus-topics" class="xliff"></a>

# Начало работы с разделами служебной шины

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

<a id="what-will-be-accomplished" class="xliff"></a>

## Что будет выполнено

В этом руководстве рассматриваются следующие действия:

1. Создание пространства имен служебной шины с помощью портала Azure.
2. Создание раздела служебной шины с помощью портала Azure.
3. Создание подписки на этот раздел служебной шины с помощью портала Azure.
4. Создание консольного приложения для отправки сообщения в раздел.
5. Создание консольного приложения для получения этого сообщения из подписки.

<a id="prerequisites" class="xliff"></a>

## Предварительные требования

1. [Visual Studio 2015 или более поздней версии.](http://www.visualstudio.com) В описанных в этом руководстве примерах используется Visual Studio 2017.
2. Подписка Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

<a id="1-create-a-namespace-using-the-azure-portal" class="xliff"></a>

## 1. Создание пространства имен с помощью портала Azure

Если пространство имен для обмена сообщениями служебной шины уже создано, перейдите к разделу [Создание раздела с помощью портала Azure](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

<a id="2-create-a-topic-using-the-azure-portal" class="xliff"></a>

## 2) Создание раздела с помощью портала Azure

1. Войдите на [портал Azure][azure-portal].
2. В левой области навигации портала щелкните **Служебная шина**. Если элемент **Служебная шина** не отображается, щелкните **Больше служб**.
3. Щелкните пространство имен, в котором хотите создать раздел. Появится колонка обзора пространства имен:
   
    ![Создание раздела][createtopic1]
4. В колонке **Пространство имен служебной шины** выберите **Разделы** и щелкните **Добавить раздел**.
   
    ![Выбор разделов][createtopic2]
5. Введите имя темы и снимите флажок для параметра **Включить секционирование**. Для других параметров оставьте значения по умолчанию.
   
    ![Нажмите кнопку "Создать"][createtopic3]
6. Щелкните кнопку **Создать**в нижней части колонки.

<a id="3-create-a-subscription-to-the-topic" class="xliff"></a>

## 3. Создание подписки на раздел

1. В области ресурсов на портале выберите пространство имен, созданное на этапе 1, и щелкните имя раздела, созданное на этапе 2.
2. На панели обзора вверху щелкните знак плюса рядом с надписью **Подписка**, чтобы добавить подписку на этот раздел.

    ![Создание подписки][createtopic4]

3. Введите имя подписки. Для других параметров оставьте значения по умолчанию.

<a id="4-send-messages-to-the-topic" class="xliff"></a>

## 4. Отправка сообщений в раздел

Для отправки сообщений в раздел мы создадим консольное приложение C# с помощью Visual Studio.

<a id="create-a-console-application" class="xliff"></a>

### Создание консольного приложение

Откройте Visual Studio и создайте проект **Консольное приложение (.NET Framework)**.

<a id="add-the-service-bus-nuget-package" class="xliff"></a>

### Получение пакета NuGet для служебной шины

1. Щелкните созданный проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
2. Откройте вкладку **Обзор**, выполните поиск по фразе **служебная шина Microsoft Azure** и выберите элемент **WindowsAzure.ServiceBus**. Щелкните **Установить** , чтобы выполнить установку, а затем закройте это диалоговое окно.
   
    ![Установка пакета NuGet][nuget-pkg]

<a id="write-some-code-to-send-a-message-to-the-topic" class="xliff"></a>

### Написание кода для отправки сообщения в раздел

1. Добавьте следующую инструкцию `using` в начало файла Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Добавьте в метод `Main` следующий код. Укажите для переменной `connectionString` строку подключения, полученную при создании пространства имен, и задайте для `topicName` имя раздела, которое использовалось при его создании.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
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

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Запустите программу и перейдите на портал Azure. В колонке **Обзор** щелкните имя раздела в пространстве имен. Отобразится колонка раздела **Основные**. Обратите внимание, что для каждой подписки внизу колонки параметр **Количество сообщений** должен иметь значение 1. Каждый раз при запуске приложения отправителя без получения сообщений (как указано в следующем разделе) это значение увеличивается на 1. А при каждом добавлении сообщения в раздел или подписку текущий размер раздела увеличивается, как и значение параметра **Текущий** в колонке **Основные**.
   
      ![Размер сообщения][topic-message]

<a id="5-receive-messages-from-the-subscription" class="xliff"></a>

## 5. Получение сообщений из подписки

1. Чтобы получить только что отправленные сообщения, создайте консольное приложение и добавьте ссылку на пакет NuGet служебной шины в соответствии с инструкциями для предыдущего приложения отправителя.
2. Добавьте следующую инструкцию `using` в начало файла Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Добавьте в метод `Main` следующий код. Укажите для переменной `connectionString` строку подключения, полученную при создании пространства имен, и задайте для `topicName` имя раздела, которое использовалось при его создании.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
4. Запустите программу и перейдите на портал еще раз. Обратите внимание, что параметры **Количество сообщений** и **Текущий** имеют значение 0.
   
    ![Длина раздела][topic-message-receive]

Поздравляем! Вы создали раздел и подписку, а также отправили и получили сообщение.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

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

