---
title: Приступая к работе с хранилищем BLOB-объектов и подключенными службами Visual Studio (ASP.NET Core) | Документация Майкрософт
description: Начало работы с хранилищем BLOB-объектов Azure в проекте Visual Studio ASP.NET Core после создания учетной записи хранения с использованием подключенных служб Visual Studio.
services: storage
author: ghogen
manager: douge
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 566e2edc0157ccd02e0b44ae7df86c4b484858b0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793959"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

В этой статье описывается, как приступить к использованию хранилища BLOB-объектов Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее в проекте ASP.NET Core с помощью функции **подключенных служб** в Visual Studio. Операция **подключенных служб** устанавливает соответствующие пакеты NuGet для доступа к службе хранилища Azure в вашем проекте и добавляет строку подключения для учетной записи хранения в файлы конфигурации проекта. Общие сведения о службе хранилища Azure см. в [документации по службе хранилища](https://azure.microsoft.com/documentation/services/storage/).

Хранилище больших двоичных объектов Azure — это служба хранения большого количества неструктурированных данных, к которым можно получить доступ практически из любой точки мира по протоколам HTTP или HTTPS. Один большой двоичный объект может быть любого размера. Большими двоичными объектами могут быть изображения, аудио- и видеофайлы, необработанные данные и файлы документов. В этой статье описывается, как приступить к работе с хранилищем BLOB-объектов после создания учетной записи хранения Azure с помощью **подключенных служб** в проекте ASP.NET Core.

Так же как файлы располагаются в папках, большие двоичные объекты хранилища располагаются в контейнерах. После создания большого двоичного объекта вы можете создать один или несколько контейнеров в нем. Например, можно создать большой двоичный объект "Альбом", в нем создать контейнеры "изображения" для хранения файлов изображений и "аудио" для хранения аудиофайлов. После создания контейнеров в них можно передавать отдельные файлы. Дополнительные сведения о выполнении программных операций с большими двоичными объектами см. в статье [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

Некоторые интерфейсы API службы хранилища Azure являются асинхронными, и в коде, приведенном в этой статье, предполагается, что используются асинхронные методы. Дополнительные сведения см. в статье [Асинхронное программирование](https://docs.microsoft.com/dotnet/csharp/async).

## <a name="access-blob-containers-in-code"></a>Доступ к контейнерам больших двоичных объектов в коде

Для программного доступа к большим двоичным объектам в проектах ASP.NET Core необходимо добавить приведенный ниже код, если он отсутствует.

1. Добавьте необходимые инструкции `using`.

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Получите объект `CloudStorageAccount`, представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Используйте объект `CloudBlobClient`, чтобы получить ссылку `CloudBlobContainer` на существующий контейнер в вашей учетной записи хранения.

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Создание контейнера в коде

Вы также можете использовать объект `CloudBlobClient` для создания контейнера в учетной записи хранения, вызвав `CreateIfNotExistsAsync`.

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Чтобы сделать файлы в контейнере доступными для всех пользователей, сделайте контейнер общедоступным.

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Отправка BLOB-объекта в контейнер

Для отправки файла большого двоичного объекта в контейнер получите ссылку на контейнер и используйте ее для получения ссылки на большой двоичный объект. Затем передайте любой поток данных по этой ссылке, вызвав метод `UploadFromStreamAsync`. Эта операция создает большой двоичный объект, если он не существует, или перезаписывает существующий большой двоичный объект. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Чтобы получить список больших двоичных объектов в контейнере, сначала следует получить ссылку на контейнер, а затем вызвать его метод `ListBlobsSegmentedAsync` для получения больших двоичных объектов и (или) каталогов внутри него. Чтобы получить доступ к широкому набору свойств и методов для возвращенного `IListBlobItem`, приведите его к объекту `CloudBlockBlob`, `CloudPageBlob` или `CloudBlobDirectory`. Если тип большого двоичного объекта неизвестен, можно использовать проверку типов, чтобы определить нужный тип для приведения.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

Другие способы просмотра содержимого контейнера BLOB-объектов см. в [кратком руководстве по передаче, скачиванию и составлению списка больших двоичных объектов с помощью .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="download-a-blob"></a>Загрузка BLOB-объектов

Чтобы скачать большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод `DownloadToStreamAsync`. В следующем примере метод `DownloadToStreamAsync` используется для переноса содержимого большого двоичного объекта в объект потока, который затем можно сохранить в локальном файле.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Другие способы сохранения больших двоичных объектов в виде файлов см. в [кратком руководстве по передаче, скачиванию и составлению списка больших двоичных объектов с помощью .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).

## <a name="delete-a-blob"></a>Удаление большого двоичного объекта

Чтобы удалить большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод `DeleteAsync`.

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
