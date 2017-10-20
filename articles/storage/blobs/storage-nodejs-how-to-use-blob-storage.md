---
title: "Как использовать хранилище BLOB-объектов из Node.js | Документация Майкрософт"
description: "Хранение неструктурированных данных в облаке в хранилище BLOB-объектов Azure."
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8b0df222-1ca8-4967-8248-6d6d720947b8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: e52f38d5fb3c100e4275032f9a2a1234961c672b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-nodejs"></a>Использование хранилища больших двоичных объектов из Node.js
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Обзор
В этой статье описано, как реализовать типичные сценарии с использованием хранилища больших двоичных объектов. Примеры написаны с использованием интерфейса API Node.js. Рассмотренные сценарии включают загрузку, получение списка, загрузку и удаление больших двоичных объектов.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Создание приложения Node.js
Инструкции по созданию приложения Node.js см. в статьях [Создание веб-приложения Node.js в службе приложений Azure], [Сборка и развертывание приложения Node.js в облачной службе Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (с помощью Windows PowerShell) и [Создание и развертывание веб-приложения Node.js в Azure с использованием WebMatrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Настройка приложения для доступа к хранилищу
Для использования службы хранилища Azure необходим пакет SDK для службы хранилища Azure для Node.js, который содержит набор удобных библиотек, взаимодействующих со службами REST хранилища.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Использование диспетчера пакета Node (NPM) для получения пакета
1. В интерфейсе командной строки, например в **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой вы создали приложение.
2. Введите в командной строке **npm install azure-storage** . Результат выполнения этой команды будет аналогичен следующему примеру кода:

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. Выполнив команду **ls** вручную, можно убедиться, что папка **node\_modules** создана. В этом каталоге найдите пакет **azure-storage** , который содержит библиотеки, необходимые для доступа к хранилищу.

### <a name="import-the-package"></a>Импорт пакета
С помощью Блокнота или другого текстового редактора добавьте следующий код в начало файла **server.js** приложения, в котором планируется использовать хранилище.

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Настройка подключения к службе хранилища Azure
Модуль Azure считает переменные среды `AZURE_STORAGE_ACCOUNT` и `AZURE_STORAGE_ACCESS_KEY` или `AZURE_STORAGE_CONNECTION_STRING` для получения сведений, необходимых для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, при вызове **createBlobService**необходимо указать данные учетной записи.

## <a name="create-a-container"></a>Создание контейнера
Объект **BlobService** позволяет работать с контейнерами и большими двоичными объектами. Указанный ниже код создает объект **BlobService** . Добавьте следующий код в начало файла **server.js**:

```nodejs
var blobSvc = azure.createBlobService();
```

> [!NOTE]
> Для анонимного доступа к большому двоичному объекту воспользуйтесь **createBlobServiceAnonymous** и укажите адрес узла. Например, воспользуйтесь `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Чтобы создать новый контейнер, используйте **createContainerIfNotExists**. Код в следующем примере создает новый контейнер с именем mycontainer.

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
    if(!error){
      // Container exists and is private
    }
});
```

Если контейнер только что создан, `result.created` имеет значение true. Если контейнер уже существует, `result.created` будет иметь значение false. `response` будет содержать сведения об операции, включая сведения ETag для контейнера.

### <a name="container-security"></a>Безопасность контейнера
По умолчанию все контейнеры частные, и доступ к ним не может быть анонимным. Чтобы сделать контейнер общедоступным (чтобы к нему можно было подключаться анонимно), установите для уровня доступа к контейнеру значение **blob** или **container**.

* **blob** можно осуществлять анонимный доступ для чтения к содержимому и метаданным больших двоичных объектов внутри этого контейнера, но не к метаданным контейнера, например для перечисления всех больших двоичных объектов в контейнере.
* **container** разрешается анонимный доступ на чтение содержимого и метаданных больших двоичных объектов, а также метаданных контейнера.

В следующем примере кода показана установка уровня доступа **blob**:

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
    if(!error){
      // Container exists and allows
      // anonymous read access to blob
      // content and metadata within this container
    }
});
```

Кроме того, можно изменить уровень доступа к контейнеру, используя метод **setContainerAcl** для указания уровня доступа. В следующем примере кода уровень доступа изменяется на "container":

```nodejs
blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
  if(!error){
    // Container access level set to 'container'
  }
});
```

Результат содержит сведения об операции, включая текущий **ETag** для контейнера.

### <a name="filters"></a>Фильтры
С помощью **BlobService** к выполняемым операциям можно применить дополнительную фильтрацию. К операциям фильтрации могут относиться ведение журнала, автоматический повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

```nodejs
function handle (requestOptions, next)
```

Выполнив предварительную обработку параметров запроса, метод должен вызвать next, передав ему обратный вызов со следующей сигнатурой:

```nodejs
function (returnObject, finalCallback, next)
```

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В пакет SDK Azure для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter**. Следующий код создает объект **BlobService**, использующий фильтр **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var blobSvc = azure.createBlobService().withFilter(retryOperations);
```

