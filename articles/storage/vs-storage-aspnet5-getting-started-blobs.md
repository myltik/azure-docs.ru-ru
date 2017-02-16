---
title: "Приступая к работе с хранилищем BLOB-объектов и подключенными службами Visual Studio (ASP.NET 5) | Документация Майкрософт"
description: "Как приступить к работе, используя хранилище больших двоичных объектов Azure в проекте Visual Studio ASP.NET 5 после создания учетной записи хранения с помощью подключенных служб Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 88e6ce0deeb5dab276b5ae49f6c99391e37495f4
ms.openlocfilehash: 2a31f6d8aa00e89e8dbbe0089fc76ecbb2102b3c


---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>Начало работы с хранилищем больших двоичных объектов Azure и подключенными службами Visual Studio (ASP.NET 5)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Обзор
В этой статье описывается, как приступить к использованию хранилища больших двоичных объектов Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее в проекте ASP.NET 5 с помощью диалогового окна "Добавление подключенных служб" в Visual Studio.

Хранилище больших двоичных объектов Azure — это служба хранения большого количества неструктурированных данных, к которым можно получить доступ практически из любой точки мира по протоколам HTTP или HTTPS. Один большой двоичный объект может быть любого размера. Большими двоичными объектами могут быть изображения, аудио- и видеофайлы, необработанные данные и файлы документов. В этой статье описывается, как приступить к работе с хранилищем больших двоичных объектов после создания учетной записи хранения Azure с помощью диалогового окна **Добавление подключенных служб** в проекте ASP.NET 5.

Так же как файлы располагаются в папках, большие двоичные объекты хранилища располагаются в контейнерах. После создания хранилища вы можете создать один или несколько контейнеров в нем. Например, можно создать хранилище с именем "Альбом", в нем создать контейнеры "изображения" для хранения картинок и "аудио" для хранения аудиофайлов. После создания контейнеров в них можно отправлять индивидуальные большие двоичные объекты. Дополнительные сведения о выполнении программных операций с большими двоичными объектами см. в статье [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](storage-dotnet-how-to-use-blobs.md).

## <a name="access-blob-containers-in-code"></a>Доступ к контейнерам больших двоичных объектов в коде
Для программного доступа к большим двоичным объектам в проектах ASP.NET 5 необходимо добавить следующие элементы, если они еще не существуют.

1. Добавьте следующие объявления пространств имен кода в начало каждого файла C#, в котором вы собираетесь обращаться к службе хранилища Azure программным способом.
   
        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;
2. Получите объект **CloudStorageAccount** , представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.
   
         CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
            "<storage-account-name>",
            "<access-key>"), true);
   
    **ПРИМЕЧАНИЕ.** Вставьте весь код, представленный выше, перед кодом из следующих разделов.
3. Используйте объект **CloudBlobClient**, чтобы получить ссылку **CloudBlobContainer** на существующий контейнер в учетной записи хранения.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
   
        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

## <a name="create-a-container-in-code"></a>Создание контейнера в коде
Вы также можете использовать объект **CloudBlobClient** для создания контейнера в учетной записи хранения. Вам нужно всего лишь добавить вызов **CreateIfNotExistsAsync** , как это сделано в следующем коде:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**Примечание**. Интерфейсы API, которые выполняют вызовы к службе хранилища Azure в ASP.NET 5, являются асинхронными. Дополнительные сведения см. в статье [Asynchronous Programming with async and await (C#)](http://msdn.microsoft.com/library/hh191443.aspx) (Асинхронное программирование с использованием ключевых слов Async и Await (C#)). В следующем примере кода предполагается, что используются асинхронные методы программирования.

Чтобы сделать файлы в контейнере доступными для всех пользователей, сделайте контейнер общедоступным с помощью следующего примера кода.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

## <a name="upload-a-blob-into-a-container"></a>Отправка BLOB-объекта в контейнер
Для отправки файла большого двоичного объекта в контейнер получите ссылку на контейнер и используйте ее для получения ссылки на большой двоичный объект. Получив ссылку на большой двоичный объект, вы можете передать в него любой поток данных с помощью метода **UploadFromStreamAsync**. Эта операция создает большой двоичный объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере
Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. После этого вы сможете извлекать из контейнера большие двоичные объекты и каталоги с помощью метода **ListBlobsSegmentedAsync** . Для доступа к широкому набору свойств и методов возвращаемого объекта **IListBlobItem** необходимо преобразовать его в объект **CloudBlockBlob**, **CloudPageBlob** или **CloudBlobDirectory**. Если тип большого двоичного объекта неизвестен, можно использовать проверку типов, чтобы определить нужный. Следующий код показывает, как получить и вывести URI каждого элемента в контейнере.

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

Можно вывести список содержимого контейнера BLOB-объектов и другими способами. Дополнительные сведения см. в разделе [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container).

## <a name="download-a-blob"></a>Загрузка BLOB-объектов
Чтобы скачать большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод **DownloadToStreamAsync** . В следующем примере метод **DownloadToStreamAsync** используется для переноса содержимого большого двоичного объекта в объект потока, который затем можно сохранить в локальном файле.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Есть другие способы сохранения больших двоичных объектов в виде файлов. Дополнительные сведения см. в разделе [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](storage-dotnet-how-to-use-blobs.md#download-blobs).

## <a name="delete-a-blob"></a>Удаление большого двоичного объекта
Чтобы удалить большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод **DeleteAsync** .

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]




<!--HONumber=Jan17_HO1-->


