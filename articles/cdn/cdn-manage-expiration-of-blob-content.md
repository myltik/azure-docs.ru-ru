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
ms.openlocfilehash: 6f82ae396a17f903a522c716f73a5f7d2de660e7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Управление сроком действия хранилища BLOB-объектов Azure в сети доставки содержимого Azure
> [!div class="op_single_selector"]
> * [Веб-содержимое Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [хранилище BLOB-объектов Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Служба хранилища BLOB-объектов](../storage/common/storage-introduction.md#blob-storage) в службе хранилища Azure — это один из нескольких источников в облаке Azure, интегрированных с сетью доставки содержимого (CDN) Azure. Любое общедоступное содержимое BLOB-объекта может кэшироваться в Azure CDN до истечения его срока жизни (TTL). Срок жизни определяется заголовком `Cache-Control`, указанным в HTTP-ответе исходного сервера. В этой статье описано несколько способов определения заголовка `Cache-Control` для большого двоичного объекта в службе хранилища Azure.

Можно также управлять параметрами кэша на портале Azure, определив [правила кэширования CDN](cdn-caching-rules.md). Если определить одно или несколько правил кэширования и настроить их на **переопределение** или **отключение кэша**, предоставляемые системой параметры, описываемые в этой статье, будут игнорироваться. См. дополнительные сведения о [функции кэширования](cdn-how-caching-works.md).

> [!TIP]
> Срок жизни для BLOB-объекта можно не указывать. В этом случае Azure CDN автоматически применяет стандартное значение TTL (семь дней), если только вы не настроили правила кэширования на портале Azure. Этот срок жизни по умолчанию применяется только к оптимизациям общей веб-доставки. Для оптимизаций больших файлов срок жизни по умолчанию составляет один день, а для оптимизаций потоковой передачи срок жизни по умолчанию составляет один год.
> 
> Дополнительные сведения о том, как Azure CDN ускоряет доступ к BLOB-объектам и другим файлам, см. в статье [Общие сведения о сети доставки содержимого (CDN) Azure](cdn-overview.md).
> 
> Дополнительные сведения о хранилище BLOB-объектов Azure см. в статье [Общие сведения о хранилище BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Определение заголовков Cache-Control с помощью Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) — это одно из самых быстрых и мощных средств администрирования служб Azure. Используйте командлет `Get-AzureStorageBlob`, чтобы получить ссылку на большой двоичный объект, а затем определите свойство `.ICloudBlob.Properties.CacheControl`. 

Например: 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Можно также использовать PowerShell для [управления профилями и конечными точками CDN](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Определение заголовков Cache-Control с помощью .NET
Чтобы определить заголовок `Cache-Control` для большого двоичного объекта с помощью кода .NET, используйте [клиентскую библиотеку службы хранилища Azure для .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) для задания свойства [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

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
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Другие примеры кода .NET для хранилища BLOB-объектов Azure см. на [этой странице](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Определение заголовков Cache-Control с помощью других методов

### <a name="azure-storage-explorer"></a>обозреватель хранилищ Azure
С помощью [обозревателя службы хранилища Azure](https://azure.microsoft.com/en-us/features/storage-explorer/) можно просматривать и изменять ресурсы хранилища BLOB-объектов, включая такие свойства, как *CacheControl*. 

Чтобы обновить свойство *CacheControl* большого двоичного объекта с помощью обозревателя хранилищ Azure, сделайте следующее.
   1. Выберите большой двоичный объект, а затем выберите **Свойства** в контекстном меню. 
   2. Прокрутите меню вниз до свойства *CacheControl*.
   3. Введите значение, а затем нажмите кнопку **Сохранить**.


![Свойства в обозревателе службы хранилища Azure](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Интерфейс командной строки Azure
С помощью [интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) (CLI) можно управлять ресурсами BLOB-объектов Azure из командной строки. Чтобы определить заголовок Cache-Control при передаче большого двоичного объекта с помощью Azure CLI, определите свойство *cacheControl* с помощью параметра `-p`. В следующем примере показано, как задать срок жизни, равный 1 часу (3600 секунд).
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\test.txt myContainer test.txt
```

### <a name="azure-storage-services-rest-api"></a>REST API служб хранилища Azure
Можно использовать [REST API служб хранилища Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx), чтобы явно задать свойство *x-ms-blob-cache-control* с помощью следующих операций в запросе:
  
   - [вставка большого двоичного объекта](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx);
   - [вставка списка блокировки](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx);
   - [задание свойств службы BLOB-объекта](https://msdn.microsoft.com/library/azure/ee691966.aspx).

## <a name="testing-the-cache-control-header"></a>Проверка заголовка Cache-Control
Вы легко можете проверить установленный для BLOB-объектов срок жизни. Используя встроенные в браузер [средства разработчика](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), убедитесь, что ваш BLOB-объект содержит заголовок ответа `Cache-Control`. Для просмотра заголовков ответа можно использовать и другие инструменты, например [wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/) или [Fiddler](http://www.telerik.com/fiddler).

## <a name="next-steps"></a>Дальнейшие действия
* [Узнайте, как управлять сроком действия содержимого облачных служб в сети доставки содержимого (CDN) Azure](cdn-manage-expiration-of-cloud-service-content.md).
* [Дополнительные сведения о кэшировании](cdn-how-caching-works.md)

