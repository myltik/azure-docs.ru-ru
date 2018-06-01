---
title: Ресурсы в Службах мультимедиа Azure | Документация Майкрософт
description: В этой статье приведены общие сведения о ресурсах и их использовании в Службах мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 76ed74f2df62d478b83e109a492977ec2d580198
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305095"
---
# <a name="assets"></a>Активы

Сущность **Ресурс** содержит цифровые файлы (в том числе видео, аудио, изображения, коллекции эскизов, текстовые дорожки и файлы скрытых титров), а также метаданные об этих файлах. После загрузки цифровых файлов в ресурс их можно использовать в рабочих процессах кодирования и потоковой передачи служб мультимедиа.

Ресурс сопоставляется с контейнером больших двоичных объектов в [учетной записи хранения Azure](storage-account-concept.md). Файлы ресурса хранятся в этом контейнере как блочные BLOB-объекты. Вы можете работать с файлами ресурсов в контейнерах с помощью клиентских пакетов SDK службы хранилища.

Если в учетной записи используется хранилище общего назначения версии 2 (GPv2), Службы мультимедиа Azure поддерживают уровни больших двоичных объектов. Это хранилище позволяет перемещать файлы в хранилище холодного уровня или автономное неструктурированное защищенное хранилище. Автономное неструктурированное защищенное хранилище подходит для архивации ненужных исходных файлов (например, после шифрования).

В Службах мультимедиа версии 3 входные данные задания можно создавать из ресурсов или URL-адреса HTTP (HTTPS). Сведения о создании ресурса, который можно использовать в качестве входных данных задания, см. в статье [Создание входных данных задания из локального файла](job-input-from-local-file-how-to.md).

Кроме того, прочтите об [учетных записях хранения в Службах мультимедиа](storage-account-concept.md), а также о [преобразованиях и заданиях](transform-concept.md).

## <a name="asset-definition"></a>Определение ресурса

В следующей таблице представлены свойства ресурса и их определения.

|ИМЯ|type|ОПИСАНИЕ|
|---|---|---|
|Идентификатор|строка|Полный идентификатор ресурса.|
|name|строка|Имя ресурса.|
|properties.alternateId |строка|Альтернативный идентификатор ресурса.|
|properties.assetId |строка|Идентификатор ресурса.|
|properties.container |строка|Имя контейнера больших двоичных объектов ресурса.|
|properties.created |строка|Дата создания ресурса.|
|properties.description |строка|Описание ресурса.|
|properties.lastModified |строка|Дата последнего изменения ресурса.|
|properties.storageAccountName |строка|Имя учетной записи хранения.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |Формат шифрования ресурса. Не указан или MediaStorageEncryption.|
|Тип|строка|Тип ресурса.|

Полное определение см. в статье о [ресурсах](https://docs.microsoft.com/rest/api/media/assets).

## <a name="filtering-ordering-and-paging-support"></a>Поддержка фильтрации, упорядочения и разбиения на страницы

Службы мультимедиа поддерживают следующие параметры запроса OData для ресурсов: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>Фильтрация и упорядочение

В следующей таблице показано, как эти параметры могут быть применены к свойствам ресурса: 

|ИМЯ|Фильтр|Порядок|
|---|---|---|
|Идентификатор|Поддерживает:<br/>Равно<br/>Больше<br/>Меньше|Поддерживает:<br/>По возрастанию<br/>По убыванию|
|name|||
|properties.alternateId |Поддерживает:<br/>Равно||
|properties.assetId |Поддерживает:<br/>Равно||
|properties.container |||
|properties.created|Поддерживает:<br/>Равно<br/>Больше<br/>Меньше|Поддерживает:<br/>По возрастанию<br/>По убыванию|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|Тип|||

В следующем примере C# выполняется фильтрация по дате создания:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Разбиение на страницы

Для каждого из четырех порядков сортировки поддерживается разбиение на страницы. 

Если ответ запроса содержит большое количество элементов (в настоящее время более 1000), служба возвращает свойство @odata.nextLink, чтобы получить следующую страницу результатов. Это можно использовать для просмотра всего результирующего набора. Пользователь не настраивает размер страницы. 

Если ресурсы создаются или удаляются во время разбивки коллекции на страницы, изменения отражаются в возвращаемых результатах (если эти изменения находятся в той части коллекции, которая не была загружена). 

В следующем примере C# показано перечисление всех ресурсов в учетной записи.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

Примеры REST для перечисления ресурсов см. в [этой статье](https://docs.microsoft.com/rest/api/media/assets/list).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
