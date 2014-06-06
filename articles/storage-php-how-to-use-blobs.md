<properties title="Использование хранилища BLOB-объектов (PHP) - руководство по функциям Azure" pageTitle="Использование хранилища BLOB-объектов (PHP) — руководство по функциям Azure" metaKeywords="Служба BLOB-объектов Azure PHP, BLOB-объекты Azure PHP" description="Использование службы BLOB-объектов Azure для передачи, отображения списка, загрузки и удаления BLOB-объектов. Примеры кода написаны на PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="waltpo" manager="bjsmith" editor="mollybos" />

#Использование службы BLOB-объектов в PHP

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны на PHP и используют [пакет Azure SDK для PHP][загрузить]. Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия](#NextSteps).

##Оглавление

* [Что такое хранилище BLOB-объектов](#what-is)
* [Основные понятия](#concepts)
* [Создание учетной записи хранения Azure](#CreateAccount)
* [Создание приложения PHP](#CreateApplication)
* [Настройка приложения для доступа к службе BLOB-объектов](#ConfigureStorage)
* [Настройка подключения к хранилищу Azure](#ConnectionString)
* [Практическое руководство. Создание контейнера](#CreateContainer)
* [Практическое руководство. Отправка BLOB-объекта в контейнер](#UploadBlob)
* [Практическое руководство. Перечисление BLOB-объектов в контейнере](#ListBlobs)
* [Практическое руководство. Загрузка BLOB-объектов](#DownloadBlob)
* [Практическое руководство. Удаление BLOB-объектов](#DeleteBlob)
* [Практическое руководство. Удаление контейнера BLOB-объектов](#DeleteContainer)
* [Дальнейшие действия](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="CreateApplication"></a>Создание приложения PHP</h2>

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в пакете Azure SDK для PHP непосредственно из кода. Для создания приложения можно использовать любые средства разработки, включая Блокнот.

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

<h2><a id="GetClientLibrary"></a>Получение клиентских библиотек Azure</h2>

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="ConfigureStorage"></a>Настройка приложения для доступа к службе BLOB-объектов</h2>

Чтобы использовать интерфейсы API службы BLOB-объектов Azure, необходимо следующее:

1. Ссылка на файл автозагрузчика с использованием инструкции [require_once][require_once].
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

> [WACOM.NOTE]
> При работе с этим примером (и другими примерами в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure через Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо сослаться на файл автозагрузчика `WindowsAzure.php`.

	require_once 'vendor\autoload.php'; 	use WindowsAzure\Common\ServicesBuilder;


В приведенных ниже примерах инструкция `require_once` всегда будет отображаться, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

<h2><a id="ConnectionString"></a>Настройка подключения к хранилищу Azure</h2>

Для создания клиента службы BLOB-объектов Azure необходимо сначала сформировать правильную строку подключения. Формат строки подключения к службе BLOB-объектов:

Для доступа к службе в режиме реального времени:

	DefaultEndpointsProtocol=[http|https];AccountName=[ваша_учетная_запись];AccountKey=[ваш_ключ]

Для доступа к хранилищу эмулятора:

	UseDevelopmentStorage=true


Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

* передать строку подключения напрямую или
* использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:
	* по умолчанию предоставляется поддержка одного внешнего источника - переменных среды
	* можно добавить новые источники, расширив класс **ConnectionStringSource**

В приведенных здесь примерах строка подключения передается напрямую.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

<h2><a id="CreateContainer"></a>Практическое руководство. Создание контейнера</h2>

Объект **BlobRestProxy** позволяет создать контейнер BLOB-объектов с помощью метода **createContainer**. При создании контейнера можно задать параметры контейнера, однако это не является обязательным. (В приведенном ниже примере показано, как задать ACL и метаданные контейнера).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder; 	use WindowsAzure\Blob\Models\CreateContainerOptions; 	use WindowsAzure\Blob\Models\PublicAccessType; 	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	// OPTIONAL: Set public access policy and metadata.
	// Create container options object.
	$createContainerOptions = new CreateContainerOptions();	

	// Set public access policy. Possible values are  	// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
	// CONTAINER_AND_BLOBS: 	 	// Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous  	// request, but cannot enumerate containers within the storage account.
	// 	// BLOBS_ONLY: 	// Specifies public read access for blobs. Blob data within this      // container can be read via anonymous request, but container data is not      // available. proxys cannot enumerate blobs within the container via  	// anonymous request.
	// If this value is not specified in the request, container data is  	// private to the account owner.
	$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS); 	 	// Set container metadata 	$createContainerOptions->addMetaData("key1", "value1"); 	$createContainerOptions->addMetaData("key2", "value2"); 	 	try	{ 		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions); 	} 	catch(ServiceException $e){ 		// Handle exception based on error codes and messages.
		// Error codes and messages are here:  		// http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Вызов **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** делает данные контейнера и blob-объектов доступными через анонимные запросы. Вызов **setPublicAccess(PublicAccessType::BLOBS_ONLY)** делает доступными через анонимные запросы только данные BLOB-объектов. Дополнительные сведения о ACL контейнера см. в разделе [Определение ACL контейнера (REST API)][container-acl]

Дополнительные сведения о кодах ошибок службы Blob-объектов см. в разделе [Коды ошибок службы Blob-объектов][error-codes].

<h2><a id="UploadBlob"></a>Практическое руководство. Отправка BLOB-объекта в контейнер</h2>

Чтобы передать файл в виде BLOB-объекта, используйте метод **BlobRestProxy-&gt;createBlockBlob**. Эта операция создает BLOB-объект, если он не существует, или заменяет его, если он существует. В примере кода предполагается, что контейнер уже был создан и использует [fopen][fopen] для открытия файла в виде потока.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder; 	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	 	$content = fopen("c:\myfile.txt", "r"); 	$blob_name = "myblob"; 	 	try	{ 		//Upload blob 		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content); 	} 	catch(ServiceException $e){ 		// Handle exception based on error codes and messages.
		// Error codes and messages are here:  		// http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx 		$code = $e->getCode(); 		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Обратите внимание, что в приведенном выше примере BLOB-объект передается в виде потока. Однако BLOB-объект может быть передан как строка с помощью, к примеру, функции  [file\_get\_contents][file_get_contents]. Для этого измените `$content = fopen(&quot;c:\myfile.txt&quot;, &quot;r&quot;);` из приведенного выше примера на `$content = file_get_contents(&quot;c:\myfile.txt&quot;);`.

<h2><a id="ListBlobs"></a>Практическое руководство. Перечисление BLOB-объектов в контейнере</h2>

Чтобы получить список BLOB-объектов в контейнере, используйте метод **BlobRestProxy-&gt;listBlobs** с циклом **foreach** для перебора результатов. Следующий код выводит имя каждого BLOB-объекта в контейнере и соответствующий URI в браузере.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder; 	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	 	try	{ 		// List blobs.
		$blob_list = $blobRestProxy->listBlobs("mycontainer"); 		$blobs = $blob_list->getBlobs(); 		 		foreach($blobs as $blob) 		{ 			echo $blob->getName().": ".$blob->getUrl()."<br />"; 		} 	} 	catch(ServiceException $e){ 		// Handle exception based on error codes and messages.
		// Error codes and messages are here:  		// http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx 		$code = $e->getCode(); 		$error_message = $e->getMessage(); 		echo $code.": ".$error_message."<br />";
	}


<h2><a id="DownloadBlob"></a>Практическое руководство. Загрузка BLOB-объектов</h2>

Чтобы загрузить BLOB-объект, вызовите метод **BlobRestProxy-&gt;getBlob**, затем вызовите метод **getContentStream** для результирующего объекта **GetBlobResult**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder; 	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	 	try	{ 		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob"); 		fpassthru($blob->getContentStream()); 	} 	catch(ServiceException $e){ 		// Handle exception based on error codes and messages.
		// Error codes and messages are here:  		// http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx 		$code = $e->getCode(); 		$error_message = $e->getMessage(); 		echo $code.": ".$error_message."<br />"; 	}

Обратите внимание, что приведенный выше пример получает BLOB-объект как ресурс потока (поведение по умолчанию). Однако можно использовать функцию [stream\_get\_contents][stream-get-contents] для преобразования возвращенного потока в строку.

<h2><a id="DeleteBlob"></a>Практическое руководство. Удаление BLOB-объектов</h2>

Чтобы удалить BLOB-объект, передайте имя контейнера и имя BLOB-объекта в **BlobRestProxy-&gt;deleteBlob**. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder; 	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	 	try	{ 		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob"); 	} 	catch(ServiceException $e){ 		// Handle exception based on error codes and messages.
		// Error codes and messages are here:  		// http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx 		$code = $e->getCode(); 		$error_message = $e->getMessage(); 		echo $code.": ".$error_message."<br />"; 	}

<h2><a id="DeleteContainer"></a>Практическое руководство. Удаление контейнера BLOB-объектов</h2>

Наконец, чтобы удалить контейнер BLOB-объектов, передайте имя контейнера в **BlobRestProxy-&gt;deleteContainer**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder; 	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	 	try	{ 		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer"); 	} 	catch(ServiceException $e){ 		// Handle exception based on error codes and messages.
		// Error codes and messages are here:  		// http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx 		$code = $e->getCode(); 		$error_message = $e->getMessage(); 		echo $code.": ".$error_message."<br />"; 	}

<h2><a id="NextSteps"></a>Дальнейшие действия</h2>

Вы изучили основные сведения о службе BLOB-объектов Azure. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- См. справочник MSDN: [Хранение данных и доступ к ним в Azure] []
- Посетите блог команды разработчиков хранилища Azure <http://blogs.msdn.com/b/windowsazurestorage/>
- Просмотрите пример блокировки BLOB-объектов в PHP на сайте <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- Просмотрите пример страницы BLOB-объектов в PHP на сайте <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

[загрузить]: http://go.microsoft.com/fwlink/?LinkID=252473
[Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

