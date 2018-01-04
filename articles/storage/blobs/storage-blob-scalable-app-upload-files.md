---
title: "Отправка больших объемов случайные данные в параллельном режиме в хранилище Azure | Документы Microsoft"
description: "Сведения об использовании пакета Azure SDK для передачи больших объемов случайных данных одновременно для учетной записи хранилища Azure"
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
ms.openlocfilehash: 98f3f69c6025d61caac20e13b573651854952432
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/23/2017
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Отправка больших объемов случайные данные в параллельном режиме в хранилище Azure

Это руководство представляет собой вторую часть цикла. В этом учебнике показано, что при развертывании приложения, которая отправляет большой объем случайные данные учетной записи хранилища Azure.

Из второй части цикла вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка строки подключения
> * Создание приложения
> * Выполнение приложения
> * Проверить количество подключений

Хранилище больших двоичных объектов предоставляет масштабируемые службы для хранения данных. Чтобы убедиться, что приложение является как высокопроизводительных можно понимать как рекомендуется использовать хранилище BLOB-объектов работает. Знание ограничения для больших двоичных объектов Azure важно, чтобы узнать больше об этих ограничениях посетите: [больших двоичных объектов целевые показатели масштабируемости хранилища](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

[Именование разделов](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47) является еще один важный фактор при разработке приложения с высокой производительностью, с помощью больших двоичных объектов. Хранилище Azure использует схему секционирования на основе диапазонов для масштабирования и сбалансировать нагрузку. Эта конфигурация означает файлы с одинаковые соглашения об именовании или префиксы перейдите к разделу. Эта логика включает имя контейнера, в котором файлы загружаются в. В этом учебнике используйте файлы, чьи идентификаторы GUID для имен также как случайный содержимого. Затем они отправляются в пять разных контейнеров с случайные имена.

## <a name="prerequisites"></a>Технические условия

Для завершения этого учебника, необходимо выполнить работу с предыдущим учебником хранилища: [Создание виртуальной машины и учетная запись хранения для масштабируемых приложений][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Удаленное подключение к виртуальной машине

Чтобы создать сеанс удаленного рабочего стола с виртуальной машиной, используйте следующую команду на локальном компьютере. Замените IP-адрес значением publicIPAddress виртуальной машины. При появлении запроса введите учетные данные, используемые при создании виртуальной машины.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Настройка строки подключения

На портале Azure перейдите к вашей учетной записи хранилища. Выберите **ключи доступа** под **параметры** вашей учетной записи хранилища. Копировать **строка подключения** из первичного или вторичного ключа. Войдите на виртуальную машину, созданную в предыдущем учебнике. Откройте **командной строки** от имени администратора и выполните `setx` с `/m` коммутатора, эта команда сохраняет переменной среды machine параметр. Переменная среды недоступна, пока вы перезагрузить **командной строки**. Замените  **\<storageConnectionString\>**  в следующем примере:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

По завершении откройте другую **командной строки**, перейдите к `D:\git\storage-dotnet-perf-scale-app` и тип `dotnet build` для построения приложения.

## <a name="run-the-application"></a>Выполнение приложения

Перейдите на страницу `D:\git\storage-dotnet-perf-scale-app`.

Нажмите клавишу `dotnet run`, чтобы запустить приложение. При первом запуске `dotnet` он заполняет кэш локального пакета, позволяет повысить скорость восстановления и доступа в автономном режиме. Эта команда занимает около минуты для завершения и происходит только один раз.

```
dotnet run
```

Приложение создает пять произвольным именем контейнера и начинается передача файлов в промежуточный каталог в учетной записи хранения. Приложение задает менее потоков до 100 и [свойство DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) на 100, чтобы убедиться, что большое количество одновременных соединений разрешены при выполнении приложения.

Кроме настройки ограничивают потоков и соединений [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) для [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) метод настроены для использования параллелизма и отключить проверку MD5-хэш. Файлы загружаются в блоки по 100 МБ, такая конфигурация обеспечивает более высокую производительность, но может потребовать значительных затрат при использовании с ошибками выполнения сети так, будто весь блок 100 МБ сбоя будет предпринята повторная попытка.

|Свойство|Значение|ОПИСАНИЕ|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| Параметр разбивает на блоков большого двоичного объекта, при передаче. Для максимальной производительности это значение должно быть 8 раз превышает количество ядер. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| Да| Это свойство отключает проверку MD5-хэш содержимое, отправляемое. Отключение проверки MD5 выводятся быстрее передачи. Но не подтверждает действительность или целостности передаваемых файлов.   |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Это свойство определяет, если хэш MD5 вычисляется и сохраняется в файле.   |
| [RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| Отсрочка 2 секунд с 10 max повторной попытки |Определяет политику повтора запросов. Ошибки соединения производятся в этом примере [ExponentialRetry](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.exponentialretry?view=azure-dotnet) политика настроена с растущим 2 секунды, а также максимальное число повторных попыток 10. Этот параметр важен, когда приложение приблизится попадание [больших двоичных объектов целевые показатели масштабируемости хранилища](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).  |

`UploadFilesAsync` Задача отображается в следующем примере:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestionOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

Следующий пример представляет собой выходные данные усеченное приложения, в системе Windows.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Проверять соединения

При передаче файлов, можно проверить число одновременных подключений к вашей учетной записи хранилища. Откройте **командной строки** и тип `netstat -a | find /c "blob:https"`. Эта команда показывает количество подключений, открытых в настоящее время с помощью `netstat`. В следующем примере показано, как выходные данные для просмотра при запуске учебника самостоятельно. Как видно из примера, 800 подключения были открыты при загрузке произвольных файлов учетной записи хранилища. Это значение изменяется на протяжении выполнения передачи. Передав параллельного блока фрагментами, значительно сокращается количество времени, необходимые для передачи содержимого.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Дальнейшие действия

Во второй части серии вы узнали о загрузке больших объемов случайные данные для учетной записи хранения в параллельном режиме, такими как:

> [!div class="checklist"]
> * Настройка строки подключения
> * Создание приложения
> * Выполнение приложения
> * Проверить количество подключений

Перейти к части три ряда для загрузки больших объемов данных из учетной записи хранения.

> [!div class="nextstepaction"]
> [Отправка больших объемов больших файлов одновременно для учетной записи хранения](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
