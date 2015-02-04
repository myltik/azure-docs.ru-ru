<properties urlDisplayName="Service Bus Queues" pageTitle="Использование очередей Service Bus (.NET): Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues C#, Azure queues .NET" description="Узнайте, как использовать очереди Service Bus в Azure. Примеры кода написаны на C# с использованием API .NET." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />





# Использование очередей Service Bus

<span>В этом руководстве показано, как использовать очереди Service Bus. Примеры написаны на языке C# и используют интерфейс API .NET. Здесь описаны такие сценарии, как **создание очередей, отправка и получение сообщений**, а также **удаление очередей**. Дополнительную информацию об очередях см. в разделе [Дальнейшие действия]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## Настройка приложения для использования Service Bus

При создании приложения, использующего Service Bus, необходимо
добавить ссылку на сборку Service Bus и указать
соответствующие пространства имен.

## Получение пакета NuGet шины Service Bus

Пакет **NuGet** для Service Bus является самым простым способом получить API Service Bus и настроить для приложения все зависимости Service Bus. Расширение NuGet для Visual Studio упрощает установку и обновление библиотек и инструментов в Visual Studio и Visual Studio Express 2012 для Web.

Для установки пакета NuGet в приложении выполните следующие действия:

1.  В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.
2.  Найдите элемент WindowsAzure и выберите пункт **Azure Service Bus**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте это диалоговое окно.

    ![][7]

Теперь все готово, чтобы написать код для Service Bus.


## Практическое руководство. Настройка строки подключения для Service Bus

Service Bus использует строку подключения для хранения учетных данных и конечных точек. Строку подключения можно разместить в файле конфигурации, не задавая ее жестко в коде:

- При использовании облачных служб Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Azure (CSDEF- и CSCFG-файлы).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется сохранять строки подключения с помощью системы конфигурации .NET (например, в файле `web.config`).

В обоих случаях можно получить строку подключения с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этом руководстве.

### <a name="config-connstring"> </a>Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Azure и позволяет динамически изменять параметры конфигурации на портале управления Azure без повторного развертывания приложения.  Например, добавьте параметр в файл определения службы (CSDEF), как показано ниже:

	<ServiceDefinition name="WindowsAzure1">
	...
		<WebRole name="MyRole" vmsize="Small">
	    	<ConfigurationSettings>
	      		<Setting name="Microsoft.ServiceBus.ConnectionString" />
    		</ConfigurationSettings>
  		</WebRole>
	...
	</ServiceDefinition>

Затем задайте значения в файле конфигурации службы (CSCFG):

	<ServiceConfiguration serviceName="WindowsAzure1">
	...
		<Role name="MyRole">
			<ConfigurationSettings>
				<Setting name="Microsoft.ServiceBus.ConnectionString" 
						 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
			</ConfigurationSettings>
		</Role>
	...
	</ServiceConfiguration>

Используйте значения издателя и ключа, полученные на портале управления,как описано в предыдущем разделе.

### Настройка строки подключения при использовании веб-сайтов или виртуальных машин

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, web.config).  Строка подключения хранится с помощью элемента <appSettings>:

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Используйте значения издателя и ключа, полученные на портале управления,как описано в предыдущем разделе.

## Создание очереди

Операции управления для очередей Service Bus можно выполнять с помощью класса **NamespaceManager**. Класс **NamespaceManager** предоставляет методы для создания, перечисления и удаления очередей. 

В этом примере объект **NamespaceManager** создается с помощью класса Azure **CloudConfigurationManager** со строкой подключения, состоящей из базового адреса пространства имен служб Service Bus и учетных данных с соответствующими правами на управление. Эта строка подключения имеет вид 

	Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

Например, для параметров конфигурации из предыдущего раздела:

	// Create the queue if it does not exist already
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Существуют перегрузки метода **CreateQueue**, позволяющие настраивать свойства очереди, например задавать значение по умолчанию для "времени жизни", применяемое к сообщениям, отправленным в очередь. Эти параметры применяются с помощью класса **QueueDescription**. В следующем примере показано, как создать очередь с именем TestQueue, максимальным размером 5 ГБ и временем жизни сообщения по умолчанию, равным 1 минуте:

	// Configure Queue Settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

	// Create a new Queue with custom settings
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

