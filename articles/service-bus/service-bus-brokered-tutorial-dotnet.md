<properties 
   pageTitle="Руководство по обмену сообщениями .NET через служебную шину через посредника | Microsoft Azure"
   description="Руководство по обмену сообщениями .NET через посредника"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/14/2015"
   ms.author="sethm" />

# Учебное пособие по обмену сообщениями .NET через посредника в служебной шине

Служебная шина Azure предоставляет два комплексных решения для обмена сообщениями. Первое решение основано на централизованной службе "ретрансляции", которая запущена в облаке. Это решение поддерживает множество различных транспортных протоколов и стандартов веб-служб, включая SOAP, WS-* и REST. Клиенту не требуются прямое подключение к локальной службе и информация о ее местонахождении, а локальной службе не требуется открывать на брандмауэре входящие порты.

Второе решение для обмена сообщениями обеспечивает "посреднические" возможности обмена сообщениями. Такой обмен сообщениями можно считать асинхронным, или разделенным. Он поддерживает публикацию и подписку, временное разделение, а также сценарии балансировки нагрузки с использованием инфраструктуры обмена сообщениями через служебную шину. Разделенный обмен данными имеет множество преимуществ. Например, клиенты и серверы могут подключаться по необходимости, выполняя свои операции в асинхронном режиме.

Этот учебник предоставит вам общие сведения и практический опыт работы с очередями, одними из основных компонентов обмена сообщениями через служебную шину через посредника. После работы с разделами в этом учебнике у вас получится приложение, которое заполняет список сообщений, создает очередь и отправляет сообщения в эту очередь. Наконец, приложение получает и отображает сообщения из очереди, а затем освобождает свои ресурсы и завершает работу. Соответствующий учебник, в котором описывается создание приложения, использующего возможности обмена сообщениями через служебную шину с ретрансляцией см. в статье [Руководство по обмену сообщениями с ретрансляцией через служебную шину](service-bus-relay-tutorial.md).

## Введение и предварительные требования

Очереди предлагают доставку сообщений конкурирующим потребителям FIFO. Доставка сообщений FIFO означает, что получатели принимают и обрабатывают сообщения в порядке их добавления в очередь, и каждое сообщение принимается и обрабатывается только одним потребителем сообщений. Основным преимуществом использования очередей является достижение *временного разделения* компонентов приложения: иными словами, создатели и потребители не должны отправлять и получать сообщения в одно и то же время, поскольку сообщения хранятся в очереди на протяжении долгого времени. Сопутствующее преимущество заключается в *выравнивании нагрузки* — оно позволяет производителям и потребителям отправлять и получать сообщения с разной скоростью.

Ниже приведены некоторые административные и подготовительные действия, которые необходимо выполнить перед началом работы с данным руководством. Сначала необходимо создать пространство имен службы и получить ключ подписи общего доступа (SAS). Пространство имен службы определяет границы каждого приложения, предоставляемого через Service Bus. Ключ SAS автоматически создается системой при создании пространства имен службы. Пространство имен и ключ SAS совместно образуют учетные данные, на основе которых служебная шина осуществляет проверку подлинности и разрешает доступ к приложению.

### Создание пространства имен службы и получение ключа SAS

1. Чтобы создать пространство имен службы, перейдите на [классический портал Azure][]. В левой части щелкните **Служебная шина** и нажмите кнопку **Создать**. Введите имя для пространства имен, а затем щелкните значок с изображением флажка.

1. В главном окне [классического портала Azure][] щелкните пространство имен службы, созданное на предыдущем шаге.

1. Нажмите **Настроить**.

1. В разделе **Генератор подписи общего доступа** запишите первичный ключ, связанный с политикой **RootManagerSharedAccessKey**, или скопируйте его в буфер обмена. Этот ключ потребуется позже в этом руководстве.

