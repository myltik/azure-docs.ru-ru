<properties title="How to use the queue service (PHP) - Azure feature guide" pageTitle="How to use the queue service (PHP) | Microsoft Azure" metaKeywords="Azure Queue Service messaging PHP" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in PHP." documentationCenter="PHP" services="storage" authors="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Использование службы очередей из PHP

В этом руководстве показано, как реализовать типичные сценарии с использованием службы очередей Azure. Примеры написаны с использованием классов из пакета Windows SDK для PHP. Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия][].

## Оглавление

-   [Что такое хранилище очередей?][]
-   [Основные понятия][]
-   [Создание учетной записи хранения Azure][]
-   [Создание приложения PHP][]
-   [Настройка приложения для использования службы очередей][]
-   [Настройка подключения к хранилищу Azure][]
-   [Практическое руководство. Создание очереди][]
-   [Практическое руководство. Добавление сообщения в очередь][]
-   [Практическое руководство. Просмотр следующего сообщения][]
-   [Практическое руководство. Удаление следующего сообщения из очереди][]
-   [Практическое руководство. Изменение содержимого сообщения в очереди][]
-   [Дополнительные параметры для удаления сообщений из очереди][]
-   [Практическое руководство. Получение длины очереди][]
-   [Практическое руководство. Удаление очереди][]
-   [Дальнейшие действия][1]

[WACOM.INCLUDE [руководство-очередь-хранилище][]]

## <span id="create-account"></span></a>Создание учетной записи хранения Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись][]]

## <span id="create-app"></span></a>Создание приложения на PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе очередей Azure, является ссылка на классы в пакете Azure SDK для PHP непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве будут использоваться компоненты службы очередей, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

## <span id="GetClientLibrary"></span></a>Получение клиентских библиотек Azure

[WACOM.INCLUDE [получение-клиент-библиотеки][]]

## <span id="configure-app"></span></a>Настройка приложения для доступа к службе очередей

Чтобы использовать интерфейсы API службы очередей Azure, необходимо следующее:

1.  Ссылка на файл автозагрузчика с использованием инструкции [require\_once][] и
2.  Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