## <a name="upload-a-blob-into-a-container"></a>Отправка BLOB-объекта в контейнер
Существует три типа больших двоичных объектов: блочные, страничные и добавочные. Блочные BLOB-объекты позволяют более эффективно отправлять данные большого размера. Добавочные BLOB-объекты оптимизированы для операций добавления. Страничные BLOB-объекты оптимизированы для операций чтения и записи. Дополнительные сведения см. в статье [Understanding Block Blobs, Append Blobs, and Page Blobs](http://msdn.microsoft.com/library/azure/ee691964.aspx) (Основные сведения о блочных, добавочных и страничных BLOB-объектах).

### <a name="block-blobs"></a>Blob-блоки
Чтобы загрузить данные в blob-блок, используйте следующие команды:

* **createBlockBlobFromLocalFile** — создает новый blob-блок и отправляет содержимое файла;
* **createBlockBlobFromStream** — создает новый блочный BLOB-объект и загружает содержимое потока
* **createBlockBlobFromText** — создает новый блочный BLOB-объект и загружает содержимое строки
* **createWriteStreamToBlockBlob** — предоставляет поток записи в блочный BLOB-объект.

В следующем примере кода содержимое файла **test.txt** передается в **myblob**.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

Возвращаемое этими методами `result` содержит сведения об операции, такие как **ETag** большого двоичного объекта.

### <a name="append-blobs"></a>Добавочные BLOB-объекты
Для отправки данных в новый добавочный BLOB-объект используйте указанные далее команды.

* **createAppendBlobFromLocalFile** — создает добавочный BLOB-объект и передает содержимое файла.
* **createAppendBlobFromText** — создает добавочный BLOB-объект и передает содержимое потока.
* **createAppendBlobFromStream** — создает добавочный BLOB-объект и передает содержимое строки.
* **createWriteStreamToNewAppendBlob** — создает добавочный BLOB-объект, а затем предоставляет поток для записи в него.

В следующем примере кода содержимое файла **test.txt** передается в **myappendblob**.

```nodejs
blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

Чтобы добавить блок в существующий добавочный BLOB-объект, используйте указанные далее команды:

* **appendFromLocalFile** — добавляет содержимое файла в существующий добавочный BLOB-объект.
* **appendFromStream** — добавляет содержимое потока в существующий добавочный BLOB-объект.
* **appendFromText** — добавляет содержимое строки в существующий добавочный BLOB-объект.
* **appendBlockFromStream** — добавляет содержимое потока в существующий добавочный BLOB-объект.
* **appendBlockFromText** — добавляет содержимое строки в существующий добавочный BLOB-объект.

> [!NOTE]
> Чтобы исключить ненужные вызовы сервера, интерфейсы API appendFromXXX будут выполнять проверку на стороне клиента с быстрым прекращением. appendBlockFromXXX не будут делать этого.
>
>

В следующем примере кода содержимое файла **test.txt** передается в **myappendblob**.

```nodejs
blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
  if(!error){
    // text appended
  }
});
```

### <a name="page-blobs"></a>Blob-страницы
Чтобы загрузить данные в blob-страницу, используйте следующие команды:

* **createPageBlob** — создает новый страничный BLOB-объект заданной длины.
* **createPageBlobFromLocalFile** — создает новый страничный BLOB-объект и загружает содержимое файла.
* **createPageBlobFromStream** — создает новый страничный BLOB-объект и загружает содержимое потока
* **createWriteStreamToExistingPageBlob** -предоставляет поток записи существующий страничный BLOB-объект
* **createWriteStreamToNewPageBlob** — создает страничный BLOB-объект, а затем предоставляет поток для записи в него.

В следующем примере кода содержимое файла **test.txt** передается в **mypageblob**.

```nodejs
blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

