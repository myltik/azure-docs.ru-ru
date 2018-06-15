---
title: Как использовать хранилище очередей из PHP | Документация Майкрософт
description: Узнайте, как создавать и удалять очереди, а также вставлять, получать и удалять сообщения с помощью службы хранилища очередей Azure. Примеры написаны на PHP.
documentationcenter: php
services: storage
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 7582b208-4851-4489-a74a-bb952569f55b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 01/11/2018
ms.author: rogarana
ms.openlocfilehash: 737054aefa684b3fc486c860253c69afbb6eaf0c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30924806"
---
# <a name="how-to-use-queue-storage-from-php"></a>Использование хранилища очередей из PHP
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища очередей Azure. Примеры написаны через классы из [клиентской библиотеки службы хранилища Azure для PHP][download]. Здесь описаны такие сценарии, как вставка, просмотр, получение и удаление сообщений очереди, а также создание и удаление очередей.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Создание приложения PHP
Единственным требованием для создания приложения PHP, которое получает доступ к хранилищу очередей Azure, является привязка к классам в [клиентской библиотеке службы хранилища Azure для PHP][download] непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве будут использоваться компоненты службы хранилища очередей, которые могут быть вызваны локально в приложении PHP или в коде, работающем в веб-роли, рабочей роли или на веб-сайте Azure.

## <a name="get-the-azure-client-libraries"></a>Получение клиентских библиотек Azure
### <a name="install-via-composer"></a>Установка через компоновщик
1. Создайте файл с именем **composer.json** в корневой папке проекта и добавьте в него следующий код:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. Скачайте **[composer.phar][composer-phar]** в корневой каталог проекта.
3. Откройте командную строку и выполните следующую команду в корневом каталоге проекта.
   
    ```
    php composer.phar install
    ```

Также можно перейти в [клиентскую библиотеку службы хранилища Azure для PHP][download] на GitHub, чтобы клонировать исходный код.

## <a name="configure-your-application-to-access-queue-storage"></a>Настройка приложения для доступа к хранилищу очередей
Чтобы использовать интерфейсы API для хранилища очередей Azure, требуется:

1. Ссылка на файл автозагрузчика с использованием оператора [require_once].
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и добавить ссылку на класс **QueueRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

В приведенных ниже примерах всегда будет отображаться оператор `require_once`, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

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

Для создания клиента службы очередей Azure необходимо использовать класс **QueueRestProxy**. Для этого можно использовать один из следующих методов.

* Передать строку подключения напрямую или
* Используйте переменные среды в веб-приложении для хранения строки подключения. Дополнительные сведения о настройке строк подключения см. в разделе [Настройка веб-приложений в службе приложений Azure](../../app-service/web-sites-configure.md).
В приведенных здесь примерах строка подключения передается напрямую.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Создание очереди
Объект **QueueRestProxy** позволяет создать очередь с помощью метода **createQueue**. При создании очереди можно задать ее параметры, однако это не является обязательным. (В приведенном ниже примере показано, как задать метаданные в очереди.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
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

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello World!");
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

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
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
Ваш код удаляет сообщение из очереди в два этапа. Во-первых, вызовите метод **QueueRestProxy->listMessages**, который делает сообщение невидимым для любого другого кода, который считывает данные из очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. (Если за это время сообщение не будет удалено, оно снова появится в очереди.) Чтобы завершить удаление сообщения из очереди, необходимо вызвать **QueueRestProxy->deleteMessage**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **deleteMessage** сразу после обработки сообщения.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
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
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
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

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
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
    $queueClient->updateMessage("myqueue",
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
Существует два способа настройки извлечения сообщения из очереди. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **getMessages** используется для получения 16 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла **for** . Он также задает время ожидания невидимости 5 минут для каждого сообщения.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
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
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
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

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
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

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
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

## <a name="next-steps"></a>Дополнительная информация
Вы изучили основную информацию о хранилище очередей Azure. Дополнительную информацию о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

* [Справочник по API для клиентской библиотеки службы хранилища Azure для PHP](http://azure.github.io/azure-storage-php/)
* [Пример расширенной очереди](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php)

Дополнительную информацию можно найти также в [Центре разработчика PHP](/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: http://getcomposer.org/composer.phar

