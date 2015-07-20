<properties 
	pageTitle="Использование разделов служебной шины (PHP) в Azure" 
	description="Узнайте, как использовать разделы служебной шины с помощью PHP в Azure." 
	services="service-bus" 
	documentationCenter="php" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="sethm"/>


# Использование разделов и подписок служебной шины

Из этого руководства вы узнаете, как использовать разделы и подписки служебной шины. Примеры написаны на PHP и используют [пакет Azure SDK для PHP](../php-download-sdk.md). В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений в раздел**, **получение сообщений из подписки** и **удаление разделов и подписок**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Создание приложения PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в [пакете Azure SDK для PHP](../php-download-sdk.md) непосредственно из кода. Для создания приложения можно использовать любые средства разработки или Блокнот.

> [AZURE.NOTE]Для установки PHP должно быть установлено и включено [расширение OpenSSL](http://php.net/openssl).

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

## Получение клиентских библиотек Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Настройка приложения для использования служебной шины

Чтобы использовать интерфейсы API служебной шины, требуется следующее.

1. Ссылка на файл автозагрузчика с использованием оператора [require_once][require-once].
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServiceBusService**.

> [AZURE.NOTE]В этом примере (и других примерах в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure с помощью Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо использовать ссылку на файл автозагрузчика **WindowsAzure.php**.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;

В приведенных ниже примерах всегда будет отображаться оператор `require_once`, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

## Настройка подключения к Service Bus

Для создания клиента служебной шины Azure необходимо сначала сформировать правильную строку подключения в следующем формате:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

где **конечная точка** обычно имеет формат `https://[yourNamespace].servicebus.windows.net`.

Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

* Передать строку подключения напрямую или
* использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:
	* По умолчанию предоставляется поддержка одного внешнего источника — переменных среды.
	* Можно добавить новые источники, расширив класс **ConnectionStringSource**.

В приведенных здесь примерах строка подключения передается напрямую.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	
	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

## Практическое руководство. Создание раздела

Операции управления для разделов служебной шины можно выполнять с помощью класса **ServiceBusRestProxy**. Объект **ServiceBusRestProxy** создается с помощью фабричного метода **ServicesBuilder::createServiceBusService** с соответствующей строкой подключения, которая включает в себя разрешения маркеров для управления им.

В приведенном ниже примере показано, как создать экземпляр **ServiceBusRestProxy** и вызвать метод **ServiceBusRestProxy->createTopic** для создания раздела с именем `mytopic` в пространстве имен службы `MySBNamespace`.

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
		// http://msdn.microsoft.com/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [AZURE.NOTE]Можно использовать метод `listTopics` для объектов `ServiceBusRestProxy`, чтобы проверить, существует ли уже раздел с указанным именем в пространстве имен службы.

## Практическое руководство. Создание подписки

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
		// http://msdn.microsoft.com/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

### Создание подписок с фильтрами

Можно также настраивать фильтры, позволяющие определять, какие посылаемые в раздел сообщения должны появляться в рамках конкретной подписки. Самый гибкий тип фильтра, который поддерживается подписками, — это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о фильтрах SqlFilter см. в описании [свойства SqlFilter.SqlExpression][sqlfilter].

> [AZURE.NOTE]Каждое правило в подписке обрабатывает входящие сообщения независимо, добавляя к подписке свои результирующие сообщения. Кроме того, каждая новая подписка имеет объект **Правило** по умолчанию с фильтром, которое добавляет все сообщения из раздела в подписку. Чтобы получать только сообщения, соответствующие условиям фильтра, необходимо удалить это правило по умолчанию. Правило по умолчанию можно удалить с помощью метода `ServiceBusRestProxy->deleteRule`.

В приведенном ниже примере создается подписка с именем **HighMessages** с фильтром **SqlFilter**, который выбирает только сообщения, у которых значение настраиваемого свойства **MessageNumber** больше 3 (см. раздел [Практическое руководство. Отправка сообщений в раздел](#SendMessage) для получения сведений о добавлении настраиваемых свойств в сообщения):

	$subscriptionInfo = new SubscriptionInfo("HighMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

  	$ruleInfo = new RuleInfo("HighMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber > 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Обратите внимание, что приведенный выше код требует использования дополнительного пространства имен: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Аналогичным образом в следующем примере создается подписка с именем **LowMessages** и фильтром **SqlFilter**, который выбирает только сообщения, у которых значение свойства **MessageNumber** меньше или равно 3:

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

Теперь, если сообщение отправляется в раздел `mytopic`, оно всегда будет доставляться получателям, подписанным на подписку раздела `mysubscription`, и выборочно доставляться получателям, подписанным на подписки разделов `HighMessages` и `LowMessages` (в зависимости от содержания сообщения).

## Практическое руководство. Отправка сообщений в раздел

Чтобы отправить сообщение в раздел служебной шины, в приложении вызывается метод **ServiceBusRestProxy->sendTopicMessage**. В следующем примере кода показано, как отправить сообщение в раздел `mytopic`, созданный ранее в пространстве имен службы `MySBNamespace`.

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
		// http://msdn.microsoft.com/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Сообщения, отправляемые в разделы Service Bus и получаемые из них, — это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств и методов (например, **getLabel**, **getTimeToLive**, **setLabel** и **setTimeToLive**), а также свойства, которые можно использовать для хранения настраиваемых свойств приложения. В следующем примере показано, как отправить 5 тестовых сообщений в созданный ранее раздел `mytopic`. Метод **setProperty** используется для добавления настраиваемого свойства (`MessageNumber`) в каждое сообщение. Обратите внимание, что значение свойства `MessageNumber` меняется в каждом сообщении (с помощью этого можно определить, какие подписки получают его, как показано в разделе [Практическое руководство. Создание подписки](#CreateSubscription)):

	for($i = 0; $i < 5; $i++){
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message ".$i);
			
		// Set custom property.
		$message->setProperty("MessageNumber", $i);
			
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Максимальный размер очереди ограничен 5 ГБ.

## Практическое руководство. Получение сообщений от подписки

Основным способом получения сообщений из подписки является метод **ServiceBusRestProxy->receiveSubscriptionMessage**. Полученные сообщения могут работать в двух режимах: **ReceiveAndDelete** (по умолчанию) и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда Service Bus получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), шина Service Bus завершает второй этап процесса получения, передавая полученное сообщение в метод **ServiceBusRestProxy->deleteMessage**. Когда Service Bus обнаруживает вызов **deleteMessage**, сообщение помечается как используемое и удаляется из очереди.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не является режимом по умолчанию).

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
		// http://msdn.microsoft.com/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений

служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **Обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## Как удалять разделы и подписки

Чтобы удалить раздел или подписку, используйте метод **ServiceBusRestProxy->deleteTopic** или **ServiceBusRestProxy->deleteSubscripton** соответственно. Обратите внимание, что при удалении раздела также удаляются все подписки, зарегистрированные в этом разделе.

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
		// http://msdn.microsoft.com/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

С помощью метода **deleteSubscription** можно удалить подписку независимо:

	$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

## Дальнейшие действия

Вы познакомились с основами использования очередей служебной шины. Дополнительную информацию см. в статье [Очереди, разделы и подписки][] на портале MSDN.

[What are Service Bus Topics and Subscriptions?]: #bkmk_WhatAreSvcBusTopics
[Create a Service Namespace]: #bkmk_CreateSvcNamespace
[Obtain the Default Management Credentials for the Namespace]: #bkmk_ObtainDefaultMngmntCredentials
[Configure Your Application to Use Service Bus]: #bkmk_ConfigYourApp
[How to: Create a Topic]: #bkmk_HowToCreateTopic
[How to: Create Subscriptions]: #bkmk_HowToCreateSubscrip
[How to: Send Messages to a Topic]: #bkmk_HowToSendMsgs
[How to: Receive Messages from a Subscription]: #bkmk_HowToReceiveMsgs
[How to: Handle Application Crashes and Unreadable Messages]: #bkmk_HowToHandleAppCrash
[How to: Delete Topics and Subscriptions]: #bkmk_HowToDeleteTopics
[Next Steps]: #bkmk_NextSteps
[Service Bus Topics diagram]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
[Azure Management Portal]: http://manage.windowsazure.com/
[Service Bus Node screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
[Create a New Namespace screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
[Namespace List screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
[Properties Pane screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
[Default Key screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
[Очереди, разделы и подписки]: http://msdn.microsoft.com/library/azure/hh367516.aspx
[Available Namespaces screenshot]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
 

<!---HONumber=July15_HO2-->