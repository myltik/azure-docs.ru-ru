<properties linkid="dev-nodejs-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Blob Service from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Использование службы BLOB-объектов из Node.js

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны с помощью Node.jsAPI. Здесь описаны такие сценарии, как **отправка**, **перечисление**, **загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о  BLOB-объектах см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое служба BLOB-объектов?][Что такое служба BLOB-объектов?]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]
-   [Создание приложения Node.js][Создание приложения Node.js]
-   [Настройка приложения для доступа к хранилищу][Настройка приложения для доступа к хранилищу]
-   [Настройка строки подключения к хранилищу Azure][Настройка строки подключения к хранилищу Azure]
-   [Практическое руководство. Создание контейнера][Практическое руководство. Создание контейнера]
-   [Практическое руководство. Отправка BLOB-объекта в контейнер][Практическое руководство. Отправка BLOB-объекта в контейнер]
-   [Практическое руководство. Перечисление BLOB-объектов в контейнере][Практическое руководство. Перечисление BLOB-объектов в контейнере]
-   [Практическое руководство. Загрузка BLOB-объектов][Практическое руководство. Загрузка BLOB-объектов]
-   [Практическое руководство. Удаление BLOB-объекта][Практическое руководство. Удаление BLOB-объекта]
-   [Практическое руководство. Одновременный доступ][Практическое руководство. Одновременный доступ]
-   [Практическое руководство. Работа с подписями общего доступа][Практическое руководство. Работа с подписями общего доступа]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-блоб-объект-хранилище](../includes/howto-blob-storage.md)]

## <a name="create-account"></a>Создайте учетную запись хранения Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Создание приложения Node.js

Создайте пустое приложение Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure][Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][Облачная служба Node.js] (с помощью Windows PowerShell) или [Веб-сайт с WebMatrix][Веб-сайт с WebMatrix].

## <a name="configure-access"> </a>Настройка приложения для доступа к хранилищу

Для использования хранилища Azure необходимо загрузить и использовать пакет Azure Storage SDK для Node.js, который содержит набор удобных библиотек, взаимодействующих со службами REST хранилища.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, такой как **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.

2.  Введите в командной строке **npm install azure-storage**, что должно
    привести к следующему результату:

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

Модуль Azure считывает переменные среды AZURE\\\_STORAGE\\\_ACCOUNT и AZURE\\\_STORAGE\\\_ACCESS\\\_KEY или AZURE\_STORAGE\_CONNECTION\_STRING для получения сведений, необходимых для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, при вызове **createBlobService** необходимо указать данные учетной записи.

Пример настройки переменных среды на портале управления для веб-сайта Azure см. в статье [Веб-приложение Node.js с хранилищем][Веб-приложение Node.js с хранилищем]

## <a name="create-container"> </a>Практическое руководство. Создание контейнера

Объект **BlobService** позволяет работать с контейнерами и BLOB-объектами. Следующий код создает объект **BlobService**. Добавьте следующий код в начало **server.js**:

    var blobSvc = azure.createBlobService();

> [WACOM.NOTE] Можно обеспечить анонимный доступ к blob-объекту, используя **createBlobServiceAnonymous** и предоставив адрес узла. Например, `var blobSvc = azure.createBlobService('https://myblob.blob.core.windows.net/');`.

Все BLOB-объекты содержатся в контейнере. Чтобы создать новый контейнер, используйте **createContainerIfNotExists**. Следующий код создает новый контейнер с именем 'mycontainer'

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
    });

Если контейнер создан, значение `result` истинно. Если контейнер уже существует, значение `result` будет ложным. `response` будет содержать сведения об операции, включая сведения [ETag][ETag] для контейнера.

### Безопасность контейнера

По умолчанию все контейнеры частные, и доступ к ним не может быть анонимным. Чтобы сделать контейнер открытым с возможностью анонимного доступа, можно установить значения уровня доступа контейнера, равными **blob** или **container**.

-   При установке уровня доступа **blob** можно осуществлять анонимный доступ для чтения к содержимому и метаданным BLOB-объектов внутри этого контейнера, но не к метаданным контейнера, например, для перечисления всех BLOB-объектов в контейнере.

-   При установке уровня доступа **container** можно осуществлять анонимный доступ для чтения к содержимому и метаданным BLOB-объектов, а также к метаданным контейнера.

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

будет содержать информацию об операции, в том числе информацию **ETag** для контейнера.

### Фильтры

Используя **QueueService**, к выполняемым операциям можно применить дополнительные операции фильтрации. К операциям фильтрации могут относиться ведение журнала, автоматически повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

        function handle (requestOptions, next)

