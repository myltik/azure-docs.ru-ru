<properties
 pageTitle="Управление сроком хранения содержимого BLOB-объекта в службе хранилища Azure в Azure CDN | Microsoft Azure"
 description="Сведения о возможностях контроля времени жизни BLOB-объектов в кэшировании Azure CDN."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# Управление сроком хранения содержимого BLOB-объекта в службе хранилища Azure в Azure CDN

> [AZURE.SELECTOR]
- [Веб-приложения и облачные службы Azure, ASP.NET или IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Служба BLOB-объектов в службе хранилища Azure](cdn-manage-expiration-of-blob-content.md)

[Служба BLOB-объектов](../storage/storage-introduction.md#blob-storage) в [службе хранилища Azure](../storage/storage-introduction.md) — это один из возможных источников на основе Azure, интегрированных с Azure CDN. Любое общедоступное содержимое BLOB-объекта может кэшироваться в Azure CDN до истечения его срока жизни (TTL). Срок жизни определяется [заголовком *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9), указанным в HTTP-ответе службы хранилища Azure.

>[AZURE.TIP] Вы можете не указывать срок жизни для большого двоичного объекта. Тогда Azure CDN по умолчанию применит срок жизни длительностью семь дней.
>
>Дополнительные сведения о том, как Azure CDN ускоряет доступ к BLOB-объектам и другим файлам, см. в статье [Общие сведения о сети доставки содержимого (CDN) Azure](./cdn-overview.md).
>
>Дополнительные сведения о службе BLOB-объектов службы хранилища Azure см. в разделе [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/dd179376.aspx).

В этом руководстве описано несколько способов определения срока жизни (TTL) для большого двоичного объекта в службе хранилища Azure.

## Azure PowerShell

Одним из самых быстрых и эффективных способов управления службами Azure является [Azure PowerShell](../powershell-install-configure.md). Используйте командлет `Get-AzureStorageBlob`, чтобы получить ссылку на большой двоичный объект, а затем определите свойство `.ICloudBlob.Properties.CacheControl`.

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

>[AZURE.TIP] Можно также использовать PowerShell для [управления профилями и конечными точками CDN](./cdn-manage-powershell.md).

## Клиентская библиотека хранилища Azure для .NET

Чтобы задать TTL для большого двоичного объекта с помощью .NET, используйте [клиентскую библиотеку службы хранилища Azure для .NET](../storage/storage-dotnet-how-to-use-blobs.md), чтобы определить свойство [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

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

>[AZURE.TIP] На [этой странице](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) вы найдете множество примеров кода .NET для службы хранилища Azure.

## Другие методы

- [Интерфейс командной строки Azure](../xplat-cli-install.md)

	При отправке большого двоичного объекта определите свойство *cacheControl* с помощью параметра `-p`. В этом примере устанавливается срок жизни 3600 секунд (1 час).

	```text
	azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
	```

- [API-интерфейс REST служб хранилища Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)

	Явно определите свойство *x-ms-blob-cache-control* в запросе [Put Blob](https://msdn.microsoft.com/ru-RU/library/azure/dd179451.aspx), [Put Block List](https://msdn.microsoft.com/ru-RU/library/azure/dd179467.aspx) или [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx).

- Сторонние средства управления хранилищем

	Некоторые сторонние средства управления хранилищем Azure позволяют определять свойство *CacheControl* для больших двоичных объектов.

## Проверка заголовка *Cache-Control*

Вы легко можете проверить установленный для BLOB-объектов срок жизни. Для этого проверьте наличие заголовка *Cache-Control* в своем BLOB-объекте с помощью [средств разработчика](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) в любом браузере. Для проверки заголовков ответа можно использовать и другие средства, например**wget**, [Postman](https://www.getpostman.com/) или [Fiddler](http://www.telerik.com/fiddler).

## Дальнейшие действия

- [Изучите дополнительные сведения о заголовке *Cache-Control*.](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Узнайте, как управлять сроком действия содержимого облачных служб в сети доставки содержимого (CDN) Azure](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_0921_2016-->