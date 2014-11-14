<properties title="Использование хранилища BLOB-объектов (PHP) &mdash; руководство по компонентам Azure" pageTitle="Использование хранилища BLOB-объектов (PHP) &mdash; Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Вы узнаете, как использовать службы BLOB-объектов Azure для передачи, перечисления, загрузки и удаления содержимого BLOB-объектов. Примеры кода написаны на PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="robmcm" manager="adinah" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Использование службы BLOB-объектов в PHP

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны на PHP и используют [пакет Azure SDK для PHP][пакет Azure SDK для PHP]. Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое хранилище BLOB-объектов][Что такое хранилище BLOB-объектов]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]
-   [Создание приложения PHP][Создание приложения PHP]
-   [Настройка приложения для доступа к службе BLOB-объектов][Настройка приложения для доступа к службе BLOB-объектов]
-   [Настройка подключения к хранилищу Azure][Настройка подключения к хранилищу Azure]
-   [Практическое руководство. Создание контейнера][Практическое руководство. Создание контейнера]
-   [Практическое руководство. Отправка BLOB-объекта в контейнер][Практическое руководство. Отправка BLOB-объекта в контейнер]
-   [Практическое руководство. Перечисление BLOB-объектов в контейнере][Практическое руководство. Перечисление BLOB-объектов в контейнере]
-   [Практическое руководство. Загрузка blob-объектов][Практическое руководство. Загрузка blob-объектов]
-   [Практическое руководство. Удаление BLOB-объекта][Практическое руководство. Удаление BLOB-объекта]
-   [Практическое руководство. Удаление контейнера blob-объектов][Практическое руководство. Удаление контейнера blob-объектов]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-блоб-объект-хранилище](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>Создание учетной записи хранения Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <span id="CreateApplication"></span></a>Создание приложения на PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в пакете Azure SDK для PHP непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

## <span id="GetClientLibrary"></span></a>Получение клиентских библиотек Azure

[WACOM.INCLUDE [получение-клиент-библиотеки](../includes/get-client-libraries.md)]

## <span id="ConfigureStorage"></span></a>Настройка приложения для доступа к службе BLOB-объектов

Чтобы использовать интерфейсы API службы BLOB-объектов Azure, необходимо следующее:

1.  Ссылка на файл автозагрузчика с использованием инструкции [require\_once][require\_once] и
2.  Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

> [WACOM.NOTE]
> При работе с этим примером (и другими примерами в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure через Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо сослаться на файл автозагрузчика `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

В приведенных ниже примерах инструкция `require_once` всегда будет отображаться, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

## <span id="ConnectionString"></span></a>Настройка подключения к хранилищу Azure

Для создания клиента службы BLOB-объектов Azure необходимо сначала сформировать правильную строку подключения. Формат строки подключения к службе BLOB-объектов:

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

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <span id="CreateContainer"></span></a>Практическое руководство. Создание контейнера

Объект **BlobRestProxy** позволяет создать контейнер BLOB-объектов с помощью метода **createContainer**. При создании контейнера можно задать параметры контейнера, однако это не является обязательным. (В приведенном ниже примере показано, как задать ACL и метаданные контейнера).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Blob\Models\CreateContainerOptions;
    use WindowsAzure\Blob\Models\PublicAccessType;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


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

    // Set container metadata
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Вызов **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** делает контейнер и данные BLOB-объектов доступными через анонимные запросы. Вызов **setPublicAccess(PublicAccessType::BLOBS\_ONLY)** делает доступными через анонимные запросы только данные BLOB-объектов. Дополнительные сведения о ACL контейнера см. в разделе [Определение ACL контейнера (REST API)][Определение ACL контейнера (REST API)]

Дополнительные сведения о кодах ошибок службы Blob-объектов см. в разделе [Коды ошибок службы Blob-объектов][Коды ошибок службы Blob-объектов].

## <span id="UploadBlob"></span></a>Практическое руководство. Отправка BLOB-объекта в контейнер

Чтобы передать файл в виде BLOB-объекта, используйте метод **BlobRestProxy-\>createBlockBlob**. Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует. В примере кода предполагается, что контейнер уже был создан и использует [fopen][fopen] для открытия файла в виде потока.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Обратите внимание, что в приведенном выше примере BLOB-объект передается в виде потока. Однако BLOB-объект может быть передан как строка с помощью, к примеру, функции [file\_get\_contents][file\_get\_contents]. Для этого измените в приведенном выше примере `$content = fopen("c:\myfile.txt", "r");` на `$content = file_get_contents("c:\myfile.txt");`.

## <span id="ListBlobs"></span></a>Практическое руководство. Перечисление BLOB-объектов в контейнере

Чтобы получить список BLOB-объектов в контейнере, используйте метод **BlobRestProxy-\>listBlobs** с циклом **foreach** для перебора результатов. Следующий код выводит имя каждого BLOB-объекта в контейнере и соответствующий URI в браузере.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();
        
        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DownloadBlob"></span></a>Практическое руководство. Загрузка blob-объектов

Чтобы загрузить BLOB-объект, вызовите метод **BlobRestProxy-\>getBlob**, затем вызовите метод **getContentStream** для результирующего объекта **GetBlobResult**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Обратите внимание, что приведенный выше пример получает BLOB-объект как ресурс потока (поведение по умолчанию). Однако можно использовать функцию [stream\_get\_contents][stream\_get\_contents] для преобразования возвращенного потока в строку.

## <span id="DeleteBlob"></span></a>Практическое руководство. Удаление BLOB-объекта

Чтобы удалить BLOB-объект, передайте имя контейнера и имя BLOB-объекта в **BlobRestProxy-\>deleteBlob**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteContainer"></span></a>Практическое руководство. Удаление контейнера blob-объектов

Наконец, чтобы удалить контейнер BLOB-объектов, передайте имя контейнера в **BlobRestProxy-\>deleteContainer**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="NextSteps"></span></a>Дальнейшие действия

Вы изучили основные сведения о службе BLOB-объектов Azure. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][Хранение данных и доступ к ним в Azure]
-   Посетите блог команды разработчиков хранилища Azure <http://blogs.msdn.com/b/windowsazurestorage/>
-   См. пример блочного blob-объекта РНР по адресу: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
-   См. пример страничного blob-объекта РНР по адресу: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

  [пакет Azure SDK для PHP]: http://go.microsoft.com/fwlink/?LinkID=252473
  [Дальнейшие действия]: #NextSteps
  [Что такое хранилище BLOB-объектов]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #CreateAccount
  [Создание приложения PHP]: #CreateApplication
  [Настройка приложения для доступа к службе BLOB-объектов]: #ConfigureStorage
  [Настройка подключения к хранилищу Azure]: #ConnectionString
  [Практическое руководство. Создание контейнера]: #CreateContainer
  [Практическое руководство. Отправка BLOB-объекта в контейнер]: #UploadBlob
  [Практическое руководство. Перечисление BLOB-объектов в контейнере]: #ListBlobs
  [Практическое руководство. Загрузка blob-объектов]: #DownloadBlob
  [Практическое руководство. Удаление BLOB-объекта]: #DeleteBlob
  [Практическое руководство. Удаление контейнера blob-объектов]: #DeleteContainer
  [require\_once]: http://php.net/require_once
  [Определение ACL контейнера (REST API)]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179391.aspx
  [Коды ошибок службы Blob-объектов]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
  [fopen]: http://www.php.net/fopen
  [file\_get\_contents]: http://php.net/file_get_contents
  [stream\_get\_contents]: http://www.php.net/stream_get_contents
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