**Примечание.** Можно использовать метод **QueueExists** для объектов **NamespaceManager**, чтобы проверить, существует ли уже очередь с указанным именем в пространстве имен службы.

## Практическое руководство. Отправка сообщений в очередь

Чтобы отправить сообщение в очередь Service Bus, приложение создает объект **QueueClient** с помощью строки подключения.

В следующем примере кода показано, как создать объект **QueueClient**
для очереди TestQueue, созданной ранее с помощью вызова API **CreateFromConnectionString**:

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
		QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

	Client.Send(new BrokeredMessage());

Сообщения, отправляемые в очереди Service Bus и получаемые из них - это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств (например, **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может задать текст сообщения, передав конструктору **BrokeredMessage** любой сериализуемый объект, после чего для сериализации объекта будет использоваться соответствующий **DataContractSerializer**. Кроме того, может быть предоставлен **System.IO.Stream**.  В следующем примере показано, как отправить пять тестовых сообщений в очередь TestQueue клиента **QueueClient**, полученного в предыдущем фрагменте кода:

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;   

       // Send message to the queue
       Client.Send(message);
     }

Очереди Service Bus поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, составляет 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

## Получение сообщений из очереди

Самым простым способом получения сообщений из очереди является использование объекта **QueueClient**. Этот объект может работать в двух режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда Service Bus получает запрос на чтение для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus помечает сообщение как использованное, то после того как приложение перезапускается и снова начинает обрабатывать сообщения, оно пропускает сообщение, которое использовалось до сбоя.

В режиме **PeekLock** (который является режимом по умолчанию) процесс получения проходит в два этапа, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), Service Bus завершает второй этап процесса получения, вызывая метод **Complete** для полученного сообщения. Когда Service Bus обнаруживает вызов **Complete**, сообщение помечается как использованное и удаляется из очереди.

В следующем примере показано, как получать и обрабатывать сообщения с помощью режима по умолчанию **PeekLock**. Чтобы задать другое значение **ReceiveMode**, можно использовать другую перегрузку метода **CreateFromConnectionString**. В этом примере создается бесконечный цикл и сообщения обрабатываются по мере поступления в очередь TestQueue.

    Client.Receive();
     
    // Continuously process messages sent to the "TestQueue" 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("Test Property: " + 
				message.Properties["TestProperty"]);

             // Remove message from queue
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in queue
             message.Abandon();
          }
       }
    } 

## Как обрабатывать сбои приложения и нечитаемые сообщения

Service Bus поддерживает функции, помогающие корректно выполнить восстановление после ошибок в приложении или при возникновении трудностей с обработкой сообщений. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **Abandon** (вместо метода **Complete**). После этого Service Bus разблокирует сообщение в очереди и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, блокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus автоматически разблокирует сообщение и снова сделает его доступным для получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **Complete** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

## Дальнейшие действия

Вы получили основное представление об очередях Service Bus. Чтобы поучить дополнительную информацию,
воспользуйтесь следующими ссылками.

-   См. справочник MSDN: [Очереди, разделы и подписки.][]
-   Сборка рабочего приложения, способного отправлять сообщения в очередь Service Bus и получать их из нее: [Учебник по обмену сообщениями .NET через посредника в Service Bus].

  [Дальнейшие действия]: #next-steps
  [Что такое очереди Service Bus]: #what-queues
  [Создание пространства имен службы]: #create-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-creds
  [Настройка приложения для использования Service Bus]: #configure-app
  [Практическое руководство. Настройка строки подключения для Service Bus]: #set-up-connstring
  [Практическое руководство. Настройка строки подключения]: #config-connstring
  [Практическое руководство. Создание очереди]: #create-queue
  [Практическое руководство. Отправка сообщений в очередь]: #send-messages
  [Практическое руководство. Получение сообщений из очереди]: #receive-messages
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #handle-crashes
  [Основные понятия очередей]: ./media/service-bus-dotnet-how-to-use-queues/sb-queues-08.png
  [Портал управления Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Очереди, разделы и подписки.]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367516.aspx
  [Учебник по обмену сообщениями .NET через посредника в Service Bus]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367512.aspx

<!--HONumber=35.1-->
