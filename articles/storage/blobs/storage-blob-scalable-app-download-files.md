---
title: "Загрузки больших объемов случайные данные из хранилища Azure | Документы Microsoft"
description: "Сведения об использовании пакета Azure SDK для загрузки больших объемов случайные данные из учетной записи хранилища Azure"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 46b0cf3666088175372b6a2e73b3dd421a4bff8b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Загрузки больших объемов случайные данные из хранилища Azure

Это руководство представляет собой первую часть цикла. Этого учебника показано, как для загрузки больших объемов данных из хранилища Azure.

В третьей части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Обновление приложения
> * Выполнение приложения
> * Проверить количество подключений

## <a name="prerequisites"></a>Технические условия

Для завершения этого учебника, необходимо выполнить работу с предыдущим учебником хранилища: [отправка больших объемов случайные данные в параллельном режиме в хранилище Azure][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Удаленное подключение к виртуальной машине

 Чтобы создать сеанс удаленного рабочего стола с виртуальной машиной, используйте следующую команду на локальном компьютере. Замените IP-адрес значением publicIPAddress виртуальной машины. При появлении запроса введите учетные данные, использованные при создании виртуальной машины.

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Обновление приложения

В предыдущем учебнике только загруженные файлы в учетную запись хранилища. Откройте `D:\git\storage-dotnet-perf-scale-app\Program.cs` в текстовом редакторе. Замените `Main` метод с помощью следующего примера. Этот пример комментарии out задача передачи и uncomments задачи загрузки и для удаления содержимого в учетной записи хранения, после завершения.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        UploadFilesAsync().GetAwaiter().GetResult();

        // Uncomment the following line to enable downloading of files from the storage account.  This is commented out
        // initially to support the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initialy
        // As the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

После обновления приложения, необходимо создать приложение еще раз. Откройте `Command Prompt` и перейдите к `D:\git\storage-dotnet-perf-scale-app`. Перестройте приложение, выполнив `dotnet build` как показано в следующем примере:

```
dotnet build
```

## <a name="run-the-application"></a>Выполнение приложения

Теперь, когда приложение перестроена пришло время для запуска приложения с обновленным кодом. Если это еще не открыта, откройте `Command Prompt` и перейдите к `D:\git\storage-dotnet-perf-scale-app`.

Нажмите клавишу `dotnet run`, чтобы запустить приложение.

```
dotnet run
```

Приложение считывает контейнеры, расположенный в учетной записи хранения, указанной в **storageconnectionstring**. Он проходит по большим двоичным объектам 10 при помощи [ListBlobsSegmented](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmented?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmented_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) метод в контейнерах и загрузки их на локальный компьютер с помощью [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) метод.
В следующей таблице показаны [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) , определенных для каждого большого двоичного объекта, как она загружается.

|Свойство|Значение|ОПИСАНИЕ|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| Да| Это свойство отключает проверку MD5-хэш содержимое, отправляемое. Отключение проверки MD5 выводятся быстрее передачи. Но не подтверждает действительность или целостности передаваемых файлов. |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Это свойство определяет, если хэш MD5 вычисляется и сохраняется.   |

`DownloadFilesAsync` Задача отображается в следующем примере:

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestionOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.  Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container lazily 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

### <a name="validate-the-connections"></a>Проверять соединения

Во время загрузки файлов, можно проверить число одновременных подключений к вашей учетной записи хранилища. Откройте `Command Prompt` и тип `netstat -a | find /c "blob:https"`. Эта команда показывает количество подключений, открытых в настоящее время с помощью `netstat`. В следующем примере показано, как выходные данные для просмотра при запуске учебника самостоятельно. Как видно из примера, более 280 подключения были открыты при загрузке произвольных файлов из учетной записи хранилища.

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Дальнейшие действия

В третьей части серии вы узнали о загрузке больших объемов случайные данные из учетной записи хранилища, такими как:

> [!div class="checklist"]
> * Выполнение приложения
> * Проверить количество подключений

Перейти к четвертой части ряда, чтобы проверить показатели пропускной способности и задержке на портале.

> [!div class="nextstepaction"]
> [Проверка пропускной способности и задержки метрики на портале](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md