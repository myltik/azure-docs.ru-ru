<properties urlDisplayName="Blob Service" pageTitle="Как использовать хранилище больших двоичных объектов (Node.js) - Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="nodejs" title="How to Use the Blob Service from Node.js" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />





# Использование службы BLOB-объектов из Node.js

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны с использованием APINode.js. Здесь описаны такие сценарии, как **отправка**, **перечисление**,**скачивание** и **удаление** больших двоичных объектов. Дополнительную информацию о больших двоичных объектах
см. в разделе [Следующие шаги][].

## Оглавление

* [Что такое служба больших двоичных объектов?][]    
* [Основные понятия][]    
* [Создание учетной записи хранения Azure][]    
* [Создание приложения Node.js][]  
* [Настройка приложения для доступа к хранилищу][]     
* [Настройка строки подключения к службе хранилища Azure][]  
* [Практическое руководство. Создание контейнера][]  
* [Практическое руководство. Отправка большого двоичного объекта в контейнер][]  
* [Практическое руководство. Перечисление больших двоичных объектов в контейнере][]  
* [Практическое руководство. Скачивание больших двоичных объектов][]  
* [Практическое руководство. Удаление большого двоичного объекта][]  
* [Практическое руководство. Одновременный доступ][]     
* [Практическое руководство. Работа с подписанными URL-адресами][]     
* [Дальнейшие действия][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Создание учетной записи хранения Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Создание приложения Node.js

Создайте пустое приложение Node.js. Указания по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][Node.js Cloud Service] (с помощью Windows PowerShell) или [Веб-сайт с WebMatrix].

## <a name="configure-access"> </a>Настройка приложения для доступа к хранилищу

Для использования службы хранилища Azure необходим пакет SDK для службы хранилища Azure для Node.js, который содержит набор удобных библиотек, взаимодействующих со службами REST хранилища.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, такой как **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.

2.  Введите в окне командной строки **npm install azure-storage**, что должно привести к следующему результату:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Можно вручную выполнить команду **ls**, чтобы убедиться в создании папки **node\_modules**. В этой папке находится пакет **azure-storage**, содержащий библиотеки, необходимые для доступа к хранилищу.

### Импорт пакета

Используйте Блокнот или другой текстовый редактор, чтобы добавить следующий код в начало файла **server.js** приложения, где планируется использовать хранилище.

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Настройка подключения к хранилищу Azure

Модуль Azure считывает переменные среды AZURE\_STORAGE\_ACCOUNT и AZURE\_STORAGE\_ACCESS\_KEY или AZURE\_STORAGE\_CONNECTION\_STRING для получения сведений, необходимых для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, при вызове **createBlobService** необходимо указать данные учетной записи.

Пример настройки переменных среды на портале управления для веб-сайта Azure см. в разделе [Веб-приложение Node.js с хранилищем]

## <a name="create-container"> </a>Практическое руководство: Создание контейнера

Объект **BlobService** позволяет работать с контейнерами и большими двоичными объектами. Пример:
Следующий код создает объект **BlobService**. Добавьте следующий код
в верхней части файла **server.js**:

    var blobSvc = azure.createBlobService();

> [WACOM.NOTE] Вы можете обеспечить анонимный доступ к большому двоичному объекту, используя **createBlobServiceAnonymous** и предоставив адрес узла. Например: var blobSvc = azure.createBlobService('https://myblob.blob.core.windows.net/');.

Все BLOB-объекты содержатся в контейнере. Чтобы создать новый контейнер, используйте **createContainerIfNotExists**. Следующий код создает новый контейнер с именем 'mycontainer'

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

Если контейнер создан, значение result будет истинно. Если контейнер уже существует, значение result будет ложно. response содержит данные об операции, включая информацию о [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) для контейнера.

###Безопасность контейнера

По умолчанию все контейнеры частные, и доступ к ним не может быть анонимным. Чтобы сделать контейнер открытым с возможностью анонимного доступа, можно установить значения уровня доступа контейнера, равными **blob** или **container**.

* **blob** - позволяет осуществлять анонимный доступ для чтения к содержимому и метаданным больших двоичных объектов внутри этого контейнера, но не к метаданным контейнера, например, для перечисления всех больших двоичных объектов в контейнере. 

* **container** - позволяет осуществлять анонимный доступ для чтения к содержимому и метаданным больших двоичных объектов, а также к метаданным контейнера. 

В следующем примере показана установка уровня доступа **blob**: 

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