Затем необходимо создать проект Visual Studio и написать две вспомогательные функции, которые загружают разделенный запятыми список сообщений в строго типизированный объект [List](https://msdn.microsoft.com/library/6sh2ey19.aspx) .NET [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx).

### Создание проекта Visual Studio

1. Откройте Visual Studio с правами администратора, щелкнув программу правой кнопкой мыши в меню "Пуск" и выбрав **Запуск от имени администратора**.

1. Создайте новый проект консольного приложения. В меню **Файл** выберите **Создать**, а затем **Проект**. В диалоговом окне **Новый проект** выберите **Visual C#** (если **Visual C#** не отображается, откройте список **Другие языки**). Выберите шаблон **Консольное приложение** и назовите его **QueueSample**. Используйте **расположение** по умолчанию. Нажмите кнопку **ОК**, чтобы создать проект.

1. Воспользуйтесь диспетчером пакетов NuGet для добавления библиотек служебной шины в проект.
	1. В обозревателе решений щелкните правой кнопкой мыши каталог проекта и выберите **Управление пакетами NuGet**.
	2. В диалоговом окне **Управление пакетами Nuget** введите в строке поиска **Служебная шина** и нажмите кнопку **Установить**. <br />
1. В окне обозревателя решений дважды щелкните файл Program.cs, чтобы открыть его в редакторе Visual Studio. Измените имя пространства имен с `QueueSample` (имя по умолчанию) на `Microsoft.ServiceBus.Samples`.

	```
	Microsoft.ServiceBus.Samples
	{
	    …
	```

1. Измените инструкции `using`, как показано в следующем коде.

	```
	using System;
	using System.Collections.Generic;
	using System.Data;
	using System.IO;
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	```

1. Создайте текстовый файл с именем Data.csv и скопируйте в него следующий текст, разделенный запятыми.

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

	Сохраните и закройте файл Data.csv и запомните каталог, в который его сохранили.

1. В обозревателе решений щелкните правой кнопкой мыши имя проекта (в данном примере **QueueSample**), нажмите кнопку **Добавить**, затем щелкните **Существующий элемент**.

1. Найдите файл Data.csv, созданный на шаге 6. Щелкните файл и нажмите кнопку **Добавить**. Убедитесь, что в списке типов файлов выбрано **Все файлы (*.*)**.

### Создание функции, которая анализирует список сообщений

1. Перед методом `Main()` объявите две переменные. Первая должна иметь тип **DataTable** и будет содержать список сообщений в файле Data.csv. Вторая должна иметь тип объекта List, строго типизированный к [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Эта переменная представляет собой список сообщений, доставленных через посредника, которые будут использоваться далее в этом руководстве.

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    public class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList;
	```

1. За пределами `Main()` определите метод `ParseCSV()`, который анализирует список сообщений в файле Data.csv и загружает сообщения в таблицу [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx), как показано ниже. Этот метод возвращает объект **DataTable**.

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

1. В метод `Main()` добавьте вызов метода `ParseCSVFile()`.

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	
	}
	```

### Создание функции, которая загружает список сообщений

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

1. В `Main()` сразу после вызова `ParseCSVFile()` добавьте вызов метода `GenerateMessages()` со значением, возвращаемым `ParseCSVFile()`, в качестве аргумента.

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	}
	```

### Получение учетных данных пользователя

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

1. Затем создайте функцию, которая принимает и сохраняет пространство имен службы и ключ SAS. Добавьте этот метод вне `Main()`. Например:

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

1. В `Main()` сразу после вызова `GenerateMessages()` добавьте вызов метода `CollectUserInput()`.

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

### Выполните сборку решения.

В меню **Сборка** Visual Studio выберите **Выполнить сборку решения** или нажмите F6, чтобы проверить правильность выполнения действий.

## Создание учетных данных управления

На этом шаге определяются операции управления, которые будут использованы для создания учетных данных подписи общего доступа (SAS), с которыми будет выполняться проверка подлинности приложения.

1. Для ясности в этом руководстве все операции по работе с очередью помещены в отдельный метод. Создайте метод `Queue()` в классе `Program` под методом `Main()`. Например:
 
	```
	public static void Main(string[] args)
	{
	…
	}
	static void Queue()
	{
	}
	```

1. Затем необходимо создать учетные данные SAS с помощью объекта [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx). Метод создания принимает имя ключа SAS и значение, полученное в методе `CollectUserInput()`. Добавьте в метод `Queue()` следующий код:

	```
	static void Queue()
	{
	    // Create management credentials
	    TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
	}
	```
### Создание диспетчера пространства имен

1. Создайте новый объект управления пространством имен с URI, содержащим имя пространства имен и учетные данные управления, полученные на предыдущем шаге, в качестве аргументов. Добавьте следующий код непосредственно после кода, добавленного на предыдущем шаге.
	
	```
	NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", <namespaceName>, string.Empty), credentials);
	```

### Пример

В этот момент ваш код должен выглядеть примерно так:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
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
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
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

На следующем шаге вы создадите очередь, в которую будете отправлять сообщения.

## Отправка сообщений в очередь

На этом шаге вы создаете очередь и затем отправляете в нее сообщения, содержащиеся в списке сообщений, доставленных через посредника.

### Создание очереди и отправка сообщений в очередь

1. Сначала создайте очередь. Назовите ее, например, `myQueue` и объявите ее сразу после операций управления, добавленных на предыдущем шаге:

	```
	QueueDescription myQueue;
	myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
	```

1. В методе `Queue()` создайте объект фабрики обмена сообщениями, указав только что созданный URI служебной шины в качестве аргумента. Добавьте следующий код сразу после операций управления, добавленных на предыдущем шаге:

	```
	MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
	```

1. Затем создайте объект очереди с помощью класса [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Добавьте следующий код сразу после кода, добавленного на предыдущем шаге:

	```
	QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
	```

1. Затем добавьте код, который просматривает список сообщений, доставленных через посредника, который вы создали ранее, и отправляет каждое сообщение в очередь. Добавьте следующий код сразу после инструкции `CreateQueueClient()` на предыдущем шаге:
	
	```
	// Send messages
	Console.WriteLine("Now sending messages to the queue.");
	for (int count = 0; count < 6; count++)
	{
	    var issue = MessageList[count];
	    issue.Label = issue.Properties["IssueTitle"].ToString();
	    myQueueClient.Send(issue);
	    Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
	}
	```

## Получение сообщений из очереди

На этом шаге вы получаете список сообщений из очереди, созданной на предыдущем шаге.

### Создание получателя и получение сообщений из очереди

В методе `Queue()` пройдитесь по очереди сообщений и получите сообщения с помощью метода [Microsoft.ServiceBus.Messaging.QueueClient.Receive](https://msdn.microsoft.com/library/azure/hh322678.aspx), выводя каждое сообщение в консоли. Добавьте следующий код сразу после кода, добавленного на предыдущем шаге.

	```
	Console.WriteLine("Now receiving messages from Queue.");
	BrokeredMessage message;
	while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
	    {
	        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
	        message.Complete();
	
	        Console.WriteLine("Processing message (sleeping...)");
	        Thread.Sleep(1000);
	    }
	```

### Завершение метода `Queue()` и освобождение ресурсов

Сразу после предыдущего кода добавьте следующий код для освобождения ресурсов фабрики и очереди сообщений.

	```
	factory.Close();
	myQueueClient.Close();
	namespaceClient.DeleteQueue("IssueTrackingQueue");
	```

### Вызов метода `Queue()`

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

### Пример

Следующий код содержит полный код приложения **QueueSample**.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
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
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);

      QueueDescription myQueue;
      myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");

      MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
      QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

      // Send messages
      Console.WriteLine("Now sending messages to the Queue.");
      for (int count = 0; count < 6; count++)
      {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        myQueueClient.Send(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
      }

      Console.WriteLine("Now receiving messages from Queue.");
      BrokeredMessage message;
      while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
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

## Сборка и запуск приложения QueueSample

Теперь после завершения предыдущих шагов можно собрать и запустить приложение **QueueSample**.

### Сборка приложения QueueSample

В Visual Studio в меню **Сборка** щелкните **Выполнить сборку решения** или нажмите F6. При возникновении ошибок проверьте правильность кода по полному примеру, приведенному в конце предыдущего шага.

### Запуск приложения QueueSample

1. Перед запуском приложения убедитесь, что вы создали пространство имен службы и получили ключ SAS, как описано в разделе [Введение и предварительные требования](#introduction-and-prerequisites).

1. Откройте веб-браузер и перейдите в [классический портал Azure][].

1. В дереве слева щелкните **Служебная шина**.

1. Щелкните по имени пространства имен, которое хотите использовать. В нижней части страницы щелкните **Сведения о подключении**. Запишите строку подключения с ключом SAS или скопируйте ее в буфер обмена.

1. В Visual Studio в меню **Отладка** выберите **Начать отладку** или нажмите F5. При появлении запроса введите имя пространства имен службы и ключ, полученный на предыдущем шаге.

## Дальнейшие действия

В этом учебнике было показано создание приложения клиента и службы служебной шины с использованием возможностей обмена сообщениями через посредника служебной шины. Аналогичное руководство, в котором используется [обмен сообщениями с ретрансляцией](service-bus-messaging-overview.md#Relayed-messaging) см. в разделе [Руководство по обмену сообщениями с ретрансляцией через служебную шину](service-bus-relay-tutorial.md).

Дополнительные сведения о [служебной шине](https://azure.microsoft.com/services/service-bus/) см. в следующих разделах.

- [Основные сведения об обмене сообщениями через служебную шину](service-bus-messaging-overview.md)
- [Базовая информация о служебной шине](service-bus-fundamentals-hybrid-solutions.md)
- [Архитектура служебной шины](service-bus-architecture.md)

[классический портал Azure]: http://manage.windowsazure.com
[классического портала Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0218_2016-->