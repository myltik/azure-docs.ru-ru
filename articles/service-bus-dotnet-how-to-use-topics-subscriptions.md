<properties urlDisplayName="Service Bus Topics" pageTitle="Использование разделов Service Bus (.NET). Azure" metaKeywords="Приступая к работе с разделами служебной шины Azure, публикация подписка отправка сообщений Azure, отправка сообщений в разделы и подписки Azure C# " description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for .NET applications. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />





# Использование разделов и подписок Service Bus

<span>Из этого руководства вы узнаете, как использовать разделы и подписки служебной шины. Примеры написаны на языке C# и используют интерфейс API .NET. В этой статье описаны такие сценарии, как **создание разделов и подписок, создание фильтров подписок, отправка сообщений** в раздел, **получение сообщений от подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия][]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

<h2>Настройка приложения для использования Service Bus</h2>

При создании приложения, использующего служебную шину, вам понадобится добавить ссылку на сборку Service Bus и включить туда соответствующие пространства имен.

<h2>Получение пакета NuGet шины Service Bus</h2>

Пакет **NuGet** для Service Bus - самый простой способ получить
Service Bus API и сконфигурировать свое приложение со всеми зависимостями Service Bus. Расширение NuGet для Visual Studio упрощает установку и обновление библиотек и инструментов в Visual Studio и Visual Studio Express 2012 для Web. Пакет NuGet служебной шины является самым простым способом получить API служебной шины и настроить для приложения все зависимости.

Для установки пакета NuGet в приложении выполните следующие действия:

1.  Щелкните в обозревателе решений правой кнопкой мыши пункт **Ссылки**, затем щелкните
    **Управление пакетами NuGet**.
2.  Выполните поиск по "Windows Azure" и выберите пункт **Azure Service Bus**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте это диалоговое окно.

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

<h2>Создание раздела</h2>

Операции управления для разделов и подписок Service Bus можно выполнять через класс **NamespaceManager**. Класс **NamespaceManager** предоставляет методы для создания, перечисления и удаления очередей. 

В этом примере объект **NamespaceManager** создается с помощью класса Azure **CloudConfigurationManager** со строкой подключения, состоящей из базового адреса пространства имен служб служебной шины и учетных данных с соответствующими правами на управление. Эта строка подключения имеет вид 

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

Существуют перегруженные версии метода **CreateTopic**, позволяющие настраивать свойства этого раздела, например задавать стандартное значение "time-to-live" (срока жизни), применяемое к сообщениям, отправляемым в раздел. Эти параметры применяются с помощью класса **TopicDescription**. В следующем примере демонстрируется создание раздела по имени TestTopic с максимальным размером в 5 Гб и сроком жизни по умолчанию в 1 минуту.

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

**Примечание.** Можно использовать метод **TopicExists** на объектах **NamespaceManager** , чтобы проверить, не существует ли уже в пространстве имен службы раздел с указанным именем.

<h2>Создание подписок</h2>

Можно также создавать подписки на разделы с помощью класса **NamespaceManager**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** используется по умолчанию, если при создании подписки не указать фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка по имени AllMessages, где используется стандартный фильтр **MatchAll**.

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Создание подписок с фильтрами

Можно также настраивать фильтры, позволяющие определять, какие посылаемые в раздел сообщения должны появляться в рамках конкретной подписки.

Самый гибкий тип фильтра, который поддерживается подписками, - это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с фильтром SQL, см. в описании синтаксиса [SqlFilter.SqlExpression][].

В следующем примере создается подписка по имени HighMessages с фильтром **SqlFilter**, выбирающим только сообщения, пользовательское свойство **MessageNumber** в которых больше 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
		new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"HighMessages", 
		highMessagesFilter);

Similarly, the following example creates a subscription named
"LowMessages" with a **SqlFilter** that only selects messages that have
a **MessageNumber** property less than or equal to 3:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
		new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"LowMessages", 
		lowMessagesFilter);

Теперь когда в раздел TestTopic отправляется сообщение, оно будет всегда доставляться получателям, подписанным на подписку раздела AllMessages и выборочно доставляться получателям, подписанным на подписки разделов HighMessages и LowMessages в зависимости от содержания сообщения.

<h2>Отправка сообщений в раздел</h2>

Чтобы отправить сообщение в раздел Service Bus, ваше приложение создает объект **TopicClient**, использующий строку подключения.

Приведенный ниже код демонстрирует создание объекта **TopicClient** для раздела TestTopic, созданного выше с помощью API-вызова **CreateFromConnectionString**:

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

   	TopicClient Client = 
		TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

	Client.Send(new BrokeredMessage());
 

