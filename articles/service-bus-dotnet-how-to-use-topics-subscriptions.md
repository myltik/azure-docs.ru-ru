<properties linkid="dev-net-how-to-service-bus-topics" urlDisplayName="Разделы Service Bus" pageTitle="Использование разделов Service Bus (.NET) — Azure" metaKeywords="Начало работы с разделами Azure Service Bus, обмен сообщениями публикаций и подписок Azure, разделы, посвященные обмену сообщениями Azure и подпискам C# " description="Узнайте, как использовать разделы Service Bus и подписки в Azure. Примеры кода написаны для приложений .NET. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="Использование разделов и подписок Service Bus" authors=""  solutions="" writer="sethm" manager="dwrede" editor="mattshel"  />





# Использование разделов и подписок Service Bus

<span>В этом руководстве показывается, как использовать разделы и подписки Service Bus. Примеры написаны на языке C\# и используют интерфейс .NET API. В этой статье описаны такие сценарии, как **создание разделов и подписок, создание фильтров подписок, отправка сообщений** в раздел, **получение сообщений от подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия][]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

<h2><span class="short-header">Настройка приложения</span>Настройка приложения для использования Service Bus</h2>

При создании приложения, использующего Service Bus, необходимо добавить ссылку на сборку Service Bus и соответствующие пространства имен.

<h2><span class="short-header">Получение пакета NuGet</span>Получение пакета NuGet для Service Bus</h2>

Пакет **NuGet** шины Service Bus является самым простым способом получить API шины Service Bus и настроить для приложения все зависимости шины Service Bus. Расширение Visual Studio для NuGet упрощает установку и обновления библиотек и инструментов в Visual Studio и в Visual Studio Express 2012 for Web. Пакет NuGet шины Service Bus является самым простым способом получить API шины Service Bus и настроить для приложения все зависимости шины Service Bus.

Для установки пакета NuGet в приложении выполните следующие действия:

1.  В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.
2.  Найдите WindowsAzure и выберите пункт **Azure Service Bus**. Нажмите кнопку **Установить**, чтобы закончить установку, затем закройте это диалоговое окно.

    ![][7]

Теперь все готово, чтобы написать код для Service Bus.

<h2><span class="short-header">Настройка строки подключения</span> Настройка строки подключения Service Bus</h2>

Service Bus использует строку подключения для хранения учетных данных и конечных точек. Строку подключения можно разместить в файле конфигурации, не задавая ее жестко в коде:

- При использовании облачных служб Azure рекомендуется хранить строку подключения, используя систему конфигурации служб Azure (файлы `*CSDEF` и `*CSCFG`).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется хранить строки подключения с помощью системы конфигурации .NET (например, в файле `web.config`).

В обоих случаях можно получить строку подключения с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этом руководстве.

### <a name="config-connstring"> </a>Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Azure и позволяет динамически изменять параметры конфигурации на портале управления Azure без повторного развертывания приложения.  Например, добавьте параметр в файл определения службы (`*CSDEF`), как показано ниже:

	<ServiceDefinition name="WindowsAzure1">
	...
		<WebRole name="MyRole" vmsize="Small">
	    	<ConfigurationSettings>
	      		<Setting name="Microsoft.ServiceBus.ConnectionString" />
    		</ConfigurationSettings>
  		</WebRole>
	...
	</ServiceDefinition>

Затем задайте значения в файле конфигурации службы (`*CSCFG`):

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

Используйте значения издателя и ключа, полученные из портала управления, как описано в предыдущем разделе.