Выполнив предварительную обработку параметров запроса, метод должен вызвать "next", передавая обратный вызов со следующей сигнатурой:

        function (returnObject, finalCallback, next)

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В Azure SDK для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter** Следующий код создает объект **BlobService**, использующий **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-blob"> </a>Практическое руководство. Отправка BLOB-объекта в контейнер

Blob-объект может быть либо блочным, либо страничным. Blob-блоки позволяют более эффективно отправлять данные большого размера, в то время как blob-страницы оптимизированы для операция чтения/записи. Дополнительную информацию см. в разделе [Что такое BLOB-блоки и BLOB-страницы][Что такое BLOB-блоки и BLOB-страницы].

### Blob-блоки

Чтобы загрузить данные в blob-блок, используйте следующие команды:

-   **createBlockBlobFromLocalFile** - создает новый blob-блок и отправляет содержимое файла.

-   **createBlockBlobFromStream** - создает новый blob-блок и отправляет содержимое потока.

-   **createBlockBlobFromText** - создает новый blob-блок и отправляет содержимое строки.

-   **createWriteStreamToBlockBlob** - обеспечивает поток записи в blob-блок.

В следующем примере содержимое файла **test.txt** передается в BLOB-объект **myblob**.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

`result`, возвращаемый этими методами, будет содержать информацию об операции, такую как **ETag** BLOB-объекта.

### Blob-страницы

Чтобы загрузить данные в blob-страницу, используйте следующие команды:

-   **createPageBlob** - создает новую blob-страницу определенной длины.

-   **createPageBlobFromFile** - создает новую blob-страницу и отправляет содержимое файла.

-   **createPageBlobFromStream** - создает новую blob-страницу и отправляет содержимое потока.

-   **createWriteStreamToExistingPageBlob** - создает поток записи к существующей blob-странице.

-   **createWriteStreamToNewPageBlob** - создает новую blob-страницу, а затем обеспечивает поток для записи на нее.

В следующем примере содержимое файла **test.txt** передается в BLOB-объект **mypageblob**.

    blobSvc.createPageBlobFromFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [WACOM.NOTE] Вlob-страницы состоят из 512-байтовых 'страниц'. При отправке файлов с некратным 512 размером, может возникать ошибка.

## <a name="list-blob"> </a>Практическое руководство. Перечисление BLOB-объектов в контейнере

Чтобы перечислить blob-объекты в контейнере, используйте метод **listBlobsSegmented**. При необходимости возвращать blob-объекты с определенным префиксом, используйте метод **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
      }
    });

`result` будет содержать коллекцию `entries`, которая представляет собой массив объектов, описывающих каждый BLOB-объект. Если не удается вернуть все BLOB-объекты, `result` также предоставит `continuationToken`, который может использоваться как вторичный параметр для извлечения дополнительных сущностей.

## <a name="download-blob"> </a>Практическое руководство. Загрузка BLOB-объектов

Чтобы загрузить данные из BLOB-объекта, используйте следующие команды:

-   **getBlobToFile** - записывает содержимое blob-объекта в файл

-   **getBlobToStream** - записывает содержимое blob-объекта в поток.

-   **getBlobToText** - записывает содержимое blob-объекта в строку.

-   **createReadStream** - обеспечивает поток для чтения из blob-объекта

В следующем примере показано, как использовать метод **getBlobToStream**, чтобы загрузить содержимое BLOB-объекта **myblob** и сохранить его в файл **output.txt** с помощью потока:

    var fs=require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

`result` будет содержать информацию о BLOB-объекте, в том числе **ETag**.

## <a name="delete-blob"> </a>Практическое руководство. Удаление BLOB-объекта

Наконец, чтобы удалить BLOB-объект, вызовите **deleteBlob**. В следующем примере удаляется BLOB-объект с именем **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## <a name="concurrent-access"></a>Практическое руководство. Одновременный доступ

Чтобы поддержать одновременный доступ к blob-объекту от нескольких клиентов или экземпляров процесса, можно использовать **ETags** или **leases**.

-   **Etag** - позволяет обнаружить, был ли blob-объект или контейнер изменен другим процессом.

-   **Lease** - дает возможность получить эксклюзивный, повторяемый доступ к blob-объекту для записи или удаления на определенный период времени.

### ETag

ETag необходимо использовать, если нужно обеспечить возможность записи в blob-объект одновременно для нескольких клиентов или экземпляров. ETag позволяет определить, был ли контейнер или blob-объект изменен после его начального чтения или создания. Благодаря этому можно избежать перезаписи изменений, сделанных другим клиентом или экземпляром.

Условия для ETag задаются дополнительным параметром `options.accessConditions`. В следующем пример файл **test.txt** будет отправляться только в том случае, если blob-объект уже существует, а значение ETag задается параметром `etagToMatch`.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Общая процедура использования ETags следующая:

