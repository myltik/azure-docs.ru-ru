<properties 
	pageTitle="Использование хранилища BLOB-объектов из PHP в Microsoft Azure" 
	description="Узнайте, как использовать службу BLOB-объектов Azure для отправки, перечисления, загрузки и удаления больших двоичных объектов. Примеры кода написаны на PHP." 
	documentationCenter="php" 
	services="storage" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>

# Использование хранилища BLOB-объектов из PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны на PHP и используют [пакет SDK Azure для PHP] [загрузка]. Здесь описаны такие сценарии, как **отправка**, **перечисление**, **скачивание** и **удаление** больших двоичных объектов. Дополнительную информацию о больших двоичных объектах см. в разделе [Дальнейшие действия](#NextSteps) .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание приложения PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в пакете Azure SDK для PHP непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально в приложении на PHP или в коде, работающем в веб-роли, роли рабочего процесса или на веб-сайте Azure.

## Получение клиентских библиотек Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Настройка приложения для доступа к службе BLOB-объектов

Чтобы использовать интерфейсы API службы BLOB-объектов Azure, необходимо следующее:

1. Ссылка на файл автозагрузчика с использованием оператора [require_once][require_once] и
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

> [AZURE.NOTE] В этом примере (и других примерах в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure через Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо добавить ссылку на файл автозагрузчика `WindowsAzure.php`.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


В приведенных ниже примерах всегда будет отображаться оператор `require_once`, однако обращение будет осуществляться только к классам, которые необходимы для выполнения этого примера.

## Настройка подключения к хранилищу Azure

Для создания клиента службы BLOB-объектов Azure необходимо сначала сформировать правильную строку подключения. Формат строки подключения к службе BLOB-объектов:

Для доступа к службе в режиме реального времени:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Для доступа к хранилищу эмулятора:

	UseDevelopmentStorage=true


Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

* передать строку подключения напрямую или
* использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:
	* по умолчанию предоставляется поддержка одного внешнего источника - переменных среды
	* можно добавить новые источники, расширив класс **ConnectionStringSource**.

В приведенных здесь примерах строка подключения передается напрямую.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## Практическое руководство. Создание контейнера

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
	
	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Вызов **setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS)** позволяет получить доступ к данным контейнера и больших двоичных объектов через анонимные запросы. Вызов **setPublicAccess(PublicAccessType::BLOBS_ONLY)** позволяет получить доступ через анонимные запросы только к данным BLOB-объектов. Дополнительные сведения о ACL контейнера см. в разделе [Определение ACL контейнера (REST API)][container-acl].

Дополнительные сведения о кодах ошибок службы BLOB-объектов см. в разделе [Коды ошибок службы BLOB-объектов][error-codes].

## Практическое руководство. Отправка BLOB-объекта в контейнер

Чтобы передать файл в виде большего двоичного объекта, используйте метод **BlobRestProxy->createBlockBlob**. Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует. В примере кода предполагается, что контейнер уже был создан и использует [fopen][fopen] для открытия файла в виде потока.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Обратите внимание, что в приведенном выше примере BLOB-объект передается в виде потока. Однако BLOB-объект может быть передан как строка, например с помощью функции [file_get_contents][file_get_contents]. Для этого замените $content = fopen("c:\myfile.txt", "r"); из приведенного выше примера на $content = filegetcontents("c:\myfile.txt");.

## Практическое руководство. Перечисление BLOB-объектов в контейнере

Чтобы получить список BLOB-объектов в контейнере, используйте метод **BlobRestProxy->listBlobs** с циклом **foreach** для перебора результатов. Следующий код выводит имя каждого BLOB-объекта в контейнере и соответствующий URI в браузере.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


## Практическое руководство. Загрузка BLOB-объектов

Чтобы загрузить BLOB-объект, вызовите метод **BlobRestProxy->getBlob**, затем вызовите метод **getContentStream** для результирующего объекта **GetBlobResult**.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Обратите внимание, что приведенный выше пример получает BLOB-объект как ресурс потока (поведение по умолчанию). Однако можно использовать функцию [stream_get_contents][stream-get-contents] для преобразования возвращенного потока в строку.

## Практическое руководство. Удаление BLOB-объекта

Чтобы удалить BLOB-объект, передайте имя контейнера и имя BLOB-объекта в **BlobRestProxy->deleteBlob**. 

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Практическое руководство. Удаление контейнера BLOB-объектов

Наконец, чтобы удалить контейнер BLOB-объектов, передайте имя контейнера в **BlobRestProxy->deleteContainer**.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Дальнейшие действия

Вы изучили основные сведения о службе BLOB-объектов Azure. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- См. справочник MSDN: [Хранилище Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Посетите [блог рабочей группы службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- См. пример блочного BLOB-объекта РНР по адресу: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- См. пример страничного BLOB-объекта PHP по адресу: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

[загрузка]: http://go.microsoft.com/fwlink/?LinkID=252473
[Хранение и доступ к данным в Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

<!--HONumber=49--> 