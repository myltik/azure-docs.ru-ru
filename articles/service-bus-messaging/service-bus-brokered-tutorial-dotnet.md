---
title: "Руководство для .NET по обмену сообщениями через брокер с помощью служебной шины | Документация Майкрософт"
description: "Руководство по обмену сообщениями .NET через посредника"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 964e019a-8abe-42f3-8314-867010cb2608
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 9ace119de3676bcda45d524961ebea27ab093415
ms.openlocfilehash: d888a16d538491535aad8effed53a5e98aa01359


---
# <a name="service-bus-brokered-messaging-net-tutorial"></a>Учебное пособие по обмену сообщениями .NET через посредника в служебной шине
Служебная шина Azure предоставляет два комплексных решения для обмена сообщениями. Первое решение основано на централизованной службе "ретрансляции", которая запущена в облаке. Это решение поддерживает множество различных транспортных протоколов и стандартов веб-служб, включая SOAP, WS-* и REST. Клиенту не требуются прямое подключение к локальной службе и информация о ее местонахождении, а локальной службе не требуется открывать на брандмауэре входящие порты.

Второе решение для обмена сообщениями обеспечивает возможности обмена сообщениями через брокер. Такой обмен сообщениями можно считать асинхронным, или разделенным. Он поддерживает публикацию и подписку, временное разделение, а также сценарии балансировки нагрузки с использованием инфраструктуры обмена сообщениями через служебную шину. Разделенный обмен данными имеет множество преимуществ. Например, клиенты и серверы могут подключаться по необходимости, выполняя свои операции в асинхронном режиме.

Этот учебник предоставит вам общие сведения и практический опыт работы с очередями, одними из основных компонентов обмена сообщениями через служебную шину через посредника. После работы с разделами в этом учебнике у вас получится приложение, которое заполняет список сообщений, создает очередь и отправляет сообщения в эту очередь. Наконец, приложение получает и отображает сообщения из очереди, а затем освобождает свои ресурсы и завершает работу. Дополнительные сведения о создании приложения, использующего ретранслятор WCF служебной шины, см. в статье [Руководство по использованию ретранслятора служебной шины](../service-bus-relay/service-bus-relay-tutorial.md).

## <a name="introduction-and-prerequisites"></a>Введение и предварительные требования
Очереди предлагают доставку сообщений конкурирующим потребителям FIFO. Доставка сообщений FIFO означает, что получатели принимают и обрабатывают сообщения в порядке их добавления в очередь, и каждое сообщение принимается и обрабатывается только одним потребителем сообщений. Основное преимущество использования очередей заключается в достижении *временного разделения* компонентов приложения: иными словами, создатели и потребители не должны отправлять и получать сообщения в одно и то же время, так как сообщения хранятся в очереди на протяжении долгого времени. Сопутствующее преимущество заключается в *выравнивании нагрузки*. Эта возможность позволяет производителям и потребителям отправлять и получать сообщения с разной скоростью.

