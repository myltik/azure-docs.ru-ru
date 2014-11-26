<properties linkid="develop-php-how-to-guides-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (PHP) - Azure" metaKeywords="" description="Learn how to use Service Bus topics with PHP in Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Использование разделов и подписок Service Bus

В этом руководстве показывается,
как использовать разделы и подписки Service Bus. Примеры написаны на PHP и используют [пакет Azure SDK для PHP][пакет Azure SDK для PHP]. В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений в раздел**, **получение сообщений из подписки** и **удаление разделов и подписок**.

## Оглавление

-   [Что такое разделы и подписки Service Bus?][Что такое разделы и подписки Service Bus?]
-   [Создание пространства имен службы][Создание пространства имен службы]
-   [Получение учетных данных управления по умолчанию для пространства имен][Получение учетных данных управления по умолчанию для пространства имен]
-   [Создание приложения PHP][Создание приложения PHP]
-   [Получение клиентских библиотек Azure][Получение клиентских библиотек Azure]
-   [Настройка приложения для использования шины обслуживания][Настройка приложения для использования шины обслуживания]
-   [Практическое руководство. Создание раздела][Практическое руководство. Создание раздела]
-   [Практическое руководство. Создание подписки][Практическое руководство. Создание подписки]
-   [Практическое руководство. Отправка сообщений в раздел][Практическое руководство. Отправка сообщений в раздел]
-   [Практическое руководство. Получение сообщений от подписки][Практическое руководство. Получение сообщений от подписки]
-   [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений][Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]
-   [Практическое руководство. Удаление разделов и подписок][Практическое руководство. Удаление разделов и подписок]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-разделы-service-bus](../includes/howto-service-bus-topics.md)]

## <span id="CreateApplication"></span></a>Создание приложения на PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в [пакете Azure SDK для PHP][пакет Azure SDK для PHP] непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

> [WACOM.ПРИМЕЧАНИЕ]
> Для установки PHP должно быть установлено и включено [расширение OpenSSL][расширение OpenSSL].

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

## <span id="GetClientLibrary"></span></a>Получение клиентских библиотек Azure

[WACOM.INCLUDE [получение-клиент-библиотеки](../includes/get-client-libraries.md)]

## <span id="ConfigureApp"></span></a>Настройка приложения для использования Service Bus

Чтобы использовать API разделов Azure Servise Bus, необходимо следующее:

1.  Ссылка на файл автозагрузчика с использованием инструкции [require\_once][require\_once] и
2.  Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServiceBusService**.

    > [WACOM.NOTE]
    > This example (and other examples in this article) assume you have installed the PHP Client Libraries for Azure via Composer. If you installed the libraries manually or as a PEAR package, you will need to reference the <code>WindowsAzure.php</code> autoloader file.

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

## <span id="CreateTopic"></span></a>Практическое руководство. Создание раздела

Операции управления для разделов Service Bus можно выполнять с помощью класса
**ServiceBusRestProxy**. Объект **ServiceBusRestProxy** создается с помощью встроенного производителем метода **ServicesBuilder::createServiceBusService** с соответствующей строкой подключения,
которая включает в себя разрешения маркеров для управления им.

В приведенном ниже примере показано, как создать экземпляр **ServiceBusRestProxy** и вызвать метод **ServiceBusRestProxy-\>createTopic** для создания раздела с именем `mytopic` в пространстве имен службы `MySBNamespace`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\TopicInfo;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
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
    > You can use the <b>listTopics</b> method on <b>ServiceBusRestProxy</b> objects to check if a topic with a specified name already exists within a service namespace.

## <span id="CreateSubscription"></span></a>Практическое руководство. Создание подписки