1.  Получить ETag в результате операции создания перечисления или получения

2.  Выполнить действие по проверке отсутствия изменений в значении ETag.

Если значение было изменено это указывает, что другой клиент или экземпляр изменил blob-объект или контейнер после того, как вы получили значение ETag.

### Lease

Новая аренда (lease) может быть получена с помощью метода **acquireLease**, который определяет блоб-объект или контейнер для аренды. Например, следующий код получает аренду **myblob**.

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log(result);
      }
    });

Последующие операции в **myblob** должен определять параметр `options.leaseId`. Идентификатор аренды возвращается как `result.id` из **acquireLease**.

> [WACOM.NOTE] По умолчанию продолжительность аренды бесконечна. Можно задать ограниченный период аренды (между 15 и 60 секундами), задав параметр `options.leaseDuration`.

Чтобы удалить аренду, используйте **releaseLease**. Чтобы приостановить аренду, но не дать другим возможность получить новую аренду до истечения срока действия первоначальной аренды, используйте **breakLease**.

## <a name="sas"></a>Практическое руководство. Работа с подписями общего доступа

Подписи общего доступа (SAS) — безопасный способ предоставить детальный доступ к blob-объектам и контейнерам без указания имени или ключей своей учетной записи хранения. SAS часто используется для предоставления ограниченного доступа к данным, например, доступа к blob-объектам для мобильного приложения.

> [WACOM.NOTE] Хотя можно также предоставлять анонимный доступ к blob-объектам, SAS обеспечивает возможность предоставления более управляемого доступа, поскольку подпись SAS необходимо создать.

Надежное приложение, например, облачная служба, создает подпись SAS с помощью **generateSharedAccessSignature** службы **BlobService** и предоставляет ее ненадежному или полунадежному приложению. Например, мобильному приложению. Подпись SAS создается с использованием политики, которая описывает даты начала и окончания срока действия SAS, а также уровень доступа, который предоставляется держателю подписи SAS.

В следующем примере создается новая общая политика, которая позволяет держателю подписи SAS выполнять операции чтения blob-объекта **myblob**, в течение 100 минут с момента своего создания.

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

Клиентское приложение далее использует подпись SAS с помощью **BlobServiceWithSAS** для выполнения операций с blob-объектом. Следующая команда позволяет получить информацию о **myblob**.

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

Поскольку подпись SAS была создана только для доступа чтения, если выполняется попытка изменить blob-объект, будет возвращена ошибка.

### Доступ к спискам управления

Можно также использовать список управления доступом (ACL) для задания политики доступа подписи SAS. Это может оказаться полезным, когда необходимо предоставить доступ к контейнеру нескольким клиентам, но с различной политикой доступа для каждого из них.

ACL реализуется с помощью массива политик доступа, каждая из которых связана со своим идентификатором. В следующем примере определяются две политики — для пользователей 'user1' и 'user2':

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

## <a name="next-steps"> </a> Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][Хранение данных и доступ к ним в Azure].
-   Посетите [блог команды разработчиков хранилища Azure][блог команды разработчиков хранилища Azure].
-   Посетите репозиторий [Пакет SDK хранилища Azure для Node][Пакет SDK хранилища Azure для Node] на веб-сайте GitHub.

  [Дальнейшие действия]: #next-steps
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Создание приложения Node.js]: #create-app
  [Настройка приложения для доступа к хранилищу]: #configure-access
  [Настройка строки подключения к хранилищу Azure]: #setup-connection-string
  [Практическое руководство. Создание контейнера]: #create-container
  [Практическое руководство. Отправка BLOB-объекта в контейнер]: #upload-blob
  [Практическое руководство. Перечисление BLOB-объектов в контейнере]: #list-blob
  [Практическое руководство. Загрузка BLOB-объектов]: #download-blobs
  [Практическое руководство. Удаление BLOB-объекта]: #delete-blobs
  [Практическое руководство. Одновременный доступ]: #concurrent-access
  [Практическое руководство. Работа с подписями общего доступа]: #sas
  [руководство-блоб-объект-хранилище]: ../includes/howto-blob-storage.md
  [создание-хранилище-учетная запись]: ../includes/create-storage-account.md
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [Облачная служба Node.js]: /ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Веб-сайт с WebMatrix]: /ru-ru/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Веб-приложение Node.js с хранилищем]: /ru-ru/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [ETag]: http://en.wikipedia.org/wiki/HTTP_ETag
  [Что такое BLOB-блоки и BLOB-страницы]: http://msdn.microsoft.com/ru-ru/library/azure/ee691964.aspx
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Пакет SDK хранилища Azure для Node]: https://github.com/Azure/azure-storage-node
