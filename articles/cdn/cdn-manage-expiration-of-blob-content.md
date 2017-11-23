---
title: "Управление сроком действия хранилища BLOB-объектов Azure в сети доставки содержимого Azure | Microsoft Docs"
description: "Сведения о возможностях контроля времени жизни BLOB-объектов в кэшировании Azure CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: c2b49058ec7dd52b5063e815447697fa17ddb53a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Управление сроком действия хранилища BLOB-объектов Azure в сети доставки содержимого Azure
> [!div class="op_single_selector"]
> * [Веб-содержимое Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [хранилище BLOB-объектов Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Служба хранилища BLOB-объектов](../storage/common/storage-introduction.md#blob-storage) в службе хранилища Azure — это один из нескольких источников в облаке Azure, интегрированных с сетью доставки содержимого (CDN) Azure. Любое общедоступное содержимое BLOB-объекта может кэшироваться в Azure CDN до истечения его срока жизни (TTL). Срок жизни определяется заголовком `Cache-Control`, указанным в HTTP-ответе исходного сервера. В этой статье описано несколько способов определения заголовка `Cache-Control` для большого двоичного объекта в службе хранилища Azure.

> [!TIP]
> Срок жизни для BLOB-объекта можно не указывать. Тогда Azure CDN по умолчанию применит срок жизни длительностью семь дней.
> 
> Дополнительные сведения о том, как Azure CDN ускоряет доступ к BLOB-объектам и другим файлам, см. в статье [Общие сведения о сети доставки содержимого (CDN) Azure](cdn-overview.md).
> 
> Дополнительные сведения о хранилище BLOB-объектов Azure см. в статье [Общие сведения о хранилище BLOB-объектов](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) — это одно из самых быстрых и мощных средств администрирования служб Azure. Используйте командлет `Get-AzureStorageBlob`, чтобы получить ссылку на большой двоичный объект, а затем определите свойство `.ICloudBlob.Properties.CacheControl`. 

Например:

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Можно также использовать PowerShell для [управления профилями и конечными точками CDN](cdn-manage-powershell.md).
> 
>

## <a name="azure-storage-client-library-for-net"></a>Клиентская библиотека хранилища Azure для .NET
Чтобы определить заголовок `Cache-Control` для большого двоичного объекта с помощью .NET, используйте [клиентскую библиотеку службы хранилища Azure для .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Так вы определите свойство [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

Например:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Другие примеры кода .NET для хранилища BLOB-объектов Azure см. на [этой странице](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 
> 

## <a name="other-methods"></a>Другие методы
* [Интерфейс командной строки Azure](../cli-install-nodejs.md)
  
    При передаче большого двоичного объекта можно определить свойство *cacheControl* с помощью параметра `-p` в интерфейсе командной строки Azure. В следующем примере устанавливается срок жизни в 3600 секунд:
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [API-интерфейс REST служб хранилища Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    Явно определите свойство *x-ms-blob-cache-control* в запросе [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Put Block List](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx) или [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx).

* Сторонние средства управления хранилищем
  
    Некоторые сторонние средства управления службы хранилища Azure позволяют определять свойство **CacheControl** для больших двоичных объектов. 

## <a name="testing-the-cache-control-header"></a>Проверка заголовка Cache-Control
Вы легко можете проверить установленный для BLOB-объектов срок жизни. Используя встроенные в браузер [средства разработчика](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), убедитесь, что ваш BLOB-объект содержит заголовок ответа `Cache-Control`. Для просмотра заголовков ответа можно использовать и другие средства, например **wget**, [Postman](https://www.getpostman.com/) или [Fiddler](http://www.telerik.com/fiddler).

## <a name="next-steps"></a>Дальнейшие действия
* [Узнайте, как управлять сроком действия содержимого облачных служб в сети доставки содержимого (CDN) Azure](cdn-manage-expiration-of-cloud-service-content.md).

