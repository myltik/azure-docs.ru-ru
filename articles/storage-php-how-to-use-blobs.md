<properties title="How to use blob storage (PHP) - Azure feature guide" pageTitle="Как использовать хранилище больших двоичных объектов (PHP) - Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Вы узнаете, как использовать службы BLOB-объектов Azure для передачи, перечисления, загрузки и удаления содержимого BLOB-объектов. Примеры кода написаны на PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="tomfitz" manager="wpickett" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/24/2014" ms.author="tomfitz" />

#Использование службы BLOB-объектов в PHP

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны на PHP, и в них используется [пакет SDK для Azure для PHP] [скачать]. Здесь описаны такие сценарии, как **отправка**, **перечисление**, **скачивание** и **удаление** больших двоичных объектов. Дополнительную информацию о больших двоичных объектах см. в разделе [Дальнейшие действия](#NextSteps) .

##Оглавление

* [Что такое хранилище BLOB-объектов](#what-is)
* [Основные понятия](#concepts)
* [Создание учетной записи хранения Azure](#CreateAccount)
* [Создание приложения на PHP](#CreateApplication)
* [Настройка приложения для доступа к службе BLOB-объектов](#ConfigureStorage)
* [Настройка подключения к службе хранилища Azure](#ConnectionString)
* [Практическое руководство. Создание контейнера](#CreateContainer)
* [Практическое руководство. Отправка большого двоичного объекта в контейнер](#UploadBlob)
* [Практическое руководство. Перечисление больших двоичных объектов в контейнере](#ListBlobs)
* [Практическое руководство. Скачивание большого двоичного объекта](#DownloadBlob)
* [Практическое руководство. Удаление большого двоичного объекта](#DeleteBlob)
* [Практическое руководство. Удаление контейнера больших двоичных объектов](#DeleteContainer)
* [Дальнейшие действия](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="CreateApplication"></a>Создание приложения на PHP</h2>

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в пакете Azure SDK для PHP непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

<h2><a id="GetClientLibrary"></a>Получение клиентских библиотек Azure</h2>

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="ConfigureStorage"></a>Настройка приложения для доступа к службе BLOB-объектов</h2>

Чтобы использовать интерфейсы API службы BLOB-объектов Azure, необходимо следующее:

1. Ссылка на файл автозагрузчика с использованием инструкции [require_once][require_once] и
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

> [WACOM.NOTE]
> При работе с этим примером (и другими примерами в этой статье) предполагается, что вы установили клиентские библиотеки PHP для Azure с помощью Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо создать ссылку на файл автозагрузчика WindowsAzure.php.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


В приведенных ниже примерах всегда будет отображаться инструкция require_once, однако ссылки будут приводиться только на классы, необходимые для выполнения этого примера.

<h2><a id="ConnectionString"></a>Настройка подключения к хранилищу Azure</h2>

Для создания клиента службы BLOB-объектов Azure необходимо сначала сформировать правильную строку подключения. Формат строки подключения к службе BLOB-объектов:

Для доступа к службе в режиме реального времени:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Для доступа к хранилищу эмулятора:

	UseDevelopmentStorage=true


Чтобы создать клиент службы Azure, необходимо использовать класс **ServicesBuilder**. Вы можете:

* передать строку подключения напрямую или
* использовать **CloudConfigurationManager (CCM)**, чтобы проверить несколько внешних источников на наличие строки подключения:
	* по умолчанию предоставляется поддержка одного внешнего источника - переменных среды;
	* можно добавить новые источники, расширив класс **ConnectionStringSource**

В приведенных здесь примерах строка подключения передается напрямую.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

<h2><a id="CreateContainer"></a>Практическое руководство: Создание контейнера</h2>

Объект **BlobRestProxy** позволяет создать контейнер больших двоичных объектов с помощью метода **createContainer**. При создании контейнера можно задать параметры контейнера, однако это не является обязательным. (В приведенном ниже примере показано, как задать ACL и метаданные контейнера).

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
	
	try	{
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

Вызов **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** позволяет получить доступ к данным контейнера и больших двоичных объектов через анонимные запросы. Вызов **setPublicAccess(PublicAccessType::BLOBS_ONLY)** позволяет получить доступ через анонимные запросы только к данным больших двоичных объектов. Дополнительную информацию о списках управления доступом для контейнеров см. в разделе [Определение ACL контейнера (REST API)][container-acl].

Дополнительную информацию о службе BLOB-объектов см. в статье [Коды ошибок службы BLOB-объектов][error-codes].

<h2><a id="UploadBlob"></a>Практическое руководство: Отправка BLOB-объекта в контейнер</h2>

Чтобы передать файл в виде большого двоичного объекта, используйте метод **BlobRestProxy->createBlockBlob**. Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует. В примере кода предполагается, что контейнер уже создан и использует [fopen][fopen], чтобы открывать файлы в виде потоков.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";
	
	try	{
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

Обратите внимание, что в приведенном выше примере BLOB-объект передается в виде потока. Однако большой двоичный объект также можно передать как строку с помощью, к примеру, функции [file\_get\_contents][file_get_contents]. Для этого замените $content = fopen("c:\myfile.txt", "r"); из приведенного выше примера на $content = filegetcontents("c:\myfile.txt");.

<h2><a id="ListBlobs"></a>Практическое руководство: Перечисление BLOB-объектов в контейнере</h2>

Чтобы получить список больших двоичных объектов контейнера, используйте метод **BlobRestProxy->listBlobs** с циклом **foreach** для перебора результатов. Следующий код выводит имя каждого BLOB-объекта в контейнере и соответствующий URI в браузере.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
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


<h2><a id="DownloadBlob"></a>Практическое руководство. Загрузка BLOB-объектов</h2>

Чтобы скачать большой двоичный объект, вызовите метод **BlobRestProxy->getBlob**, а затем метод **getContentStream** для результирующего объекта **GetBlobResult**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
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

Обратите внимание, что приведенный выше пример получает BLOB-объект как ресурс потока (поведение по умолчанию). Однако вы можете использовать функцию [stream\_get\_contents][stream-get-contents], чтобы преобразовать возвращенный поток в строку.

<h2><a id="DeleteBlob"></a>Практическое руководство: Удаление BLOB-объекта</h2>

Чтобы удалить большой двоичный объект, передайте имя контейнера и имя большого двоичного объекта в **BlobRestProxy->deleteBlob**. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
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

<h2><a id="DeleteContainer"></a>Практическое руководство: Удаление контейнера blob-объектов</h2>

Наконец, чтобы удалить контейнер больших двоичных объектов, передайте имя контейнера в **BlobRestProxy->deleteContainer**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
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

<h2><a id="NextSteps"></a>Дальнейшие действия</h2>

Вы изучили основные сведения о службе BLOB-объектов Azure. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- См. справочник MSDN: [Хранение данных и доступ к ним в Azure] []
- Посетите блог команды разработчиков хранилища Azure <http://blogs.msdn.com/b/windowsazurestorage/>
- См. пример блочного BLOB-объекта РНР по адресу: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- См. пример страничного BLOB-объекта PHP по адресу: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Storing and Accessing Data in Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

<!--HONumber=35.1-->