> [!NOTE]
> Страничные Вlob-объекты состоят из 512-байтовых "страниц". При отправке файлов с размером, не кратным 512, может возникать ошибка.
>
>

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере
Чтобы перечислить blob-объекты в контейнере, используйте метод **listBlobsSegmented** . Если вы хотите возвращать большие двоичные объекты с заданном префиксом, используйте **listBlobsSegmentedWithPrefix**.

```nodejs
blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
  if(!error){
      // result.entries contains the entries
      // If not all blobs were returned, result.continuationToken has the continuation token.
  }
});
```

`result` содержит коллекцию `entries`, которая представляет собой массив объектов, описывающих каждый из больших двоичных объектов. Если все большие двоичные объекты возвратить нельзя, `result` также предоставляет `continuationToken`, который можно использовать в качестве второго параметра для извлечения дополнительных записей.

## <a name="download-blobs"></a>Скачивание больших двоичных объектов
Чтобы загрузить данные из BLOB-объекта, используйте следующие команды:

* **getBlobToLocalFile** — записывает содержимое большого двоичного объекта в файл.
* **getBlobToStream** — записывает содержимое большого двоичного объекта в поток
* **getBlobToText** — записывает содержимое большого двоичного объекта в строку.
* **createReadStream** - обеспечивает поток для чтения из blob-объекта

В следующем примере кода **getBlobToStream** используется для скачивания содержимого большого двоичного объекта **myblob** и его сохранения в файле **output.txt** с помощью потока.

```nodejs
var fs = require('fs');
blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
  if(!error){
    // blob retrieved
  }
});
```

`result` содержит сведения о большом двоичном объекте, включая сведения **ETag** .

## <a name="delete-a-blob"></a>Удаление большого двоичного объекта
Наконец, чтобы удалить BLOB-объект, вызовите **deleteBlob**. Следующий пример кода удаляет большой двоичный объект с именем **myblob**.

```nodejs
blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
  if(!error){
    // Blob has been deleted
  }
});
```

## <a name="concurrent-access"></a>Одновременный доступ
Чтобы реализовать одновременный доступ нескольких клиентов или экземпляров процесса к BLOB-объекту, можно использовать **ETags** или **lease**.

* **Etag** — позволяет обнаружить, что BLOB-объект или контейнер были изменены другим процессом
* **Lease** — аренда позволяет получить монопольный обновляемый доступ к BLOB-объекту на запись или удаление в течение заданного периода времени.

### <a name="etag"></a>ETag
ETag необходимо использовать, если нужно обеспечить возможность записи в блочный BLOB-объект или страничный BLOB-объект одновременно для нескольких клиентов или экземпляров. ETag позволяет определить, были ли контейнер или BLOB-объект изменены с момента изначального чтения или создания. Это позволяет избежать перезаписи изменений, выполненных другим клиентом или процессом.

