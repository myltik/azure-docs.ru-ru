<properties urlDisplayName="Service Bus Topics" pageTitle="Как использовать разделы служебной шины (PHP). Azure" metaKeywords="" description="Узнайте, как использовать разделы Service Bus с PHP в Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/13/2014" ms.author="sethm" />


# Использование разделов и подписок Service Bus

Из этого руководства вы узнаете, как использовать разделы и подписки служебной шины. Примеры написаны на PHP и используют [пакет Azure SDK для PHP][download-sdk]. В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений в раздел**, **получение сообщений из подписки** и **удаление тем и подписок**.

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

##<a id="CreateApplication"></a>Создание приложения PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в [пакете Azure SDK для PHP][download-sdk] непосредственно из кода. Можно использовать любые средства разработки для создания приложения, в том числе Блокнот.

> [WACOM.NOTE]
> Для установки PHP должно быть установлено и включено <a href="http://php.net/openssl">расширение OpenSSL</a>.

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

##<a id="GetClientLibrary"></a>Получение клиентских библиотек Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Настройка приложения для использования Service Bus

Чтобы использовать API разделов Azure Servise Bus, необходимо следующее:

1. Ссылка на файл автозагрузчика с использованием инструкции [require_once][require-once] и
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано включение файла автозагрузчика создание ссылки на класс **ServiceBusService**.

	> [WACOM.NOTE]
	> В этом примере (и других примерах этой статьи) предполагается, что у вас установлены клиентские библиотеки PHP для Azure с помощью Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо сослаться на файл автозагрузчика <code>WindowsAzure.php</code>.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


В приведенных ниже примерах инструкция require_once всегда будет отображаться, однако ссылки будут приводиться только на классы, необходимые для выполнения этого примера.

##<a id="ConnectionString"></a>Настройка подключения к служебной шине Azure

Для создания клиента Azure Service Bus необходимо сначала сформировать правильную строку подключения в следующем формате:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Конечная точка обычно имеет формат "https://[пространство_имен].servicebus.windows.net".

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
класса **ServiceBusRestProxy**. Объект **ServiceBusRestProxy**
создается через фабричный метод **ServicesBuilder::createServiceBusService** с соответствующей строкой подключения, инкапсулирующей в себе разрешения токена на управление им.

В примере ниже показано создание экземпляра **ServiceBusRestProxy** и вызов **ServiceBusRestProxy->createTopic** для создания раздела по имени mytopic в пространстве имен MySBNamespace:

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
	> You can use the <b>listTopics</b> method on Объекты <b>ServiceBusRestProxy</b> для проверки существования в пространстве имен службы раздела с заданным именем.

##<a id="CreateSubscription"></a>Практическое руководство. Создание подписки

Подписки раздела также создаются методом **ServiceBusRestProxy->createSubscription**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** используется по умолчанию, если при создании подписки не указать фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка по имени mysubscription, где используется стандартный фильтр **MatchAll**.

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

Вы также можете настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела. Самый гибкий тип фильтра, который поддерживается подписками, - это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Более подробную информацию об SqlFilters см. в разделе [Свойство SqlFilter.SqlExpression][sqlfilter].

	> [WACOM.NOTE]
	> Каждое правило в подписке обрабатывает входящие сообщения независимо, добавляя к подписке свои результирующие сообщения. Кроме того, каждая новая подписка имеет <b>Правило</b> по умолчанию с фильтром, которое добавляет все сообщения из раздела подписку. Чтобы получать только сообщения, соответствующие условиям фильтра, необходимо удалить это правило по умолчанию. Можно удалить стандартное правило, используя метод <b>ServiceBusRestProxy->deleteRule</b>.

