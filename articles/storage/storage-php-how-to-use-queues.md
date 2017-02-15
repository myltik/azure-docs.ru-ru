---
title: "Как использовать хранилище очередей из PHP | Документация Майкрософт"
description: "Узнайте, как создавать и удалять очереди, а также вставлять, получать и удалять сообщения с помощью службы хранилища очередей Azure. Примеры написаны на PHP."
documentationcenter: php
services: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7582b208-4851-4489-a74a-bb952569f55b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 1fea38ed4caacfee0c69d024b1ea6dbdcbccaf1d
ms.openlocfilehash: 3c8f799a917cfc9d74412d90f27f2ea8c21265d4


---
# <a name="how-to-use-queue-storage-from-php"></a>Использование хранилища очередей из PHP
[!INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища очередей Azure. Примеры написаны с использованием классов из пакета SDK для Windows для PHP. Здесь описаны такие сценарии, как вставка, просмотр, получение и удаление сообщений очереди, а также создание и удаление очередей.

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Создание приложения PHP
Единственным требованием для создания приложения PHP, которое получает доступ к хранилищу очередей Azure, является ссылка на классы в пакете SDK для Azure для PHP непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве будут использоваться компоненты хранилища очередей, которые могут быть вызваны локально в приложении PHP или в коде, работающем в веб-роли, рабочей роли или на веб-сайте Azure.

## <a name="get-the-azure-client-libraries"></a>Получение клиентских библиотек Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Настройка приложения для доступа к хранилищу очередей
Чтобы использовать интерфейсы API для хранилища очередей Azure, требуется:

1. Ссылка на файл автозагрузчика с использованием оператора [require_once].
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder** .

> [!NOTE]
> В этом примере (и других примерах в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure с помощью Composer. Если вы установили эти библиотеки вручную, то потребуется добавить ссылку на файл автозагрузчика `WindowsAzure.php` .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;

```

В приведенных ниже примерах всегда будет отображаться оператор `require_once` , но ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

## <a name="set-up-an-azure-storage-connection"></a>Настройка подключения к службе хранилища Azure
Чтобы создать экземпляр клиента хранилища очередей Azure, сначала необходимо сформировать правильную строку подключения. Формат строки подключения к службе очередей:

Для доступа к службе в режиме реального времени:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Для доступа к хранилищу эмулятора:

```php
UseDevelopmentStorage=true
```

Чтобы создать клиент службы Azure, необходимо использовать класс **ServicesBuilder** . Для этого можно использовать один из следующих методов.

* Передать строку подключения напрямую или
* Использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения.
  * По умолчанию предоставляется поддержка одного внешнего источника — переменных среды.
  * Можно добавить новые источники, расширив класс **ConnectionStringSource** .

В приведенных здесь примерах строка подключения передается напрямую.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Создание очереди
Объект **QueueRestProxy** позволяет создать очередь с помощью метода **createQueue**. При создании очереди можно задать ее параметры, однако это не является обязательным. (В приведенном ниже примере показано, как задать метаданные в очереди.)

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueRestProxy->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Не следует полагаться на учет регистра для ключей метаданных. Все параметры считываются из службы в нижнем регистре.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Добавление сообщения в очередь
Чтобы добавить сообщение в очередь, используйте **QueueRestProxy->createMessage**. Этот метод принимает имя очереди, текст сообщения и параметры сообщения (которые не являются обязательными).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

try    {
    // Create message.
    $builder = new ServicesBuilder();
    $queueRestProxy->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Просмотр следующего сообщения
Вы можете просмотреть сообщение (или сообщения) в начале очереди, не удаляя его из очереди, вызвав метод **QueueRestProxy->peekMessages**. По умолчанию метод **peekMessage** возвращает одно сообщение, но это значение можно изменить с помощью метода **PeekMessagesOptions->setNumberOfMessages**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
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
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Удаление следующего сообщения из очереди
Ваш код удаляет сообщение из очереди в два этапа. Во-первых, вызовите метод **QueueRestProxy->listMessages**, который делает сообщение невидимым для любого другого кода, который считывает данные из очереди. По умолчанию это сообщение будет оставаться невидимым в течение 30 секунд (если за это время сообщение не будет удалено, оно снова станет видимым в очереди). Чтобы завершить удаление сообщения из очереди, необходимо вызвать **QueueRestProxy->deleteMessage**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **deleteMessage** сразу после обработки сообщения.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueRestProxy->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Изменение содержимого сообщения в очереди
Вы можете изменить содержимое сообщения непосредственно в очереди, вызвав **QueueRestProxy->updateMessage**. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в сообщение очереди и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов. Если количество повторов сообщения превысит *n* раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueRestProxy->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Дополнительные параметры для удаления сообщений из очереди
Существует два способа настройки извлечения сообщения из очереди. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **getMessages** используется для получения 16 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла **for** . Он также задает время ожидания невидимости&5; минут для каждого сообщения.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

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
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Получение длины очереди
Вы можете узнать приблизительное количество сообщений в очереди. Метод **QueueRestProxy->getQueueMetadata** запрашивает у службы очередей метаданные очереди. Вызов метода **getApproximateMessageCount** для возвращенного объекта позволяет получить количество сообщений в очереди. Счетчик указывает число лишь приблизительно, так как сообщения могут добавляться или удаляться после ответа службы очередей на ваш запрос.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Удаление очереди
Чтобы удалить очередь и все сообщения в ней, вызовите метод **QueueRestProxy->deleteQueue**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

try    {
    // Delete queue.
    $queueRestProxy->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Дальнейшие действия
Вы изучили основную информацию о хранилище очередей Azure. Дополнительную информацию о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

* Посетите [блог команды разработчиков службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/).

Дополнительную информацию можно найти также в [Центре разработчика PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com




<!--HONumber=Dec16_HO2-->


