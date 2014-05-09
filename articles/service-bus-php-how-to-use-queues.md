<properties linkid="develop-php-how-to-guides-service-bus-queues" urlDisplayName="Очереди Service Bus" pageTitle="Использование очередей Service Bus (PHP) — Azure" metaKeywords="очереди Service Bus Azure, очереди Azure, обмен сообщениями Azure, очереди Azure PHP" description="Сведения об использовании очередей Service Bus в Azure. Примеры кода написаны на PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="Использование очередей Service Bus" authors="" solutions="" manager="" editor="" />

# Использование очередей Service Bus

В этом руководстве показано, как использовать очереди Service Bus с PHP. Примеры написаны
на PHP и используют [пакет Azure SDK для PHP][download-sdk]. Здесь описаны такие сценарии, как **создание очередей**, **отправка и получение сообщений**, а также
**удаление очередей**.

## Оглавление

-   [Что такое очереди Service Bus?](#what-are-service-bus-queues)
-   [Создание пространства имен службы](#create-a-service-namespace)
-   [Получение учетных данных управления по умолчанию для пространства имен](#obtain-default-credentials)
- 	[Создание приложения PHP](#CreateApplication)
-	[Получение клиентских библиотек Azure](#GetClientLibrary)
-   [Настройка приложения для использования Service Bus](#ConfigureApp)
-   [Практическое руководство. Создание очереди](#CreateQueue)
-   [Практическое руководство. Отправка сообщений в очередь](#SendMessages)
-   [Практическое руководство. Получение сообщений из очереди](#ReceiveMessages)
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений](#HandleCrashes)
-   [Следующие шаги](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

##<a id="CreateApplication"></a>Создание приложения на PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в [пакете Azure SDK для PHP][download-sdk] непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

> [WACOM.NOTE]
> Для установки PHP должно быть установлено и включено <a href="http://php.net/openssl">расширение OpenSSL</a>.

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

##<a id="GetClientLibrary"></a>Получение клиентских библиотек Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Настройка приложения для использования Service Bus

Чтобы использовать API очередей Azure Servise Bus, необходимо следующее:

1. Ссылка на файл автозагрузчика с использованием инструкции [require_once][require_once].
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

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
	* по умолчанию предоставляется поддержка одного внешнего источника – переменных среды
	* можно добавить новые источники, расширив класс **ConnectionStringSource**

В приведенных здесь примерах строка подключения передается напрямую.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);


##<a id="CreateQueue"></a>Создание очереди

Операции управления для очередей Service Bus можно выполнять с помощью
класса **ServiceBusRestProxy**. Объект **ServiceBusRestProxy** создается
с помощью встроенного производителем метода **ServicesBuilder::createServiceBusService** с соответствующей строкой подключения, которая включает в себя разрешения маркеров для управления им.

В приведенном ниже примере показано, как создать экземпляр **ServiceBusRestProxy** и вызвать метод **ServiceBusRestProxy->createQueue** для создания очереди с именем `myqueue` в пространстве имен службы `MySBNamespace`:

    require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\QueueInfo;

	// Create Service Bus REST proxy.
		$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		$queueInfo = new QueueInfo("myqueue");
		
		// Create queue.
		$serviceBusRestProxy->createQueue($queueInfo);
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
> Вы можете использовать метод <b>listQueues</b> для объектов <b>ServiceBusRestProxy</b>, чтобы проверить, существует ли уже очередь с указанным именем в пространстве имен служб.

##<a id="SendMessages"></a>Отправка сообщений в очередь

Чтобы отправить сообщение в очередь Service Bus, в приложении вызывается метод **ServiceBusRestProxy->sendQueueMessage**. В следующем примере кода показано, как отправить сообщение в очередь `myqueue`, созданную ранее в
пространстве имен службы `MySBNamespace`.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendQueueMessage("myqueue", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ru-ru/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Сообщения, отправляемые в очереди Service Bus и получаемые из них — это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных методов (например, **getLabel**, **getTimeToLive**,
**setLabel** и **setTimeToLive**), а также свойства, используемые для хранения
настраиваемых свойств приложения, и текст из произвольных
данных приложения.

Очереди Service Bus поддерживают максимальный размер сообщения 256 КБ (максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, составляет 64 КБ). Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Максимальный размер очереди ограничен 5 ГБ.

##<a id="ReceiveMessages"></a>Получение сообщений из очереди

Основным способом получения сообщений из очереди является метод **ServiceBusRestProxy->receiveQueueMessage**. Сообщения могут приниматься в двух режимах: **ReceiveAndDelete** (по умолчанию) и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, - то есть, когда Service Bus получает запрос на чтения для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая
лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы разобраться, рассмотрим сценарий, в котором приложение-получатель выдает запрос на получение, после чего происходит сбой этого приложения еще до обработки сообщения. Поскольку Service Bus помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы другие получатели не могли его принять, а затем возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), шина Service Bus завершает второй этап процесса получения, передавая полученное сообщение в метод **ServiceBusRestProxy->deleteMessage**. Когда Service Bus обнаруживает вызов **deleteMessage**, сообщение помечается как используемое и удаляется из очереди.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не режим по умолчанию).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set the receive mode to PeekLock (default is ReceiveAndDelete).
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
		
		// Receive message.
		$message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Message deleted.<br />";
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

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима,
рекомендуется добавить в приложение дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

##<a id="NextSteps"></a>Дальнейшие действия

Вы познакомились с основами использования очередей Service Bus. Дополнительные сведения см. в статье [Очереди, разделы и подписки][] на портале MSDN.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[Схема очереди Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
[Портал управления Azure]: http://manage.windowsazure.com/
[Снимок экрана узла Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
[Снимок экрана создания пространства имен]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[Снимок экрана доступных пространств имен]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[Снимок экрана списка пространств имен]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
[Снимок экрана панели свойств]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
[Снимок экрана ключа по умолчанию]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
[Очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367516.aspx
[require_once]: http://php.net/require_once


