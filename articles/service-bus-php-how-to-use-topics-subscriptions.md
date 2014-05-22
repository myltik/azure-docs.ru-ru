<properties linkid="develop-php-how-to-guides-service-bus-topics" urlDisplayName="Разделы Service Bus" pageTitle="Использование разделов Service Bus (PHP) — Azure" metaKeywords="" description="Узнайте, как использовать разделы Service Bus с PHP в Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="Использование разделов и подписок Service Bus" authors="" solutions="" manager="" editor="" />


# Использование разделов и подписок Service Bus

В этом руководстве показывается, как использовать разделы и
подписки Service Bus. Примеры написаны на PHP и используют [пакет Azure SDK для PHP][download-sdk]. В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений в раздел**, **получение сообщений из подписки** и **удаление разделов и подписок**.

## Оглавление

-   [Что такое разделы и подписки Service Bus?](#what-are-service-bus-topics)
-   [Создание пространства имен службы](#create-a-service-namespace)
-   [Получение учетных данных управления по умолчанию для пространства имен](#obtain-default-credentials)
- 	[Создание приложения PHP](#CreateApplication)
-	[Получение клиентских библиотек Azure](#GetClientLibrary)
-   [Настройка приложения для использования шины обслуживания](#ConfigureApp)
-   [Практическое руководство. Создание раздела](#CreateTopic)
-   [Практическое руководство. Создание подписки](#CreateSubscription)
-   [Практическое руководство. Отправка сообщений в раздел](#SendMessage)
-   [Практическое руководство. Получение сообщений от подписки](#ReceiveMessages)
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений](#HandleCrashes)
-   [Практическое руководство. Удаление разделов и подписок](#DeleteTopicsAndSubscriptions)
-   [Дальнейшие действия](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a id="CreateApplication"></a>Создание приложения на PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в [пакете Azure SDK для PHP][download-sdk] непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

> [WACOM.NOTE]
> Для установки PHP должно быть установлено и включено <a href="http://php.net/openssl">расширение OpenSSL</a>.

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

##<a id="GetClientLibrary"></a>Получение клиентских библиотек Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Настройка приложения для использования Service Bus

Чтобы использовать API разделов Azure Servise Bus, необходимо следующее:

1. Ссылка на файл автозагрузчика с использованием инструкции [require_once][require-once].
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServiceBusService**.

	> [WACOM.NOTE]
	> При работе с этим примером (и другими примерами в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure через Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо сослаться на файл автозагрузчика <code>WindowsAzure.php</code>.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


В приведенных ниже примерах инструкция `require_once` всегда будет отображаться, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

##<a id="ConnectionString"></a>Настройка подключения к Azure Service Bus

Для создания клиента Azure Service Bus необходимо сначала сформировать правильную строку подключения в следующем формате:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Конечная точка обычно имеет формат `https://[пространство_имен].servicebus.windows.net`.

Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

* передать строку подключения напрямую или
* использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:
	* по умолчанию предоставляется поддержка одного внешнего источника - переменных среды
	* можно добавить новые источники, расширив класс **ConnectionStringSource**

В приведенных здесь примерах строка подключения передается напрямую.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	
	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

##<a id="CreateTopic"></a>Практическое руководство. Создание раздела

Операции управления для разделов Service Bus можно выполнять с помощью
класса **ServiceBusRestProxy**. Объект **ServiceBusRestProxy** создается
с помощью встроенного производителем метода **ServicesBuilder::createServiceBusService** с соответствующей строкой подключения, которая включает в себя разрешения маркеров для управления им.

В приведенном ниже примере показано, как создать экземпляр **ServiceBusRestProxy** и вызвать метод **ServiceBusRestProxy->createTopic** для создания раздела с именем `mytopic` в пространстве имен службы `MySBNamespace`:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\TopicInfo;
	
	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Create topic.
		$topicInfo = new TopicInfo("mytopic");
		$serviceBusRestProxy->createTopic($topicInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

	> [WACOM.NOTE]
	> Можно использовать метод <b>listTopics</b> для объектов <b>ServiceBusRestProxy</b>, чтобы проверить, существует ли уже раздел с указанным именем в пространстве имен служб.

##<a id="CreateSubscription"></a>Практическое руководство. Создание подписки

Подписки на разделы также создаются с помощью метода **ServiceBusRestProxy->createSubscription**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем "mysubscription", и используется фильтр по умолчанию **MatchAll**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		// Create subscription.
		$subscriptionInfo = new SubscriptionInfo("mysubscription");
		$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

### Создание подписок с фильтрами

Вы также можете настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела. Самый гибкий тип фильтра, который поддерживается подписками, — это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о фильтрах SqlFilter см. в описании [свойства SqlFilter.SqlExpression][sqlfilter].

	> [WACOM.NOTE]
	> Каждое правило в подписке обрабатывает входящие сообщения независимо друг от друга, добавляя итоговые сообщения в подписку. Кроме того, каждая новая подписка имеет <b>Правило</b> по умолчанию с фильтром, которое добавляет все сообщения из раздела подписку. Чтобы получать только сообщения, соответствующие условиям фильтра, необходимо удалить это правило по умолчанию. Правило по умолчанию можно удалить с помощью метода <b>ServiceBusRestProxy->deleteRule</b>.

В приведенном ниже примере создается подписка с именем "HighMessages" с фильтром **SqlFilter**, который выбирает только сообщения, у которых значение настраиваемого свойства **MessageNumber** больше 3 (см. раздел [Практическое руководство. Отправка сообщений в раздел](#SendMessage) для получения сведений о добавлении настраиваемых свойств в сообщения):

	$subscriptionInfo = new SubscriptionInfo("HighMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

  	$ruleInfo = new RuleInfo("HighMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber > 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Обратите внимание, что приведенный выше код требует использования дополнительного пространства имен: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Аналогично, следующий пример создает подписку с именем "LowMessages" и фильтром SqlFilter, который выбирает только сообщения, у которых значение свойства MessageNumber меньше или равно 3:

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

Теперь, если сообщение будет отправлено в раздел `mytopic`, оно всегда будет доставляться получателям, подписанным на подписку `mysubscription`, и выборочно доставляться получателям, подписанным на подписки "HighMessages" и "LowMessages" (в зависимости от содержимого сообщений).

##<a id="SendMessage"></a>Практическое руководство. Отправка сообщений в раздел

Чтобы отправить сообщение в раздел Service Bus, в приложении вызывается метод **ServiceBusRestProxy->sendTopicMessage**. В следующем примере кода показано, как отправить сообщение в раздел `mytopic`, созданный ранее в
пространстве имен службы `MySBNamespace`.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ru-ru/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Сообщения, отправляемые в разделы Service Bus и получаемые из них, — это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств и методов (например, **getLabel**, **getTimeToLive**, **setLabel** и **setTimeToLive**), а также свойства, которые можно использовать для хранения настраиваемых свойств приложения. В следующем примере показано, как отправить 5 тестовых сообщений в созданный ранее раздел `mytopic`. Метод **SetProperty** используется для добавления настраиваемого свойства (`MessageNumber`) в каждое сообщение. Обратите внимание, как значение свойства `MessageNumber` меняется для каждого сообщения (с помощью этого можно определить, какие подписки получают его, как показано в предыдущем разделе [Практическое руководство. Создание подписки](#CreateSubscription)):

	for($i = 0; $i < 5; $i++){
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message ".$i);
			
		// Set custom property.
		$message->setProperty("MessageNumber", $i);
			
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}

Очереди Service Bus поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, составляет 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Максимальный размер очереди ограничен 5 ГБ.

##<a id="ReceiveMessages"></a>Практическое руководство. Получение сообщений от подписки

Основным способом получения сообщений из подписки является метод **ServiceBusRestProxy->receiveSubscriptionMessage**. Полученные сообщения могут работать в двух режимах: **ReceiveAndDelete** (по умолчанию) и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, - то есть, когда Service Bus получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая
лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), шина Service Bus завершает второй этап процесса получения, передавая полученное сообщение в метод **ServiceBusRestProxy->deleteMessage**. Когда Service Bus обнаруживает вызов **deleteMessage**, сообщение помечается как используемое и удаляется из очереди.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не режим по умолчанию). 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set receive mode to PeekLock (default is ReceiveAndDelete)
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
	
		// Get message.
		$message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", 
																	"mysubscription", 
																	$options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Deleting message...<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/ru-ru/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="HandleCrashes"></a>Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений

Service Bus предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого Service Bus разблокирует сообщение в очереди и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

##<a id="DeleteTopicsAndSubscriptions"></a>Удаление разделов и подписок

Чтобы удалить раздел или подписку, используйте метод **ServiceBusRestProxy -> deleteTopic** или **ServiceBusRestProxy -> deleteSubscripton** соответственно. Обратите внимание, что при удалении раздела также удаляются все подписки, зарегистрированные в этом разделе.

Следующий пример показывает, как удалить раздел (`mytopic`) и его зарегистрированные подписки.

    require_once 'vendor\autoload.php';

	use WindowsAzure\ServiceBus\ServiceBusService;
	use WindowsAzure\ServiceBus\ServiceBusSettings;
	use WindowsAzure\Common\ServiceException;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Delete topic.
		$serviceBusRestProxy->deleteTopic("mytopic");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

С помощью метода **deleteSubscription** можно удалить подписку независимо:

	$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

##<a id="NextSteps"></a>Дальнейшие действия

Вы познакомились с основами использования очередей Service Bus. Дополнительные сведения см. в статье [Очереди, разделы и подписки][] на портале MSDN.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[Что такое разделы и подписки Service Bus?]: #bkmk_WhatAreSvcBusTopics
[Создание пространства имен службы]: #bkmk_CreateSvcNamespace
[Получение учетных данных управления по умолчанию для пространства имен]: #bkmk_ObtainDefaultMngmntCredentials
[Настройка приложения для использования шины обслуживания]: #bkmk_ConfigYourApp
[Практическое руководство. Создание раздела]: #bkmk_HowToCreateTopic
[Практическое руководство. Создание подписок]: #bkmk_HowToCreateSubscrip
[Практическое руководство. Отправка сообщений в раздел]: #bkmk_HowToSendMsgs
[Практическое руководство. Получение сообщений от подписки]: #bkmk_HowToReceiveMsgs
[Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #bkmk_HowToHandleAppCrash
[Практическое руководство. Удаление разделов и подписок]: #bkmk_HowToDeleteTopics
[Дальнейшие действия]: #bkmk_NextSteps
[Схема разделов Service Bus]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
[Портал управления Azure]: http://manage.windowsazure.com/
[Service Bus Topics diagram]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
[Azure Management Portal]: http://manage.windowsazure.com/
[Service Bus Node screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
[Create a New Namespace screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
[Namespace List screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
[Properties Pane screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
[Default Key screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
[Queues, Topics, and Subscriptions]: http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx
[Available Namespaces screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[sqlfilter]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

[require-once]: http://php.net/require_once