### Настройка строки подключения при использовании веб-сайтов или виртуальных машин

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, `web.config`).  Строка подключения хранится с помощью элемента `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Используйте значения издателя и ключа, полученные из портала управления, как описано в предыдущем разделе.

<h2><span class="short-header">Создание раздела</span>Создание раздела</h2>

Операции управления для разделов и подписок Service Bus можно выполнять с помощью класса **NamespaceManager**. Класс **NamespaceManager** предоставляет методы для создания, перечисления и удаления очередей. 

В этом примере объект **NamespaceManager** создается с помощью класса **CloudConfigurationManager** Azure со строкой подключения, состоящей из базового адреса пространства имен служб Service Bus и учетных данных с соответствующими правами на управление. Эта строка подключения имеет вид

	Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedSecretIssuer=<issuerName>;SharedSecretValue=<yourDefaultKey>

Например, для параметров конфигурации из предыдущего раздела:

	// Create the topic if it does not exist already
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Существуют перегрузки метода **CreateTopic**, позволяющие настраивать свойства раздела, например задавать значение по умолчанию для "срока жизни", применяемое к сообщениям, отправленным в раздел. Эти параметры применяются с помощью класса **TopicDescription**. В следующем примере показывается, как создать раздел с именем "TestTopic", максимальным размером 5 ГБ и сроком жизни сообщения по умолчанию, равным 1 минуте.

	// Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

	// Create a new Topic with custom settings
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**Примечание.** Можно использовать метод **TopicExists** для объектов **NamespaceManager**, чтобы проверить, существует ли уже раздел с указанным именем в пространстве имен служб.

<h2><span class="short-header">Создание подписок</span>Создание подписок</h2>

Можно также создать подписки раздела, используя класс **NamespaceManager**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Если при создании подписки не указан фильтр, используется фильтр по умолчанию **MatchAll**. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем "AllMessages" и используется фильтр по умолчанию  **MatchAll**.

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Создание подписок с фильтрами

Можно также настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в конкретной подписке раздела.

Самым гибким типом фильтра, поддерживаемым подписками, является
**SqlFilter**, реализующий подмножество SQL92. SQL-фильтры работают со свойствами сообщений, опубликованных в разделе. Дополнительные сведения о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][].

В следующем примере создается подписка с именем "HighMessages" и фильтром
**SqlFilter**, которая выбирает только сообщения со значением
свойства **MessageNumber**, превышающим 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
		new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"HighMessages", 
		highMessagesFilter);

Аналогично в следующем примере создается подписка с именем LowMessages и фильтром **SqlFilter**, которая выбирает только сообщения, у которых значение свойства **MessageNumber** не превышает 3.

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
		new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"LowMessages", 
		lowMessagesFilter);

Теперь, если сообщение отправляется в раздел "TestTopic", оно всегда будет доставляться получателям, подписанным на подписку раздела "AllMessages", и выборочно доставляться получателям, подписанным на подписки разделов "HighMessages" и "LowMessages" (в зависимости от содержания сообщения).

<h2><span class="short-header">Отправка сообщений в раздел</span>Отправка сообщений в раздел</h2>

Чтобы отправить сообщение в разделе Service Bus, приложение создает
объект **TopicClient**, используя строку подключения.

В следующем примере кода показано, как создать объект **TopicClient** для раздела "TestTopic", созданного с помощью вызова API **CreateFromConnectionString**:

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

   	TopicClient Client = 
		TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

	Client.Send(new BrokeredMessage());
 

Сообщения, отправленные в разделы Service Bus, являются экземплярами
класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств (например, **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может определить текст сообщения, передав любой сериализуемый объект в конструктор
**BrokeredMessage**, затем соответствующий **DataContractSerializer** будет использоваться для сериализации объекта. Либо
можно предоставить **System.IO.Stream**.

В следующем примере показано, как отправить 5 тестовых сообщений клиенту **TopicClient** раздела "TestTopic", полученному в предыдущем фрагменте кода.
Обратите внимание, как значение свойства **MessageNumber** для каждого сообщения меняется в зависимости от итерации цикла (это определяет подписки, которые получат это сообщение).

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
		new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;

       // Send message to the topic
       Client.Send(message);
     }

Разделы Service Bus поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения — 64 КБ). Ограничения на количество сообщений в разделе нет, но есть ограничение на максимальный общий размер сообщений в разделе. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

<h2><span class="short-header">Получение сообщений от подписки</span>Получение сообщений от подписки</h2>

Самым простым способом получать сообщения от подписки является использование
объекта **SubscriptionClient**. Объекты **SubscriptionClient** могут работать в двух различных режимах: **ReceiveAndDelete** и **PeekLock**

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда Service Bus получает запрос на чтения для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus помечает сообщение как использованное, то приложение, когда оно перезапустится и снова начнет обрабатывать сообщения, пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** (который является режимом по умолчанию) процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы другие получатели не могли его принять, а затем возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), шина Service Bus завершает второй этап процесса получения, вызывая метод **Complete** для полученного сообщения. Когда Service
Bus обнаруживает вызов **Complete**, сообщение помечается как использованное и удаляется из подписки.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима по умолчанию **PeekLock**. Чтобы задать другое значение **ReceiveMode**, можно использовать другую перегрузку метода **CreateFromConnectionString**. В этом примере создается бесконечный цикл и сообщения обрабатываются, как только они поступают в подписку "HighMessages". Обратите внимание, что путь к подписке "HighMessages"
предоставляется в виде "<*topic
path*\>/subscriptions/<*subscription name*\>".

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
		SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

	Client.Receive();
     
	// Continuously process messages received from the HighMessages subscription 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("MessageNumber: " + 
				message.Properties["MessageNumber"]);

             // Remove message from subscription
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in subscription
             message.Abandon();
          }
       }
    } 

<h2><span class="short-header">Сбои приложения и нечитаемые сообщения</span>Обработка сбоев приложения и нечитаемых сообщений</h2>

Service Bus предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать метод **Abandon** для полученного сообщения (вместо
метода **Complete**). После этого Service Bus разблокирует сообщение в подписке и снова сделает его доступным для получения тем же или другим приложением.

Кроме того, с сообщением, блокированным в подписке, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), то Service Bus автоматически разблокирует сообщение и снова сделает его доступным для получения.

Если сбой в приложении происходит после обработки сообщения, но перед выдачей запроса **Complete**, то сообщение будет повторно доставлено приложению после его перезапуска. Часто этот подход называют
**обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны предусмотреть дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью
свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

<h2><span class="short-header">Удаление разделов и подписок</span>Удаление разделов и подписок</h2>

В приведенном ниже примере показано, как удалить раздел с именем
**TestTopic** из пространства имен **HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем примере кода показано, как удалить подписку с именем
**HighMessages** из раздела **TestTopic**:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

<h2><span class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>

Вы познакомились с основами использования разделов и подписок Service Bus. Для получения дополнительных сведений используйте следующие ссылки.

-   См. справочник MSDN: [очереди, разделы и подписки][]
-   Справочник API для [SqlFilter][].
-   Создание работающего приложения, отправляющего сообщения в очередь Service Bus и получающее их из нее: [Учебник по управляемому обмену сообщениями Service Bus в .NET][].

  [Дальнейшие действия]: #nextsteps
  [Что такое разделы и подписки Service Bus]: #what-is
  [Создание пространства имен службы]: #create-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-creds
  [Настройка приложения для использования шины обслуживания]: #configure-app
  [Практическое руководство. Настройка строки подключения для Service Bus]: #set-up-connstring
  [Практическое руководство. Настройка строки подключения]: #config-connstring
  [Практическое руководство. Создание раздела]: #create-topic
  [Практическое руководство. Создание подписок]: #create-subscriptions
  [Практическое руководство. Отправка сообщений в раздел]: #send-messages
  [Практическое руководство. Получение сообщений от подписки]: #receive-messages
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #handle-crashes
  [Практическое руководство. Удаление разделов и подписок]: #delete-topics
  
  [Основные понятия раздела]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Портал управления Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ru-ru/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Учебник по управляемому обмену сообщениями Service Bus в .NET]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367512.aspx