Ниже приведены некоторые административные и подготовительные действия, которые необходимо выполнить перед началом работы с данным руководством. Сначала необходимо создать пространство имен службы и получить ключ подписи общего доступа (SAS). Пространство имен определяет границы каждого приложения, предоставляемого через служебную шину. Ключ SAS автоматически создается системой при создании пространства имен службы. Пространство имен и ключ SAS совместно образуют учетные данные, на основе которых служебная шина осуществляет проверку подлинности и разрешает доступ к приложению.

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>Создание пространства имен службы и получение ключа SAS
Сначала необходимо создать пространство имен службы и получить ключ [подписанного URL-адреса](service-bus-sas-overview.md) (SAS). Пространство имен определяет границы каждого приложения, предоставляемого через служебную шину. Ключ SAS автоматически создается системой при создании пространства имен службы. Сочетание пространства имен и ключа совместного доступа к подписи предоставляет учетные данные, на основе которых служба Service Bus осуществляет проверку подлинности и дает доступ к приложению.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Затем необходимо создать проект Visual Studio и написать две вспомогательные функции, которые загружают разделенный запятыми список сообщений в строго типизированный объект [List](https://msdn.microsoft.com/library/6sh2ey19.aspx) .NET [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx).

### <a name="create-a-visual-studio-project"></a>Создание проекта Visual Studio
1. Откройте Visual Studio с правами администратора, щелкнув программу правой кнопкой мыши в меню "Пуск" и выбрав **Запустить от имени администратора**.
2. Создайте новый проект консольного приложения. В меню **Файл** выберите **Создать**, а затем — **Проект**. В диалоговом окне **Новый проект** выберите **Visual C#** (если **Visual C#** не отображается, перейдите в раздел **Другие языки**). Затем выберите шаблон **Консольное приложение** и назовите его **QueueSample**. Используйте **расположение** по умолчанию. Нажмите кнопку **ОК** , чтобы создать проект.
3. Воспользуйтесь диспетчером пакетов NuGet для добавления библиотек служебной шины в проект.

   1. В обозревателе решений щелкните правой кнопкой мыши проект **QueueSample** и выберите **Управление пакетами NuGet**.
   2. В диалоговом окне **Управление пакетами NuGet** откройте вкладку **Обзор** и найдите **служебную шину Azure**, а затем щелкните **Установить**.
      <br />
4. В окне обозревателя решений дважды щелкните файл Program.cs, чтобы открыть его в редакторе Visual Studio. Измените имя пространства имен с `QueueSample` (имя по умолчанию) на `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```
5. Измените инструкции `using`, как показано в следующем коде.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```
6. Создайте текстовый файл с именем Data.csv и скопируйте в него следующий текст, разделенный запятыми.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Сохраните и закройте файл Data.csv и запомните расположение, в котором его сохранили.
7. В обозревателе решений щелкните правой кнопкой мыши имя проекта (в этом примере **QueueSample**), выберите **Добавить**, а затем щелкните **Существующий элемент**.
8. Найдите файл Data.csv, созданный на шаге 6. Щелкните файл и выберите **Добавить**. В списке типов файлов должно быть выбрано **Все файлы (*.*)**.

### <a name="create-a-method-that-parses-a-list-of-messages"></a>Создание метода для анализа списка сообщений
1. В классе `Program` перед методом `Main()` объявите две переменные. Первая должна иметь тип **DataTable** и содержать список сообщений в файле Data.csv. Вторая должна иметь тип объекта List, строго типизированный к [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Эта переменная представляет собой список сообщений, доставленных через посредника, которые будут использоваться далее в этом руководстве.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {

            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```
2. За пределами `Main()` определите метод `ParseCSV()`, который анализирует список сообщений в файле Data.csv и загружает сообщения в таблицу [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx), как показано ниже. Этот метод возвращает объект **DataTable**.

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;

                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }

                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }

        return tableIssues;
    }
    ```
3. В метод `Main()` добавьте вызов метода `ParseCSVFile()`.

    ```
    public static void Main(string[] args)
    {

        // Populate test data
        issues = ParseCSVFile();

    }
    ```

### <a name="create-a-method-that-loads-the-list-of-messages"></a>Создание метода, который загружает список сообщений
1. За пределами `Main()` определите `GenerateMessages()` метод, который принимает объект **DataTable**, возвращаемый `ParseCSVFile()`, и загружает таблицу в строго типизированный список сообщений в брокере. Затем метод возвращает объект **List**, как показано в следующем примере.

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();

        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```
2. В `Main()` сразу после вызова `ParseCSVFile()` добавьте инструкцию для вызова метода `GenerateMessages()` со значением, возвращаемым `ParseCSVFile()`, в качестве аргумента.

    ```
    public static void Main(string[] args)
    {

        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>Получение учетных данных пользователя
1. Сначала создайте три глобальных строковых переменных для хранения этих значений. Объявите эти переменные сразу после предыдущего объявления переменных, например:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {

            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```
2. Затем создайте функцию, которая принимает и сохраняет пространство имен службы и ключ SAS. Добавьте этот метод вне `Main()`. Например:

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();

        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```
3. В `Main()` сразу после вызова `GenerateMessages()` добавьте инструкцию для вызова метода `CollectUserInput()`.

    ```
    public static void Main(string[] args)
    {

        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);

        // Collect user input
        CollectUserInput();
    }
    ```

### <a name="build-the-solution"></a>Выполните сборку решения.
В меню **Сборка** в Visual Studio выберите пункт **Собрать решение** или нажмите клавиши **CTRL+SHIFT+B**, чтобы подтвердить правильность выполнения действий.

## <a name="create-management-credentials"></a>Создание учетных данных управления
На этом шаге определяются операции управления, которые будут использованы для создания учетных данных подписи общего доступа (SAS), с которыми будет выполняться проверка подлинности приложения.

1. Для ясности в этом руководстве все операции по работе с очередью помещены в отдельный метод. Создайте асинхронный метод `Queue()` в классе `Program` после метода `Main()`. Например:

    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```