В следующем примере создается подписка по имени HighMessages с фильтром **SqlFilter**, выбирающим только сообщения, пользовательское свойство **MessageNumber** в которых больше 3: (информацию о добавлении в сообщения пользовательских свойство см. в разделе [ Практическое руководство. Отправка сообщений в раздел](#SendMessage)):

	$subscriptionInfo = new SubscriptionInfo("HighMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

  	$ruleInfo = new RuleInfo("HighMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber > 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Note that the code above requires the use of an additional namespace: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Similarly, the following example creates a subscription named "LowMessages" with a SqlFilter that only selects messages that have a MessageNumber property less than or equal to 3:

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

Теперь, когда в раздел mytopic отправляется сообщение, оно будет всегда доставляться получателям, подписанным на подписку раздела mysubscription и выборочно доставляться получателям, подписанным на подписки разделов HighMessages и LowMessages в зависимости от содержания сообщения.

##<a id="SendMessage"></a>Практическое руководство. Отправка сообщений в раздел

Чтобы отправить сообщение в раздел служебной шины, ваше приложение вызовет метод **ServiceBusRestProxy->sendTopicMessage**. Приведенный ниже код демонстрирует отправку сообщения в раздел mytopic, созданный нами выше в
Пространство имен службы MySBNamespace.

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

Сообщения, отправляемые в разделы Service Bus и получаемые из них, - это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств и методов (например, **getLabel**, **getTimeToLive**, **setLabel** и **setTimeToLive**), а также свойства, которые можно использовать для хранения настраиваемых свойств приложения. В следующем примере продемонстрирована отправка пяти тестовых сообщений в раздел mytopic, созданный нами ранее. Метод **setProperty** используется для добавления к каждому сообщению пользовательского свойства (MessageNumber). Обратите внимание, значение свойства MessageNumber меняется в каждом сообщении (это можно использовать, чтобы определить, какие подписки получат сообщение, как показано в разделе [Практическое руководство. Создание подписки](#CreateSubscription) выше):

	for($i = 0; $i < 5; $i++){
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message ".$i);
			
		// Set custom property.
		$message->setProperty("MessageNumber", $i);
			
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}

очереди служебной шины поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, - 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Максимальный размер очереди ограничен 5 ГБ.

##<a id="ReceiveMessages"></a>Практическое руководство. Получение сообщений из подписки

Самый простой способ получать сообщения из подписки - использование метода **ServiceBusRestProxy->receiveSubscriptionMessage**. Полученные сообщения могут работать в двух различных режимах: **ReceiveAndDelete** (по умолчанию) и **PeekLock**.

При использовании режима **ReceiveAndDelete** получение является одиночной операцией, т.е., когда служебная шина получает запрос на чтение для сообщения в подписке, он помечает сообщение как израсходованное и возвращает его приложению. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку шина обслуживания помечает сообщение как используемое, то после того как приложение перезапускается и снова начинает обрабатывать сообщения, оно пропустит сообщение, которое использовалось до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), шина Service Bus завершает второй этап процесса получения, передавая полученное сообщение в метод **ServiceBusRestProxy->deleteMessage**. Когда служебная шина обнаруживает вызов **deleteMessage**, сообщение помечается как используемое и удаляется из очереди.

ВВ следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не является режимом по умолчанию). 

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

Служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage**, это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

##<a id="DeleteTopicsAndSubscriptions"></a>Удаление разделов и подписок

Чтобы удалить раздел или подписку, используйте соответственно методы **ServiceBusRestProxy->deleteTopic** или **ServiceBusRestProxy->deleteSubscripton**. Внимание: при удалении раздела будут также удалены все подписки, зарегистрированные в этом разделе.

В следующем примере показано удаление раздела (с именем mytopic) и зарегистрированных в нем подписок.

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

Используя метод **deleteSubscription**, можно удалить подписку независимым образом:

	$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

##<a id="NextSteps"></a>Дальнейшие действия

Вы познакомились с основами использования очередей служебной шины. Дополнительные сведения см. в статье [Очереди, разделы и подписки][] на портале MSDN.

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
[Снимок экрана узла Service Bus]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
[Снимок экрана создания пространства имен]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
[Снимок экрана списка пространств имен]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
[Снимок экрана панели свойств]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
[Снимок экрана ключа по умолчанию]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
[очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367516.aspx
[Снимок экрана доступных пространств имен]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[sqlfilter]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

[require-once]: http://php.net/require_once