Подписки на разделы также создаются с помощью метода **ServiceBusRestProxy-\>createSubscription**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем "mysubscription", и используется фильтр по умолчанию **MatchAll**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {
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

Вы также можете настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела. Самый гибкий тип фильтра, который поддерживается подписками, — это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о фильтрах SqlFilter см. в описании [свойства SqlFilter.SqlExpression][свойства SqlFilter.SqlExpression].

    > [WACOM.NOTE]
    > Each rule on a subscription processes incoming messages independently, adding their result messages to the subscription. In addition, each new subscription has a default <b>Rule</b> with a filter that adds all messages from the topic to the subscription. To receive only messages matching your filter, you must remove the default rule. You can remove the default rule by using the <b>ServiceBusRestProxy->deleteRule</b> method.

В следующем примере создается подписка с именем HighMessages и фильтром **SqlFilter**, который только выбирает сообщения, у которых значение пользовательского свойства **MessageNumber** превышает 3 (см. [Практическое руководство. Отправка сообщений в раздел][Практическое руководство. Отправка сообщений в раздел] для получения сведений о добавлении настраиваемых свойств в сообщения).

    $subscriptionInfo = new SubscriptionInfo("HighMessages");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

    $serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

    $ruleInfo = new RuleInfo("HighMessagesRule");
    $ruleInfo->withSqlFilter("MessageNumber > 3");
    $ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Обратите внимание, что приведенный выше код требует использования дополнительного пространства имен. `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Аналогично, следующий пример создает подписку с именем "LowMessages" и фильтром SqlFilter, который выбирает только сообщения, у которых значение свойства MessageNumber меньше или равно 3:

    $subscriptionInfo = new SubscriptionInfo("LowMessages");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

    $serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

    $ruleInfo = new RuleInfo("LowMessagesRule");
    $ruleInfo->withSqlFilter("MessageNumber <= 3");
    $ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

Теперь, если сообщение будет отправлено в раздел `mytopic`, оно всегда будет доставляться получателям, подписанным на подписку `mysubscription`, и выборочно доставляться получателям, подписанным на подписки HighMessages и LowMessages (в зависимости от содержимого сообщений).

## <span id="SendMessage"></span></a>Практическое руководство. Отправка сообщений в раздел

Чтобы отправить сообщение в раздел Service Bus, в приложении вызывается метод **ServiceBusRestProxy-\>sendTopicMessage**. В следующем примере кода показано, как отправить сообщение в раздел `mytopic`,
созданный ранее в пространстве имен службы `MySBNamespace`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\BrokeredMessage;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
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

Сообщения, отправляемые в разделы Service Bus и получаемые из них, — это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств и методов (например, **getLabel**, **getTimeToLive**, **setLabel** и **setTimeToLive**), а также свойства, которые можно использовать для хранения настраиваемых свойств приложения. В следующем примере показано, как отправить 5 тестовых сообщений в созданный ранее раздел `mytopic`. Метод **SetProperty** используется для добавления настраиваемого свойства (`MessageNumber`) в каждое сообщение. Обратите внимание, как значение свойства `MessageNumber` меняется для каждого сообщения (с помощью этого можно определить, какие подписки получают его, как показано в предыдущем разделе [Практическое руководство. Создание подписки][Практическое руководство. Создание подписки]).

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

## <span id="ReceiveMessages"></span></a>Практическое руководство. Получение сообщений от подписки

Основным способом получения сообщений из подписки является метод **ServiceBusRestProxy-\>receiveSubscriptionMessage**. Полученные сообщения могут работать в двух различных режимах: **ReceiveAndDelete** (по умолчанию) и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, то есть, когда Service Bus получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель,
которая лучше всего работает для сценариев, где приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), шина Service Bus завершает второй этап процесса получения, передавая полученное сообщение в метод **ServiceBusRestProxy-\>deleteMessage**. Когда Service Bus обнаруживает вызов **deleteMessage**, сообщение помечается как используемое и удаляется из очереди.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не режим по умолчанию).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
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

## <span id="HandleCrashes"></span></a>Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений

Service Bus предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого Service Bus разблокирует сообщение в очереди и сделает его доступным для приема тем же или другим приложением-пользователем.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <span id="DeleteTopicsAndSubscriptions"></span></a>Удаление разделов и подписок

Чтобы удалить раздел или подписку, используйте метод **ServiceBusRestProxy -\> deleteTopic** или **ServiceBusRestProxy -\> deleteSubscripton** соответственно. Обратите внимание, что при удалении раздела также удаляются все подписки, зарегистрированные в этом разделе.

Следующий пример показывает, как удалить раздел (`mytopic`) и его зарегистрированные подписки.

    require_once 'vendor\autoload.php';

    use WindowsAzure\ServiceBus\ServiceBusService;
    use WindowsAzure\ServiceBus\ServiceBusSettings;
    use WindowsAzure\Common\ServiceException;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
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

## <span id="NextSteps"></span></a>Дальнейшие действия

Вы познакомились с основами использования очередей Service Bus.
Дополнительные сведения см. в статье [Очереди, разделы и подписки][Очереди, разделы и подписки] на портале MSDN.

  [пакет Azure SDK для PHP]: http://go.microsoft.com/fwlink/?LinkId=252473
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Создание приложения PHP]: #CreateApplication
  [Получение клиентских библиотек Azure]: #GetClientLibrary
  [Настройка приложения для использования шины обслуживания]: #ConfigureApp
  [Практическое руководство. Создание раздела]: #CreateTopic
  [Практическое руководство. Создание подписки]: #CreateSubscription
  [Практическое руководство. Отправка сообщений в раздел]: #SendMessage
  [Практическое руководство. Получение сообщений от подписки]: #ReceiveMessages
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #HandleCrashes
  [Практическое руководство. Удаление разделов и подписок]: #DeleteTopicsAndSubscriptions
  [Дальнейшие действия]: #NextSteps
  [расширение OpenSSL]: http://php.net/openssl
  [свойства SqlFilter.SqlExpression]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367516.aspx