2. Затем необходимо создать учетные данные SAS с помощью объекта [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx). Метод создания принимает имя ключа SAS и значение, полученное в методе `CollectUserInput()`. Добавьте в метод `Queue()` следующий код:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```
3. Создайте новый объект управления пространством имен с URI, содержащим имя пространства имен и учетные данные управления, полученные на предыдущем шаге, в качестве аргументов. Добавьте этот код сразу после кода, добавленного на предыдущем этапе. Не забудьте заменить `<yourNamespace>` именем пространства имен своей службы:

    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>Пример
В этот момент ваш код должен выглядеть примерно так:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## <a name="send-messages-to-the-queue"></a>Отправка сообщений в очередь
На этом шаге вы создаете очередь и затем отправляете в нее сообщения, содержащиеся в списке сообщений, доставленных через посредника.

### <a name="create-queue-and-send-messages-to-the-queue"></a>Создание очереди и отправка сообщений в очередь
1. Сначала создайте очередь. Например, назовите ее `myQueue` и объявите ее сразу после операций управления, добавленных на предыдущем этапе в метод `Queue()`:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```
2. В методе `Queue()` создайте объект фабрики обмена сообщениями, указав только что созданный URI служебной шины в качестве аргумента. Добавьте следующий код сразу после операций управления, добавленных на предыдущем этапе. Не забудьте заменить `<yourNamespace>` именем пространства имен своей службы:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```
3. После этого создайте объект очереди с помощью класса [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Добавьте следующий код сразу после кода, добавленного на предыдущем шаге:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```
4. Затем добавьте код, который просматривает список сообщений, доставленных через посредника, который вы создали ранее, и отправляет каждое сообщение в очередь. Добавьте следующий код сразу после инструкции `CreateQueueClient()` на предыдущем шаге:

    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>Получение сообщений из очереди
На этом шаге вы получаете список сообщений из очереди, созданной на предыдущем шаге.

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>Создание получателя и получение сообщений из очереди
В методе `Queue()` пройдитесь по очереди и получите сообщения с помощью метода [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx), выводя каждое сообщение на консоль. Вставьте следующий код сразу после метода кода, добавленного на предыдущем шаге.

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Обратите внимание, что `Thread.Sleep` используется только для имитации обработки сообщения. Скорее всего, в настоящем приложении для обмена сообщениями этот метод вам не потребуется.

### <a name="end-the-queue-method-and-clean-up-resources"></a>Завершение метода Queue и очистка ресурсов
Сразу после предыдущего кода добавьте следующий код для освобождения ресурсов фабрики и очереди сообщений.

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>Вызов метода Queue
Последний этап — добавить инструкцию, которая вызывает метод `Queue()` из `Main()`. Добавьте следующую выделенную строку кода в конце метода Main():

```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();

    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);

    // Add this call
    Queue();
}
```

### <a name="example"></a>Пример
Следующий код содержит полный код приложения **QueueSample**.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }

            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");

            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>Сборка и запуск приложения QueueSample
Теперь после завершения предыдущих шагов можно собрать и запустить приложение **QueueSample**.

### <a name="build-the-queuesample-application"></a>Сборка приложения QueueSample
В Visual Studio в меню **Сборка** выберите пункт **Собрать решение** или нажмите клавиши **CTRL+SHIFT+B**. При возникновении ошибок проверьте правильность кода по полному примеру, приведенному в конце предыдущего шага.

## <a name="next-steps"></a>Дальнейшие действия
В этом учебнике было показано создание приложения клиента и службы служебной шины с использованием возможностей обмена сообщениями через посредника служебной шины. Похожее руководство, в котором используется [ретранслятор WCF](service-bus-messaging-overview.md#service-bus-relay) служебной шины, см. в статье [Руководство по использованию ретранслятора служебной шины](../service-bus-relay/service-bus-relay-tutorial.md).

Дополнительные сведения о [служебной шине](https://azure.microsoft.com/services/service-bus/) см. в следующих статьях.

* [Основные сведения об обмене сообщениями через служебную шину](service-bus-messaging-overview.md)
* [Базовая информация о служебной шине](service-bus-fundamentals-hybrid-solutions.md)
* [Архитектура служебной шины](service-bus-architecture.md)



<!--HONumber=Nov16_HO3-->