Кроме того, можно изменить уровень доступа к контейнеру, используя метод **setContainerAcl** для указания уровня доступа. В следующем примере уровень доступа изменяется на "container":

    blobSvc.setContainerAcl('mycontainer', null, 'container', function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

Результат будет содержать информацию об операции, в том числе информацию **ETag** для контейнера.

###Фильтры

Дополнительные операции фильтрации можно применить к выполняемым операциям, используя **BlobService**. К операциям фильтрации могут относиться ведение журнала, автоматически повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

		function handle (requestOptions, next)

Выполнив предварительную обработку параметров запроса, метод должен вызвать "next", передавая обратный вызов со следующей сигнатурой:

		function (returnObject, finalCallback, next)

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В пакет SDK Azure для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter**. Следующий код создает объект **BlobService**, который использует **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-blob"> </a>Практическое руководство: Отправка BLOB-объекта в контейнер

Blob-объект может быть либо блочным, либо страничным. Blob-блоки позволяют более эффективно отправлять данные большого размера, в то время как blob-страницы оптимизированы для операция чтения/записи. Дополнительную информацию см. в разделе [Что такое блоки больших двоичных объектов и страницы больших двоичных объектов](http://msdn.microsoft.com/ru-ru/library/azure/ee691964.aspx).

###Blob-блоки

Чтобы загрузить данные в blob-блок, используйте следующие команды:

* **createBlockBlobFromLocalFile** - создает новый блок большого двоичного объекта и отправляет в него содержимое файла.

* **createBlockBlobFromStream** - создает новый блок большого двоичного объекта и отправляет в него содержимое потока.

* **createBlockBlobFromText** - создает новый блок большого двоичного объекта и отправляет в него содержимое строки.

* **createWriteStreamToBlockBlob** - обеспечивает поток записи в блок большого двоичного объекта.

В следующем примере содержимое файла **test.txt** передается в большой двоичный объект **myblob**.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

Возвращаемый данными операциями result будет содержать информацию об операциях, такую как **ETag** большого двоичного объекта.

###Blob-страницы

Чтобы загрузить данные в blob-страницу, используйте следующие команды:

* **createPageBlob** - создает новую страницу большого двоичного объекта определенной длины.

* **createPageBlobFromLocalFile** - создает новую страницу большого двоичного объекта и отправляет содержимое файла.

* **createPageBlobFromStream** - создает новую страницу большого двоичного объекта и отправляет содержимое потока.

* **createWriteStreamToExistingPageBlob** - создает поток записи для существующей странице большого двоичного объекта.

* **createWriteStreamToNewPageBlob** - создает новую страницу большого двоичного объекта, а затем предоставляет поток для записи на нее.

В следующем примере содержимое файла **test.txt** передается в большой двоичный объект **mypageblob**.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [WACOM.NOTE] Страницы больших двоичных объектов состоят из 512-байтовых "страниц". При отправке файлов с некратным 512 размером, может возникать ошибка.

## <a name="list-blob"> </a>Практическое руководство: Перечисление BLOB-объектов в контейнере

Чтобы перечислить большие двоичные объекты в контейнере, используйте метод **listBlobsSegmented**. При необходимости возвращать большие двоичные объекты с определенным префиксом, используйте метод **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

result будет содержать коллекцию, которая представляет собой массив объектов, описывающих каждый из больших двоичных объектов. Если не удается вернуть все большие двоичные объекты, result также предоставит continuationToken, который может использоваться как вторичный параметр для извлечения дополнительных сущностей.

## <a name="download-blob"> </a>Практическое руководство: Загрузка BLOB-объектов

Чтобы загрузить данные из BLOB-объекта, используйте следующие команды:

* **getBlobToFile** - записывает содержимое большого двоичного объекта в файл

* **getBlobToStream** - записывает содержимое большого двоичного объекта в поток.

* **getBlobToText** - записывает содержимое большого двоичного объекта в строку. 

* **createReadStream** - предоставляет поток для чтения из большого двоичного объекта

В следующем примере показано, как использовать метод **getBlobToStream**, чтобы скачать

    var fs=require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

result будет содержать информацию о большом двоичном объекте, в том числе и о **ETag**.

## <a name="delete-blob"> </a>Практическое руководство: Удаление BLOB-объекта

Наконец, чтобы удалить большой двоичный объект, вызовите метод **deleteBlob**. В следующем примере удаляется большой двоичный объект с именем **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

##<a name="concurrent-access"></a>Практическое руководство: Одновременный доступ

Чтобы обеспечить одновременный доступ к большому двоичному объекту от нескольких клиентов или экземпляров процесса, можно использовать элементы **ETag** или **Lease**.

* **Etag** - позволяет обнаружить, был ли большой двоичный объект или контейнер изменен другим процессом.

* **Lease** - дает возможность получить эксклюзивный, возобновляемый доступ к большому двоичному объекту для записи или удаления на определенный период времени.

###ETag

ETag необходимо использовать, если нужно обеспечить возможность записи в blob-объект одновременно для нескольких клиентов или экземпляров. ETag позволяет определить, был ли контейнер или blob-объект изменен после его начального чтения или создания. Благодаря этому можно избежать перезаписи изменений, сделанных другим клиентом или экземпляром.

Условия для ETag задаются дополнительным параметром options.accessConditions. В следующем примере файл **test.txt** будет отправляться только в том случае, если большой двоичный объект уже существует, а значение ETag задается параметром etagToMatch.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

Общая процедура использования ETags следующая:

1. Получить ETag в результате операции создания перечисления или получения

2. Выполнить действие по проверке отсутствия изменений в значении ETag.

Если значение было изменено это указывает, что другой клиент или экземпляр изменил blob-объект или контейнер после того, как вы получили значение ETag.

###Lease

Новая аренда может быть получена с помощью метода **acquireLease**, который определяет большой двоичный объект или контейнер для аренды. Например, следующий код получает аренду объект **myblob**.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log(result);
	  }
	});