Сообщения, отправляемые в разделы Service Bus и получаемые из них, - это экземпляры класса **BrokeredMessage**. Объекты **BrokeredMessage** обладают набором стандартных свойств, (таких как **Label** и **TimeToLive**), словарем, хранящим разные пользовательские свойства, зависящие от приложения, и основным набором произвольных данных приложения. Приложение может задавать тело сообщения, передавая любой сериализуемый объект в конструктор **BrokeredMessage**, тогда для сериализации объекта будет использовать соответствующий **DataContractSerializer**. Кроме того, может быть предоставлен **System.IO.Stream**.

В следующем примере продемонстрирована отправка пяти тестовых сообщений в TestTopic **TopicClient**, полученный в приведенном выше фрагменте кода. Обратите внимание, значение свойства **MessageNumber** каждого сообщения изменяется в цикле (это определяет, какие подписки получат сообщение):

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

Разделы Service Bus поддерживают максимальный размер сообщения в 256 Кб (размер заголовка, включающего в себя стандартные и пользовательские свойства приложения, может быть не более 64 Кб). Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ.

<h2>Получение сообщений от подписки</h2>

Самый простой способ получать сообщения из подписки - использование объекта **SubscriptionClient**. Объекты **SubscriptionClient** могут работать в двух разных режимах: **ReceiveAndDelete** и **PeekLock**.

При использовании режима **ReceiveAndDelete** получение является одиночной операцией, т.е., когда Service Bus получает запрос на чтение для сообщения в подписке, он помечает сообщение как израсходованное и возвращает его приложению. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus отметит сообщение как израсходованное, перезапущенное приложение, начав снова расходовать сообщения, пропустит сообщение, израсходованное до аварийного завершения работы.

В режиме **PeekLock** (установленном по умолчанию) процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не допускающие пропуск сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), оно завершает второй этап процесса получения, вызывая метод **Complete** для обработанного сообщения. Когда Service Bus видит вызов **Complete**, он помечает сообщение, как расходуемое и удаляет его из подписки.

В следующем примере показано, как получать и обрабатывать сообщения с помощью режима по умолчанию **PeekLock**. Чтобы задать другое значение **ReceiveMode**, можно использовать другую перегрузку метода **CreateFromConnectionString**. В этом примере создается бесконечный цикл и сообщения обрабатываются, как только они поступают в подписку "HighMessages". Внимание: путь к подписке HighMessages предоставляется в виде "<*путь к разделу*\>/subscriptions/<*имя подписки*\>".

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

<h2>Обработка сбоев приложения и нечитаемых сообщений</h2>

 Служебная шина поддерживает функции, помогающие надлежащим образом восстановиться после ошибок в приложении или трудностей, возникших при обработке сообщений. Если принимающее приложение по каким-то причинам не может обработать сообщение, оно вызывает применительно к нему метод **Abandon** (а не метод **Complete**). В этом случае Service Bus открывает доступ к сообщению в пределах подписки и делает его доступным для повторного получения, либо тем же самым расходующим сообщение приложением, либо другим расходующим его приложением.

Существует также определенное связанное с сообщением время ожидания в пределах подписки, и если приложение не сможет обработать сообщение до истечения этого времени (например, аварийно завершит работу), то Service Bus автоматически разблокирует сообщение и сделает его доступным для повторного получения.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **Complete**, это сообщение будет повторно доставлено в приложение после его перезапуска. Такой процесс часто называется **минимум однократной обработкой**, т.е. каждое сообщение будет обработано как минимум один раз, но при определенных условиях одно и то же сообщение может быть	 доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

<h2>Удаление разделов и подписок</h2>

В приведенном ниже примере показано, как удалить раздел с именем
**TestTopic** из пространства имен службы **HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

При удалении раздела также удаляются все подписки, которые зарегистрированы в разделе. Подписки также можно удалять по отдельности. В следующем коде демонстрируется удаление подписки по имени **HighMessages** из раздела **TestTopic**:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

<h2>Дальнейшие действия</h2>

Вы узнали основные сведения о разделах и подписках Service Bus. Для получения дополнительных сведений используйте следующие ссылки.

-   Справочник MSDN: [Очереди, разделы и подписки][].
-     Справочник по API для [SqlFilter][].
-   Создать рабочее приложение, которое отправляет и получает сообщения в
    из очереди шины: [Учебник по управляемому обмену сообщениями Service Bus
    в .NET][].

  [Дальнейшие действия]: #nextsteps
  [Что такое разделы и подписки Service Bus]: #what-is
  [Создание пространства имен службы]: #create-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-creds
  [Настройка приложения для использования шины обслуживания]: #configure-app
  [Практическое руководство. Настройка строки подключения для служебной шины]: #set-up-connstring
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
  [Учебник по управляемому обмену сообщениями Service Bus в .NET]: http://msdn.microsoft.com/ru-ru/library/hh367512.aspx