> [WACOM.NOTE]
> При работе с этим примером (и другими примерами в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure через Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо сослаться на файл автозагрузчика `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

В приведенных ниже примерах инструкция `require_once` всегда будет отображаться, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

## <span id="connection-string"></span></a>Настройка подключения к хранилищу Azure

Для создания клиента службы очередей Azure необходимо сначала сформировать правильную строку подключения. Формат строки подключения к службе очередей:

Для доступа к службе в режиме реального времени:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Для доступа к хранилищу эмулятора:

    UseDevelopmentStorage=true

Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

-   передать строку подключения напрямую или
-   использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:

    -   по умолчанию предоставляется поддержка одного внешнего источника – переменных среды
    -   можно добавить новые источники, расширив класс **ConnectionStringSource**

В приведенных здесь примерах строка подключения передается напрямую.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

## <span id="create-queue"></span></a>Практическое руководство. Создание очереди

Объект **QueueRestProxy** позволяет создать очередь с помощью метода **createQueue**. При создании очереди можно задать ее параметры, однако это не является обязательным. (В приведенном ниже примере показано, как задать метаданные в очереди.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\CreateQueueOptions;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // OPTIONAL: Set queue metadata.
    $createQueueOptions = new CreateQueueOptions();
    $createQueueOptions->addMetaData("key1", "value1");
    $createQueueOptions->addMetaData("key2", "value2");

    try {
        // Create queue.
        $queueRestProxy->createQueue("myqueue", $createQueueOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

> [WACOM.NOTE]
>  Не следует полагаться на учет регистра для ключей метаданных. Все параметры считываются из службы в нижнем регистре.

## <span id="add-message"></span></a>Практическое руководство. Добавление сообщения в очередь

Чтобы добавить сообщение в очередь, используйте **QueueRestProxy-\>createMessage**. Этот метод принимает имя очереди, текст сообщения и параметры сообщения (которые не являются обязательными).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\CreateMessageOptions;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    try {
        // Create message.
        $builder = new ServicesBuilder();
        $queueRestProxy->createMessage("myqueue", "Hello World!");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="peek-message"></span></a>Практическое руководство. Просмотр следующего сообщения

Вы можете просмотреть сообщение (или сообщения) в начале очереди, не удаляя его из очереди, вызвав метод **QueueRestProxy-\>peekMessages**. По умолчанию метод **peekMessage** возвращает одно сообщение, но это значение можно изменить с помощью метода **PeekMessagesOptions-\>setNumberOfMessages**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\PeekMessagesOptions;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // OPTIONAL: Set peek message options.
    $message_options = new PeekMessagesOptions();
    $message_options->setNumberOfMessages(1); // Default value is 1.

    try {
        $peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $messages = $peekMessagesResult->getQueueMessages();

    // View messages.
    $messageCount = count($messages);
    if($messageCount <= 0){
        echo "There are no messages.<br />";
    }
    else{
        foreach($messages as $message)  {
            echo "Peeked message:<br />";
            echo "Message Id: ".$message->getMessageId()."<br />";
            echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
            echo "Message text: ".$message->getMessageText()."<br /><br />";
        }
    }

## <span id="dequeue-message"></span></a>Практическое руководство. Удаление следующего сообщения из очереди

Ваш код удаляет сообщение из очереди в два этапа. Во-первых, вызовите метод **QueueRestProxy-\>listMessages**, который делает сообщение невидимым для любого другого кода, который считывает данные из очереди. По умолчанию это сообщение будет оставаться невидимым в течение 30 секунд (если сообщение не будет удалено в этот период времени, оно снова станет видимым в очереди). Чтобы завершить удаление сообщения из очереди, необходимо вызвать **QueueRestProxy-\>deleteMessage**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **deleteMessage** сразу после обработки сообщения.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // Get message.
    $listMessagesResult = $queueRestProxy->listMessages("myqueue");
    $messages = $listMessagesResult->getQueueMessages();
    $message = $messages[0];

    /* ---------------------
        Process message.
       --------------------- */

    // Get message Id and pop receipt.
    $messageId = $message->getMessageId();
    $popReceipt = $message->getPopReceipt();

    try {
        // Delete message.
        $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="change-message"></span></a>Практическое руководство. Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди, вызвав **QueueRestProxy-\>updateMessage**. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов; если количество повторов сообщения превысит n раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;   

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // Get message.
    $listMessagesResult = $queueRestProxy->listMessages("myqueue");
    $messages = $listMessagesResult->getQueueMessages();
    $message = $messages[0];

    // Define new message properties.
    $new_message_text = "New message text.";
    $new_visibility_timeout = 5; // Measured in seconds. 

    // Get message Id and pop receipt.
    $messageId = $message->getMessageId();
    $popReceipt = $message->getPopReceipt();

    try {
        // Update message.
        $queueRestProxy->updateMessage("myqueue", 
                                    $messageId, 
                                    $popReceipt, 
                                    $new_message_text, 
                                    $new_visibility_timeout);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="additional-options"></span></a>Дополнительные параметры для удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **getMessages** используется для получения 16 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощьюцикла **for**. Он также задает время ожидания невидимости 5 минут для каждого сообщения.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\ListMessagesOptions;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // Set list message options. 
    $message_options = new ListMessagesOptions();
    $message_options->setVisibilityTimeoutInSeconds(300); 
    $message_options->setNumberOfMessages(16);

    // Get messages.
    try{
        $listMessagesResult = $queueRestProxy->listMessages("myqueue", 
                                                         $message_options); 
        $messages = $listMessagesResult->getQueueMessages(); 

        foreach($messages as $message){
            
            /* ---------------------
                Process message.
            --------------------- */
        
            // Get message Id and pop receipt.
            $messageId = $message->getMessageId();
            $popReceipt = $message->getPopReceipt();
            
            // Delete message.
            $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);   
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="get-queue-length"></span></a>Практическое руководство. Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод **QueueRestProxy-\>getQueueMetadata** запрашивает у службы очередей метаданные очереди. Вызов метода **getApproximateMessageCount** для возвращенного объекта позволяет получить количество сообщений в очереди. Счетчик указывает число лишь приблизительно, так как сообщения могут добавляться или удаляться после ответа службы очередей на ваш запрос.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    try {
        // Get queue metadata.
        $queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
        $approx_msg_count = $queue_metadata->getApproximateMessageCount();
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    echo $approx_msg_count;

## <span id="delete-queue"></span></a>Практическое руководство. Удаление очереди

Для удаления очереди и всех сообщений в ней вызовите метод **QueueRestProxy-\>deleteQueue**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create queue REST proxy.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    try {
        // Delete queue.
        $queueRestProxy->deleteQueue("myqueue");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="next-steps"></span></a>Дальнейшие действия

Вы изучили основные сведения о службе очередей Azure. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][]
-   Посетите блог команды разработчиков хранилища Azure <http://blogs.msdn.com/b/windowsazurestorage/>

  [Дальнейшие действия]: #NextSteps
  [Что такое хранилище очередей?]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Создание приложения PHP]: #create-app
  [Настройка приложения для использования службы очередей]: #configure-app
  [Настройка подключения к хранилищу Azure]: #connection-string
  [Практическое руководство. Создание очереди]: #create-queue
  [Практическое руководство. Добавление сообщения в очередь]: #add-message
  [Практическое руководство. Просмотр следующего сообщения]: #peek-message
  [Практическое руководство. Удаление следующего сообщения из очереди]: #dequeue-message
  [Практическое руководство. Изменение содержимого сообщения в очереди]: #change-message
  [Дополнительные параметры для удаления сообщений из очереди]: #additional-options
  [Практическое руководство. Получение длины очереди]: #get-queue-length
  [Практическое руководство. Удаление очереди]: #delete-queue
  [1]: #next-steps
  [руководство-очередь-хранилище]: ../includes/howto-queue-storage.md
  [создание-хранилище-учетная запись]: ../includes/create-storage-account.md
  [получение-клиент-библиотеки]: ../includes/get-client-libraries.md
  [require\_once]: http://www.php.net/manual/en/function.require-once.php
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