Последующие операции над объектом **myblob** должны определять параметр options.leaseId. Идентификатор аренды возвращается в виде result.id из **acquireLease**.

> [WACOM.NOTE] По умолчанию продолжительность аренды не ограничена. Можно ограничить период аренды (от 15 до 60 секунд), задав параметр options.leaseDuration.

Чтобы удалить аренду, используйте метод **releaseLease**. Чтобы приостановить аренду, но не дать другим возможность получить новую аренду до истечения срока действия первоначальной аренды, используйте метод **breakLease**.

## <a name="sas"></a>Практическое руководство: Работа с подписями общего доступа

Подписанный URL-адрес - безопасный способ предоставить детализированный доступ к большим двоичным объектам и контейнерам без указания имени или ключей своей учетной записи хранения. SAS часто используется для предоставления ограниченного доступа к данным, например, доступа к blob-объектам для мобильного приложения.

> [WACOM.NOTE] Несмотря на возможность предоставления анонимного доступа к большим двоичным объектам, подписанный URL-адрес дает возможность более точно управлять доступом, так как вы должны создать подписанный URL-адрес.

Надежное приложение, например, облачная служба, создает подписанный URL-адрес с помощью метода **generateSharedAccessSignature** службы **BlobService** и предоставляет ее ненадежному или частично надежному приложению. Например, мобильному приложению. Подпись SAS создается с использованием политики, которая описывает даты начала и окончания срока действия SAS, а также уровень доступа, который предоставляется держателю подписи SAS.

В следующем примере создается новая общая политика, которая позволяет держателю подписанного URL-адреса выполнять операции чтения большого двоичного объекта **myblob** в течение 100 минут с момента своего создания.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	    
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};
	
	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

Обратите внимание, что также должна быть предоставлена информация узла, поскольку она требуется держателю SAS для совершения попыток доступа к контейнеру.

Клиентское приложение далее использует подписанный URL-адрес с помощью метода **BlobServiceWithSAS** для выполнения операций с большим двоичным объектом. Следующая команда позволяет получить информацию о **myblob**.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Поскольку подпись SAS была создана только для доступа чтения, если выполняется попытка изменить blob-объект, будет возвращена ошибка.

###Доступ к спискам управления

Можно также использовать список управления доступом (ACL) для задания политики доступа подписи SAS. Это может оказаться полезным, когда необходимо предоставить доступ к контейнеру нескольким клиентам, но с различной политикой доступа для каждого из них.

ACL реализуется с помощью массива политик доступа, каждая из которых связана со своим идентификатором. В следующем примере определяются две политики - для пользователей "user1" и "user2":

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

В этом примере код получает текущее значение ACL для **mycontainer**, а затем добавляет новые политики с помощью **setBlobAcl**. Такой подход допускает выполнение:

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

После задания ACL можно создать подпись SAS на основе идентификатора политики. В следующем примере создается новая подпись SAS для пользователя 'user2':

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"> </a>Дальнейшие действия

Вы получили основную информацию об управлении большими двоичными объектами хранилища. Перейдите по следующим ссылкам,
чтобы узнать о том, как выполнять более сложные задачи хранения.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][].
-   Посетите блог [группы разработчиков службы хранилища Azure][].
-   Посетите репозиторий [Пакет SDK для службы хранилища Azure для Node][] на веб-сайте GitHub.

  [Пакет SDK для Azure для Node]: https://github.com/Azure/azure-storage-node
  [Дальнейшие действия]: #next-steps
  [Что такое служба больших двоичных объектов?]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Создание приложения Node.js]: #create-app
  [Настройка приложения для доступа к хранилищу]: #configure-access
  [Настройка строки подключения к службе хранилища Azure]: #setup-connection-string
  [Практическое руководство. Создание контейнера]: #create-container
  [Практическое руководство. Отправка большого двоичного объекта в контейнер]: #upload-blob
  [Практическое руководство. Перечисление больших двоичных объектов в контейнере]: #list-blob
  [Практическое руководство. Скачивание больших двоичных объектов]: #download-blobs
  [Практическое руководство. Удаление большого двоичного объекта]: #delete-blobs
  [Практическое руководство. Одновременный доступ]: #concurrent-access
  [Практическое руководство. Работа с подписанными URL-адресами]: #sas
[Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [Облачная служба Node.js с хранилищем]: /ru-ru/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Веб-приложение Node.js с хранилищем]: /ru-ru/documentation/articles/storage-nodejs-use-table-storage-web-site/
 [Веб-сайт с WebMatrix]: /ru-ru/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Использование REST API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh264518.aspx
  [Портал управления Azure]: http://manage.windowsazure.com
  [Облачная служба Node.js]: /ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Блог команды разработчиков службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35.1-->