Условия ETag можно задать с помощью необязательного параметра `options.accessConditions` . В следующем примере кода текстовый файл **test.txt** передается только в том случае, если большой двоичный объект уже существует, а значение ETag для него соответствует содержащемуся в `etagToMatch`.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
    if(!error){
    // file uploaded
  }
});
```

Общая схема действий при использовании Etag такова:

1. Получить ETag в результате операции создания перечисления или получения
2. Выполнить действие по проверке отсутствия изменений в значении ETag.

Если значение было изменено, это означает, что другой клиент или экземпляр изменил BLOB-объект или контейнер с момента получения значения ETag.

### <a name="lease"></a>Lease
Для приобретения новой аренды можно воспользоваться методом **acquireLease** , указав большой двоичный объект или контейнер, для которого вы хотите получить аренду. Например, следующий код получает аренду для **myblob**.

```nodejs
blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
  if(!error) {
    console.log('leaseId: ' + result.id);
  }
});
```

В последующих операциях с **myblob** необходимо указывать параметр `options.leaseId`. Идентификатор аренды возвращается методом **acquireLease** в виде `result.id`.

> [!NOTE]
> По умолчанию продолжительность аренды бесконечна. В параметре `options.leaseDuration` можно указать конечную продолжительность аренды (от 15 до 60 секунд).
>
>

Чтобы удалить аренду, используйте **releaseLease**. Чтобы приостановить аренду, но не дать другим возможность получить новую аренду до истечения срока действия первоначальной аренды, используйте **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Работа с подписями общего доступа
Подписанный URL-адрес (SAS) — безопасный способ предоставить детализированный доступ к большим двоичным объектам и контейнерам без указания имени или ключей своей учетной записи хранения. Подписанные URL-адреса часто используются для предоставления ограниченного доступа к данным, например доступа к BLOB-объектам для мобильного приложения.

> [!NOTE]
> Несмотря на то что анонимный доступ к большим двоичным объектам также возможен, подписанный URL-адрес позволяет более точно управлять доступом, так как этот адрес нужно создать.
>
>

Надежное приложение, например облачная служба, создает подписанный URL-адрес с помощью метода **generateSharedAccessSignature** из **BlobService** и передает этот адрес ненадежному или частично надежному приложению, например мобильному приложению. Подписанный URL-адрес создается с использованием политики, которая описывает даты начала и окончания срока действия адреса, а также уровень доступа, который предоставляется держателю адреса.

В следующем примере кода создается новая политика общего доступа, которая позволяет держателю подписанного URL-адреса выполнять операции чтения BLOB-объекта **myblob** в течение 100 минут с момента создания политики.

```nodejs
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
```

Обратите внимание, что также необходимо предоставить информацию об узле, так как она требуется держателю SAS для доступа к контейнеру.

Затем клиентское приложение выполняет операции с BLOB-объектом, используя метод **BlobServiceWithSAS** с подписанным URL-адресом. Следующая команда позволяет получить информацию о **myblob**.

```nodejs
var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
  if(!error) {
    // retrieved info
  }
});
```

Так как подписанные URL-адреса были созданы только для доступа на чтение, при попытке изменения BLOB-объекта будет возвращена ошибка.

### <a name="access-control-lists"></a>Доступ к спискам управления
Для задания политики доступа для SAS также можно использовать список управления доступом (ACL). Это может быть удобно, когда необходимо предоставить доступ к контейнеру нескольким клиентам с различной политикой доступа для каждого из них.

ACL реализуется с помощью массива политик доступа, каждая из которых связана со своим идентификатором. В следующем примере кода определяются две политики: одна для пользователя 'user1', вторая — для 'user2'.

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

В следующем примере код получает текущий список управления доступом для **mycontainer**, а затем добавляет новые политики с помощью **setBlobAcl**. Такой подход допускает выполнение:

```nodejs
var extend = require('extend');
blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

После задания списка управления доступом можно создать подписанный URL-адрес на основе идентификатора политики. В следующем примере кода создаются новые подписанные URL-адреса для пользователя user2:

```nodejs
blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });
```

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительных сведений см. следующие ресурсы.

* [Справочник по пакету SDK службы хранилища Azure для API Node][Справочник по пакету SDK службы хранилища Azure для API Node]  
* [Блог рабочей группы службы хранилища Azure][Блог рабочей группы службы хранилища Azure]  
* Репозиторий [пакета SDK хранилища Azure для Node][Azure Storage SDK for Node] на веб-сайте GitHub.  
* [Центр разработчиков Node.js.](https://azure.microsoft.com/develop/nodejs/)  
* [Приступая к работе со служебной программой командной строки AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)  

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node  

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/  
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx  
[Azure portal]: https://portal.azure.com  
[Построение и развертывание приложения Node.js в облачной службе Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)  
[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/  
[Справочник по пакету SDK службы хранилища Azure для API Node]: http://dl.windowsazure.com/nodestoragedocs/index.html  
