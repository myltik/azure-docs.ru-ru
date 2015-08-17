<properties
	pageTitle="Как использовать хранилище больших двоичных объектов из PHP | Microsoft Azure"
	description="Узнайте, как отправлять, перечислять, передавать и удалять большие двоичные объекты с помощью службы BLOB-объектов Azure. Примеры кода написаны на PHP."
	documentationCenter="php"
	services="storage"
	authors="tfitzmac"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="tomfitz"/>

# Использование хранилища больших двоичных объектов из PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны на PHP и используют [пакет Azure SDK для PHP][download]. Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** больших двоичных объектов. Дополнительную информацию о больших двоичных объектах см. в разделе [Дополнительная информация](#NextSteps).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание приложения PHP

Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является ссылка на классы в пакете SDK для Azure для PHP непосредственно из кода. Можно использовать любые средства разработки для создания приложения, включая программу "Блокнот".

В этом руководстве используются компоненты службы, которые могут быть вызваны локально в приложении PHP или в коде, работающем в веб-роли, рабочей роли или на веб-сайте Azure.

## Получение клиентских библиотек Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Настройка приложения для доступа к службе BLOB-объектов

Чтобы использовать интерфейсы API службы BLOB-объектов Azure, требуется:

1. Ссылка на файл автозагрузчика с использованием инструкции [require\_once][require_once] и
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder**.

> [AZURE.NOTE]В этом примере (и других примерах в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure через Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо добавить ссылку на файл автозагрузчика `WindowsAzure.php`.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


В приведенных ниже примерах всегда будет отображаться оператор `require_once`, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

## Настройка подключения к службе хранилища Azure

Чтобы создать экземпляр клиента службы BLOB-объектов Azure, сначала необходимо сформировать правильную строку подключения. Формат строки подключения к службе BLOB-объектов:

Для доступа к службе в режиме реального времени:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Для доступа к эмулятору хранения:

	UseDevelopmentStorage=true


Чтобы создать клиент службы Azure, необходимо использовать класс **ServicesBuilder**. Вы можете:

* передать строку подключения напрямую или
* использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:
	* по умолчанию предоставляется поддержка одного внешнего источника — переменных среды;
	* можно добавить новые источники, расширив класс **ConnectionStringSource**.

В приведенных здесь примерах строка подключения передается напрямую.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## Создание контейнера

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Объект **BlobRestProxy** позволяет создать контейнер BLOB-объектов с помощью метода **createContainer**. При создании контейнера можно задать параметры контейнера, однако это не является обязательным. (В приведенном ниже примере показано, как задать список управления доступом (ACL) и метаданные контейнера.)

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

	// Set container metadata.
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

Вызов **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** делает контейнер и данные BLOB-объектов доступными через анонимные запросы. Вызов **setPublicAccess(PublicAccessType::BLOBS\_ONLY)** делает доступными через анонимные запросы только данные BLOB-объектов. Дополнительную информацию об ACL контейнера см. в разделе [Задание списка управления доступом для контейнера (REST API)][container-acl].

Дополнительные сведения о кодах ошибок службы Blob-объектов см. в разделе [Коды ошибок службы Blob-объектов][error-codes].

## Отправка BLOB-объекта в контейнер

Чтобы передать файл в виде BLOB-объекта, используйте метод **BlobRestProxy->createBlockBlob**. Эта операция создает большой двоичный объект, если он еще не существует, или заменяет его, если он существует. В примере кода предполагается, что контейнер уже был создан и использует [fopen][fopen] для открытия файла в виде потока.

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

Обратите внимание, что в предыдущем примере большой двоичный объект передается в виде потока. Однако BLOB-объект может быть передан как строка с помощью, к примеру, функции [file\_get\_contents][file_get_contents]. Чтобы сделать это, используя предыдущий пример, измените `$content = fopen("c:\myfile.txt", "r");` на `$content = file_get_contents("c:\myfile.txt");`.

## Перечисление BLOB-объектов в контейнере

Чтобы получить список BLOB-объектов в контейнере, используйте метод **BlobRestProxy->listBlobs** с циклом **foreach** для перебора результатов. Следующий код выводит имя каждого большого двоичного объекта в контейнере и соответствующий URI в браузере.

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


## Загрузка BLOB-объектов

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

Обратите внимание, что приведенный выше пример получает BLOB-объект как ресурс потока (поведение по умолчанию). Однако можно использовать функцию [stream\_get\_contents][stream-get-contents] для преобразования возвращенного потока в строку.

## Удаление большого двоичного объекта

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

## Удаление контейнера blob-объектов

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

Вы изучили основную информацию о службе BLOB-объектов Azure. Дополнительную информацию о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- См. справочник MSDN: [Хранилище Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Посетите [блог команды разработчиков службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- См. пример блочного BLOB-объекта РНР по адресу: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- См. пример страничного BLOB-объекта PHP по адресу: <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Storing and accessing data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

<!---HONumber=August15_HO6-->