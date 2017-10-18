---
title: "Приступая к работе со средствами разработки хранилища Azure Stack"
description: "Руководство по началу работы со средствами разработки хранилища Azure Stack"
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 9/25/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.openlocfilehash: 5b2898c64c0f1b5d804e63fa4e4e1218fa7a672c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Приступая к работе со средствами разработки хранилища Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*


Microsoft Azure Stack предоставляет набор служб хранилища, включая хранилище BLOB-объектов, таблиц и очередей Azure.

В этой статье содержится краткое руководство о том, как приступить к использованию средств разработки для хранилища Azure Stack. Более подробные сведения и пример кода можно найти в соответствующих руководствах по службе хранилища Azure.

Существуют различия между службой хранилища Azure и хранилищем Azure Stack, включая некоторые специфические требования для каждой платформы. Например, для Azure Stack существуют определенные клиентские библиотеки и определенные требования к суффиксу конечной точки. Дополнительные сведения см. в статье [Azure Stack Storage: Differences and considerations](azure-stack-acs-differences.md) (Хранилище Azure Stack. Отличия и рекомендации).

## <a name="azure-client-libraries"></a>Клиентские библиотеки Azure
Поддерживаемая версия интерфейса REST API для хранилища Azure Stack — 2015-04-05. Он не имеет полного соответствия с последней версией REST API службы хранилища Azure. Поэтому для клиентских библиотек хранилища вам необходимо знать версию, совместимую с REST API 2015-04-05.


|Клиентская библиотека|Поддерживаемая версия Azure Stack|Ссылка|Спецификация конечной точки|
|---------|---------|---------|---------|
|.NET     |6.2.0|Пакет NuGet:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Выпуск GitHub:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|Файл app.config|
|Java|4.1.0|Пакет Maven<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Выпуск GitHub:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Настройка строки подключения|
|Node.js     |1.1.0|Ссылка на NPM:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(выполните: `npm install azure-storage@1.1.0)`<br><br>Выпуск GitHub:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Объявление экземпляра службы||C++|2.4.0|Пакет NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Выпуск GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Настройка строки подключения|
|C++|2.4.0|Пакет NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Выпуск GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Настройка строки подключения|
|PHP|0.15.0|Выпуск GitHub:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Установка через компоновщик (подробности см. ниже)|Настройка строки подключения|
|Python     |0.30.0|Пакет PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(выполните: `pip install -v azure-storage==0.30.0)`<br><br>Выпуск GitHub:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Объявление экземпляра службы|
|Ruby|0.12.1<br>Предварительный просмотр|Пакет RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Выпуск GitHub:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Настройка строки подключения|

> [!NOTE]
> Сведения PHP<br><br>
>Установка через компоновщик:
>1. Создайте файл с именем `composer.json` в корневом каталоге проекта со следующим кодом:<br>
>
>   ```
>   {
>       "require":{
>           "Microsoft/azure-storage":"0.15.0"
>        }
>    }
>   ```
>
>2. Скачайте [composer.phar](http://getcomposer.org/composer.phar) в корневой каталог проекта.
>3. Выполните команду `php composer.phar install`.
>


## <a name="endpoint-declaration"></a>Объявление конечной точки
Конечная точка Azure Stack содержит две части: имя региона и домен Azure Stack.
В комплекте разработки Azure Stack конечной точкой по умолчанию является **local.azurestack.external**.
Если вы не знаете конечную точку, обратитесь к администратору облака.

## <a name="examples"></a>Примеры


### <a name="net"></a>.NET

Для Azure Stack суффикс конечной точки указан в файле app.config:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

Для Azure Stack суффикс конечной точки указывается в настройке строки подключения:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Для Azure Stack суффикс конечной точки указан в экземпляре объявления:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

Для Azure Stack суффикс конечной точки указывается в настройке строки подключения:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Для Azure Stack суффикс конечной точки указывается в настройке строки подключения:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Для Azure Stack суффикс конечной точки указан в экземпляре объявления:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

Для Azure Stack суффикс конечной точки указывается в настройке строки подключения:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Хранилище BLOB-объектов

Следующие руководства по хранилищу BLOB-объектов Azure применимы к Azure Stack. Обратите внимание на конкретное требование к суффиксу конечной точки для Azure Stack, описанное в предыдущем разделе [Примеры](#examples).

* [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Использование хранилища BLOB-объектов из Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Способы использования хранилища BLOB-объектов из Node.js]../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Использование хранилища BLOB-объектов из C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Использование хранилища BLOB-объектов из PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Использование хранилища BLOB-объектов Azure из Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Использование хранилища BLOB-объектов из Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Хранилище очередей

Следующие руководства по хранилищу очередей Azure применимы к Azure Stack. Обратите внимание на конкретное требование к суффиксу конечной точки для Azure Stack, описанное в предыдущем разделе [Примеры](#examples).

* [Приступая к работе с хранилищем очередей Azure с помощью .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Использование хранилища очередей из Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Использование хранилища очередей из Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Использование хранилища очередей из C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Использование хранилища очередей из PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Использование хранилища очередей из Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Использование хранилища очередей из Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Хранилище таблиц

Следующие руководства по хранилищу таблиц Azure применимы к Azure Stack. Обратите внимание на конкретное требование к суффиксу конечной точки для Azure Stack, описанное в предыдущем разделе [Примеры](#examples).

* [Приступая к работе с хранилищем таблиц Azure с помощью .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Использование табличного хранилища из Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Использование табличного хранилища Azure из Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Использование табличного хранилища из C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Использование табличного хранилища из PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Как использовать хранилище таблиц в Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Использование табличного хранилища из Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Введение в службу хранилища Microsoft Azure](../../storage/common/storage-introduction.md)