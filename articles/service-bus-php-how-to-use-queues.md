<properties linkid="develop-php-how-to-guides-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (PHP) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues PHP" description="Learn how to use Service Bus queues in Azure. Code samples written in PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Queues" authors="" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Использование очередей Service Bus

В этом руководстве показано, как использовать очереди Service Bus с PHP. Примеры
написаны на PHP и используют [Azure SDK for PHP][Azure SDK for PHP]. Здесь
описаны такие сценарии, как **создание очередей**, **отправка и получение
сообщений**, а также **удаление очередей**.

## Оглавление

-   [Что такое очереди Service Bus?][Что такое очереди Service Bus?]
-   [Создание пространства имен службы][Создание пространства имен службы]
-   [Получение учетных данных управления по умолчанию для пространства имен][Получение учетных данных управления по умолчанию для пространства имен]
-   [Создание приложения PHP][Создание приложения PHP]
-   [Получение клиентских библиотек Azure][Получение клиентских библиотек Azure]
-   [Настройка приложения для использования Service Bus][Настройка приложения для использования Service Bus]
-   [Практическое руководство. Создание очереди][Практическое руководство. Создание очереди]
-   [Практическое руководство. Отправка сообщений в очередь][Практическое руководство. Отправка сообщений в очередь]
-   [Практическое руководство. Получение сообщений из очереди][Практическое руководство. Получение сообщений из очереди]
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений][Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <span id="CreateApplication"></span></a>Создание приложения на PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в [пакете Azure SDK для PHP][Azure SDK for PHP] непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

> [WACOM.NOTE]
> Для установки PHP должно быть установлено и включено [расширение OpenSSL][расширение OpenSSL].

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

## <span id="GetClientLibrary"></span></a>Получение клиентских библиотек Azure

[WACOM.INCLUDE [получение-клиент-библиотеки](../includes/get-client-libraries.md)]

## <span id="ConfigureApp"></span></a>Настройка приложения для использования Service Bus

Чтобы использовать API очередей Azure Servise Bus, необходимо следующее:

1.  Ссылка на файл автозагрузчика с использованием инструкции [require\_once][require\_once] и
2.  Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

> [WACOM.NOTE]
> При работе с этим примером (и другими примерами в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure через Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо сослаться на файл автозагрузчика `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

В приведенных ниже примерах инструкция `require_once` всегда будет отображаться, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

## <span id="ConnectionString"></span></a>Настройка подключения к Azure Service Bus

Для создания клиента Azure Service Bus необходимо сначала сформировать правильную строку подключения в следующем формате:

    Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Конечная точка обычно имеет формат `https://[yourNamespace].servicebus.windows.net`.

Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

-   передать строку подключения напрямую или
-   использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:

    -   по умолчанию предоставляется поддержка одного внешнего источника – переменных среды
    -   можно добавить новые источники, расширив класс **ConnectionStringSource**

В приведенных здесь примерах строка подключения передается напрямую.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

## <span id="CreateQueue"></span></a>Практическое руководство. Создание очереди

Операции управления для очередей Service Bus можно выполнять с помощью класса
**ServiceBusRestProxy**. Объект **ServiceBusRestProxy** создается
с помощью встроенного производителем метода **ServicesBuilder::createServiceBusService** с соответствующей строкой подключения, которая включает в себя разрешения маркеров для управления им.

В приведенном ниже примере показано, как создать экземпляр **ServiceBusRestProxy** и вызвать метод **ServiceBusRestProxy-\>createQueue** для создания очереди с именем `myqueue` в пространстве имен службы `MySBNamespace`:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\QueueInfo;

    // Create Service Bus REST proxy.
        $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {
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
> Вы можете использовать метод **listQueues** для объектов **ServiceBusRestProxy** чтобы проверить, существует ли уже очередь с указанным именем в пространстве имен служб.

## <span id="SendMessages"></span></a>Практическое руководство. Отправка сообщений в очередь

Чтобы отправить сообщение в очередь Service Bus, в приложении вызывается метод **ServiceBusRestProxy-\>sendQueueMessage**. В следующем примере кода показано, как отправить сообщение в очередь `myqueue`, созданную ранее в пространстве имен службы
`MySBNamespace`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\models\BrokeredMessage;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
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

Сообщения, отправляемые в очереди Service Bus и получаемые из них — это экземпляры
класса **BrokeredMessage**. Объекты **BrokeredMessage** имеют набор
стандартных методов (например, **getLabel**, **getTimeToLive**,
**setLabel** и **setTimeToLive**) и свойств, используемых для хранения
настраиваемых свойств приложения, и тело, состоящее из
данных приложения.

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ (максимальный размер
заголовка, который содержит стандартные и настраиваемые свойства
приложения, — 64 КБ). Ограничения на количество сообщений
в очереди нет, но есть максимальный общий размер сообщений
, содержащихся в очереди. Максимальный размер очереди ограничен 5 ГБ.

## <span id="ReceiveMessages"></span></a>Практическое руководство. Получение сообщений из очереди

Основным способом получения сообщений из очереди является метод **ServiceBusRestProxy-\>receiveQueueMessage**. Сообщения можно получать в двух различных режимах: **ReceiveAndDelete** (по умолчанию) и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда служебная шина получает запрос на чтение для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. **Режим ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает для сценариев, где приложение может не обрабатывать
сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы другие получатели не могли его принять, а затем возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), шина Service Bus завершает второй этап процесса получения, передавая полученное сообщение в метод **ServiceBusRestProxy-\>deleteMessage**. Когда Service Bus обнаруживает вызов **deleteMessage**, сообщение помечается как используемое и удаляется из очереди.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не режим по умолчанию).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
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

## <span id="HandleCrashes"></span></a>Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений

Служебная шина предоставляет функции, помогающие корректно исправить
ошибки в приложении или устранить проблемы с обработкой сообщения. Если
по каким-либо причинам не может обработать сообщение,
то оно может вызвать для полученного сообщения метод **unlockMessage**
(вместо метода **deleteMessage**). После этого Service Bus
разблокирует сообщение в очереди и сделает его
доступным для приема тем же приложением или другим
приложением.

Кроме того, с сообщением, заблокированным в
очереди, связано время ожидания. Если приложение не сможет обработать сообщение
в течение времени ожидания (например, при сбое приложения), то Service
Bus разблокирует сообщение автоматически и сделает его доступным для
приема.

Если сбой приложения происходит после обработки сообщения,
но перед отправкой запроса **deleteMessage** это сообщение
будет повторно доставлено в приложение после его перезапуска. Часто этот подход
называют **обработать хотя бы один раз**, т. е. каждое сообщение будет
обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть
доставлено повторно. Если повторная обработка недопустима,
рекомендуется добавить в приложение дополнительную логику для обработки повторной доставки сообщений. Часто это достигается
с помощью метода **getMessageId** сообщения, которое остается
постоянным для различных попыток доставки.

## <span id="NextSteps"></span></a>Дальнейшие действия

Вы познакомились с основами использования очередей Service Bus.
Дополнительные сведения см. в статье [Очереди, разделы и подписки][Очереди, разделы и подписки] на портале MSDN.

  [Azure SDK for PHP]: http://go.microsoft.com/fwlink/?LinkId=252473
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Создание приложения PHP]: #CreateApplication
  [Получение клиентских библиотек Azure]: #GetClientLibrary
  [Настройка приложения для использования Service Bus]: #ConfigureApp
  [Практическое руководство. Создание очереди]: #CreateQueue
  [Практическое руководство. Отправка сообщений в очередь]: #SendMessages
  [Практическое руководство. Получение сообщений из очереди]: #ReceiveMessages
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #HandleCrashes
  [Дальнейшие действия]: #NextSteps
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [расширение OpenSSL]: http://php.net/openssl
  [получение-клиент-библиотеки]: ../includes/get-client-libraries.md
  [Очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367516.aspx
