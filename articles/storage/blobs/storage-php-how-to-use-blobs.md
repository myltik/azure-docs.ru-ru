---
title: "Как использовать хранилище BLOB-объектов (хранилище объектов) из PHP | Документация Майкрософт"
description: "Хранение неструктурированных данных в облаке в хранилище BLOB-объектов Azure."
documentationcenter: php
services: storage
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 1af56b59-b3f0-4b46-8441-aab463ae088e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 01/11/2018
ms.author: tamram
ms.openlocfilehash: 6bc7fd799eddca14e728f965601acd244d88870c
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-use-blob-storage-from-php"></a>Использование хранилища больших двоичных объектов из PHP
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Обзор
Хранилище BLOB-объектов Azure — это служба, которая хранит неструктурированные данные в облаке в качестве объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны на PHP и используют [клиентскую библиотеку Azure Storage Blob для PHP][download]. Здесь описаны такие сценарии, как **отправка**, **перечисление**, **скачивание** и **удаление** BLOB-объектов. Дополнительные сведения о больших двоичных объектах см. в разделе [Дальнейшие действия](#next-steps).

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Создание приложения PHP
Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является привязка к классам в [клиентской библиотеке службы хранилища Azure для PHP][download] непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве используются компоненты службы хранилища BLOB-объектов, которые могут быть вызваны локально в приложении PHP или в коде, работающем в веб-роли, рабочей роли или на веб-сайте Azure.

## <a name="get-the-azure-client-libraries"></a>Получение клиентских библиотек Azure
### <a name="install-via-composer"></a>Установка через компоновщик
1. Создайте файл с именем **composer.json** в корневой папке проекта и добавьте в него следующий код:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-blob": "*"
      }
    }
    ```
2. Скачайте **[composer.phar][composer-phar]** в корневой каталог проекта.
3. Откройте командную строку и выполните следующую команду в корневом каталоге проекта.
   
    ```
    php composer.phar install
    ```

Также можно перейти в [клиентскую библиотеку службы хранилища Azure для PHP][download] на GitHub, чтобы клонировать исходный код.

## <a name="configure-your-application-to-access-the-blob-service"></a>Настройка приложения для доступа к службе BLOB-объектов
Чтобы использовать интерфейсы API службы BLOB-объектов Azure, требуется:

1. Ссылка на файл автозагрузчика с использованием инструкции [require_once] и
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и добавить ссылку на класс **BlobRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Blob\BlobRestProxy;
```

В приведенных ниже примерах всегда отображается оператор `require_once`, однако ссылки приводятся только на классы, которые необходимы для выполнения этого примера.

## <a name="set-up-an-azure-storage-connection"></a>Настройка подключения к службе хранилища Azure
Чтобы создать экземпляр клиента службы BLOB-объектов Azure, сначала необходимо сформировать правильную строку подключения. Формат строки подключения к службе BLOB-объектов:

Для доступа к службе в режиме реального времени:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Для доступа к эмулятору хранения:

```php
UseDevelopmentStorage=true
```

Для создания клиента службы BLOB-объектов Azure необходимо использовать класс **BlobRestProxy**. Вы можете:

* передать строку подключения напрямую или
* Используйте переменные среды в веб-приложении для хранения строки подключения. Дополнительные сведения о настройке строк подключения см. в разделе [Настройка веб-приложений в службе приложений Azure](../../app-service/web-sites-configure.md).

В приведенных здесь примерах строка подключения передается напрямую.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

