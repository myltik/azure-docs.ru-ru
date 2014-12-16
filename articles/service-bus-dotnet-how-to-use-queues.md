<properties urlDisplayName="Service Bus Queues" pageTitle="Как использовать очереди шины обслуживания (.NET) - Azure" metaKeywords="очереди шины обслуживания Azure, очереди Azure, обмен сообщениями Azure, очереди Azure C#, очереди Azure .NET" description="Learn how to use Service Bus queues in Azure. Code samples written in C# using the .NET API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />





# Использование очередей Service Bus

<span>В этом руководстве показано, как использовать очереди Service Bus. Примеры написаны на языке C\# и используют интерфейс API .NET. Здесь описаны такие сценарии, как **создание очередей, отправка и получение сообщений**, а также **удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

<h2>Настройка приложения для использования Service Bus</h2>

При создании приложения, использующего Service Bus, необходимо добавить ссылку на сборку шины обслуживания и указать соответствующие пространства имен.

<h2>Получение пакета NuGet шины Service Bus</h2>

Пакет **NuGet** служебной шины является самым простым способом получить API шины и настроить для приложения все зависимости шины. Расширение NuGet для Visual Studio упрощает установку и обновление библиотек и инструментов в Visual Studio и Visual Studio Express 2012 для Web.

Для установки пакета NuGet в приложении выполните следующие действия:

1. В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, а затем нажмите кнопку
    **Управление пакетами NuGet**.
2. Найдите WindowsAzure и выберите **Azure
    элемент Service Bus**. Нажмите **Установить** для завершения установки,
    а затем закройте это диалоговое окно.

    ![][7]

Теперь все готово, чтобы написать код для Service Bus.

<h2>Как настроить строку подключения для служебной шины</h2>

Служебная шина использует строку подключения для хранения учетных данных и конечных точек. Строку подключения можно разместить в файле конфигурации, не задавая ее жестко в коде:

- При использовании облачных служб Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Azure (файлы CSDEF и CSCFG).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется сохранять строки подключения с помощью системы конфигурации .NET (например, в файле web.config).

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

You then specify values in the service configuration (`*.cscfg`) file:

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

Используйте значения издателя и ключа, полученные на портале управления, как описано в предыдущем разделе.

### Настройка строки подключения при использовании веб-сайтов или виртуальных машин

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, web.config).  Строка подключения сохраняется с помощью элемента <appSettings>:

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Используйте значения издателя и ключа, полученные на портале управления, как описано в предыдущем разделе.

<h2>Создание очереди</h2>

Операции управления для очередей Service Bus можно выполнять с помощью класса **NamespaceManager**. Класс **NamespaceManager** предоставляет методы для создания, перечисления и удаления очередей. 

В этом примере объект **NamespaceManager** создается с помощью класса Azure **CloudConfigurationManager** со строкой подключения, состоящей из базового адреса пространства имен служб служебной шины и учетных данных с соответствующими правами на управление. Эта строка подключения имеет вид 

	Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

For example, given the configuration settings in the previous section:

	// Create the queue if it does not exist already
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Существуют перегрузки метода **CreateQueue**, позволяющие настраивать свойства очереди, например задавать значение по умолчанию для срока действия, применяемое к сообщениям, отправленным в очередь. Эти параметры применяются с помощью класса **QueueDescription**. В следующем примере показано, как создать очередь с именем TestQueue, максимальным размером 5 ГБ и временем жизни сообщения по умолчанию, равным 1 минуте.

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

<h2>Отправка сообщений в очередь</h2>

Чтобы отправить сообщение в очередь шины обслуживания, приложение создает объект **QueueClient** с помощью строки подключения.

В следующем примере кода показано, как создать объект **QueueClient** для очереди TestQueue, созданной с помощью вызова API **CreateFromConnectionString**:

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
		QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

	Client.Send(new BrokeredMessage());

Сообщения, отправляемые в очереди Service Bus и получаемые из них - это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств (например, **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из строковых данных. Приложение может задать текст сообщения, передав **любой сериализуемый объект конструктору **BrokeredMessage**, после чего для сериализации объекта будет использоваться**соответствующий сериализатор. Кроме того, может быть предоставлен **System.IO.Stream**.

В следующем примере показано, как отправить пять тестовых сообщений в очередь TestQueue клиента **QueueClient**, полученного в предыдущем фрагменте кода.

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

Очереди шины обслуживания поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, составляет 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

<h2>Получение сообщений из очереди</h2>

Самым простым способом получения сообщений из очереди является использование объекта **QueueClient**. Этот объект может работать в двух режимах: **ReceiveAndDelete** и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда шина обслуживания получает запрос на чтение для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку шина обслуживания помечает сообщение как использованное, то после того, как приложение перезапускается и снова начинает обрабатывать сообщения, оно пропускает сообщение, которое использовалось до сбоя.

В режиме **PeekLock** (который является режимом по умолчанию) процесс получения проходит в два этапа, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **Complete** для обработанного сообщения. Когда шина обслуживания обнаруживает вызов **Complete**, сообщение помечается как использованное и удаляется из очереди.

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

<h2>Обработка сбоев приложения и нечитаемых сообщений</h2>
 Служебная шина поддерживает функции, помогающие надлежащим образом восстановиться после ошибок в приложении или трудностей, возникших при обработке сообщений. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **Abandon** (вместо метода **Complete**). После этого шина обслуживания разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, блокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), то Service Bus автоматически разблокирует сообщение и снова сделает его доступным для получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **Complete**, это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

<h2>Дальнейшие действия</h2>

Вы ознакомились с основами использования очередей шины обслуживания. Теперь вы можете использовать
ссылки на дополнительные сведения.

-   Справочник MSDN: [Очереди, разделы и подписки.][]
-   Создать рабочее приложение, которое отправляет и получает сообщения в
    из очереди шины: [Учебник по управляемому обмену сообщениями Service Bus
    в .NET].

  [Дальнейшие действия]: #next-steps
  [Что такое очереди Service Bus]: #what-queues
  [Создание пространства имен службы]: #create-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-creds
  [Настройка приложения для использования шины обслуживания]: #configure-app
  [Практическое руководство. Настройка строки подключения для служебной шины]: #set-up-connstring
  [Практическое руководство. Настройка строки подключения]: #config-connstring
  [Практическое руководство. Создать очередь]: #create-queue
  [Практическое руководство. Отправка сообщений в очередь]: #send-messages
  [Практическое руководство. Получение сообщений из очереди]: #receive-messages
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #handle-crashes
  [Основные понятия очередей]: ./media/service-bus-dotnet-how-to-use-queues/sb-queues-08.png
  [Портал управления Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Очереди, разделы и подписки.]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367516.aspx
  [Учебник по управляемому обмену сообщениями Service Bus в .NET]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367512.aspx