$blobClient = BlobRestProxy::createBlobService($connectionString);
```

## <a name="create-a-container"></a>Создание контейнера
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Объект **BlobRestProxy** позволяет создать контейнер BLOB-объектов с помощью метода **createContainer**. При создании контейнера можно задать параметры контейнера, однако это не является обязательным. (В следующем примере показано, как задать список управления доступом (ACL) и метаданные контейнера.)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create blob client.
$blobClient = BlobRestProxy::createBlobService($connectionString);


// OPTIONAL: Set public access policy and metadata.
// Create container options object.
$createContainerOptions = new CreateContainerOptions();

// Set public access policy. Possible values are
// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
// CONTAINER_AND_BLOBS:
// Specifies full public read access for container and blob data.
// proxys can enumerate blobs within the container via anonymous
// request, but cannot enumerate containers within the storage account.
//
// BLOBS_ONLY:
// Specifies public read access for blobs. Blob data within this
// container can be read via anonymous request, but container data is not
// available. proxys cannot enumerate blobs within the container via
// anonymous request.
// If this value is not specified in the request, container data is
// private to the account owner.
$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

// Set container metadata.
$createContainerOptions->addMetaData("key1", "value1");
$createContainerOptions->addMetaData("key2", "value2");

try    {
    // Create container.
    $blobClient->createContainer("mycontainer", $createContainerOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Вызов **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** делает контейнер и данные BLOB-объектов доступными через анонимные запросы. Вызов **setPublicAccess(PublicAccessType::BLOBS_ONLY)** делает доступными через анонимные запросы только данные BLOB-объектов. Дополнительные сведения о списках управления доступом контейнеров см. в статье [Set Container ACL][container-acl] (Задание списка управления доступом для контейнера).

Дополнительные сведения о кодах ошибок службы BLOB-объектов см. в разделе [Blob Service Error Codes][error-codes] (Коды ошибок службы BLOB-объектов).

## <a name="upload-a-blob-into-a-container"></a>Отправка BLOB-объекта в контейнер
Чтобы передать файл в виде BLOB-объекта, используйте метод **BlobRestProxy->createBlockBlob**. Эта операция создает большой двоичный объект, если он еще не существует, или заменяет его, если он существует. В следующем примере кода предполагается, что контейнер уже был создан и использует [fopen][fopen] для открытия файла в виде потока.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

$content = fopen("c:\myfile.txt", "r");
$blob_name = "myblob";

try    {
    //Upload blob
    $blobClient->createBlockBlob("mycontainer", $blob_name, $content);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Обратите внимание, что в предыдущем примере большой двоичный объект передается в виде потока. Однако BLOB-объект можно передать в качестве строки с помощью, к примеру, функции [file\_get\_contents][file_get_contents]. Чтобы сделать это, используя предыдущий пример, измените `$content = fopen("c:\myfile.txt", "r");` на `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере
Чтобы получить список BLOB-объектов в контейнере, используйте метод **BlobRestProxy->listBlobs** с циклом **foreach** для перебора результатов. Следующий код выводит имя каждого большого двоичного объекта в контейнере и соответствующий URI в браузере.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

try    {
    // List blobs.
    $blob_list = $blobClient->listBlobs("mycontainer");
    $blobs = $blob_list->getBlobs();

    foreach($blobs as $blob)
    {
        echo $blob->getName().": ".$blob->getUrl()."<br />";
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="download-a-blob"></a>Загрузка BLOB-объектов
Чтобы загрузить BLOB-объект, вызовите метод **BlobRestProxy->getBlob**, затем вызовите метод **getContentStream** для результирующего объекта **GetBlobResult**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);


try    {
    // Get blob.
    $blob = $blobClient->getBlob("mycontainer", "myblob");
    fpassthru($blob->getContentStream());
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Обратите внимание, что приведенный выше пример получает BLOB-объект как ресурс потока (поведение по умолчанию). Однако можно использовать функцию [stream\_get\_contents][stream-get-contents] для преобразования возвращенного потока в строку.

## <a name="delete-a-blob"></a>Удаление большого двоичного объекта
Чтобы удалить BLOB-объект, передайте имя контейнера и имя BLOB-объекта в **BlobRestProxy->deleteBlob**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

try    {
    // Delete blob.
    $blobClient->deleteBlob("mycontainer", "myblob");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-a-blob-container"></a>Удаление контейнера blob-объектов
Наконец, чтобы удалить контейнер BLOB-объектов, передайте имя контейнера в **BlobRestProxy->deleteContainer**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Blob\BlobRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create blob REST proxy.
$blobClient = BlobRestProxy::createBlobService($connectionString);

try    {
    // Delete container.
    $blobClient->deleteContainer("mycontainer");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Дополнительная информация
Вы изучили основную информацию о службе BLOB-объектов Azure. Дополнительную информацию о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

* [Справочник по API для клиентской библиотеки службы хранилища Azure для PHP](http://azure.github.io/azure-storage-php/)
* [Пример расширенного большого двоичного объекта](https://github.com/Azure/azure-storage-php/blob/master/samples/BlobSamples.php)

[download]: https://github.com/Azure/azure-storage-php
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
