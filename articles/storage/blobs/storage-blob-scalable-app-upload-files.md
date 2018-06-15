---
title: Передача больших объемов случайных данных в параллельном режиме в службу хранилища Azure | Документация Майкрософт
description: Сведения об использовании пакета SDK Azure для передачи больших объемов случайных данных в параллельном режиме в учетную запись службы хранилища Azure
services: storage
author: roygara
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 668700cf3ff3d1a90f9639129ef2953ddca016f1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239904"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Передача больших объемов случайных данных в параллельном режиме в службу хранилища Azure

Это руководство представляет собой вторую часть цикла. В этом руководстве рассматривается развертывание приложения, которое передает большой объем случайных данных в учетную запись хранения Azure.

Из второй части цикла вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка строки подключения
> * Создание приложения
> * Выполнение приложения
> * Проверка количества подключений.

Хранилище BLOB-объектов Azure предоставляет масштабируемую службу для хранения данных. Чтобы обеспечить высокую производительность приложения, рекомендуется ознакомиться с работой хранилища BLOB-объектов. Важно понимание ограничений больших двоичных объектов Azure. Дополнительные сведения см. в разделе [Целевые показатели масштабируемости хранилища BLOB-объектов Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

[Именование разделов](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47) является еще одним важным фактором при разработке приложения с высокой производительностью, использующего большие двоичные объекты. Для масштабирования и распределения нагрузки в хранилище Azure используется схема секционирования на основе диапазонов. Эта конфигурация означает, что файлы с одинаковыми соглашениями об именовании или префиксами располагаются в одном разделе. Эта логика включает имя контейнера, в который передаются файлы. В этом руководстве используются файлы, содержащие глобальные уникальные идентификаторы (GUID) имен, а также содержимое, создаваемое случайным образом. Затем они передаются в пять разных контейнеров со случайными именами.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством вы должны прежде ознакомиться с руководством о [создании виртуальной машины и учетной записи хранения для масштабируемого приложения][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Удаленное подключение к виртуальной машине

Для создания сеанса удаленного рабочего стола с виртуальной машиной выполните команду ниже на своем локальном компьютере. Замените IP-адрес значением publicIPAddress виртуальной машины. При появлении запроса введите учетные данные, использованные при создании виртуальной машины.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Настройка строки подключения

Войдите в свою учетную запись хранения на портале Azure. В меню учетной записи хранения в разделе **Параметры** выберите **Ключи доступа**. Скопируйте **строку подключения** из основного или вторичного ключа. Войдите на виртуальную машину, созданную в рамках работы с предыдущим руководством. Откройте **командную строку** от имени администратора и выполните команду `setx` с параметром `/m`. Таким образом вы сохраните переменную среды параметра виртуальной машины. Переменная среды доступна только после перезагрузки **командной строки**. Замените **\<storageConnectionString\>** в примере ниже.

```
setx storageconnectionstring "<storageConnectionString>" /m
```

По завершении откройте другую **командную строку**, перейдите в расположение `D:\git\storage-dotnet-perf-scale-app` и введите `dotnet build`, чтобы создать приложение.

## <a name="run-the-application"></a>Выполнение приложения

Перейдите на страницу `D:\git\storage-dotnet-perf-scale-app`.

Нажмите клавишу `dotnet run`, чтобы запустить приложение. При первом выполнении команды `dotnet` заполнится локальный кэш пакета для оптимизации скорости восстановления и включения автономного доступа. Выполнение этой команды займет около минуты, и она используется только один раз.

```
dotnet run
```

Приложение создаст пять контейнеров со случайными именами и начнет отправлять файлы из промежуточного каталога в учетную запись хранения. Минимальное количество потоков, которое задает приложение, равно 100. Для параметра ограничения [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) также установлено значение 100. Это требуется, чтобы разрешить выполнение большого количества одновременных подключений при запуске приложения.

Помимо параметров ограничения для количества подключений и потоков настраиваются также параметры [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) метода [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) для использования параллелизма и отключения проверки хэша MD5. Файлы передаются в блоки размером 100 МБ. Такая конфигурация обеспечивает лучшую производительность, но может быть дорогостоящей при использовании медленной сети. Это объясняется тем, что в случае сбоя будет выполнена повторная передача всего блока.

|Свойство|Значение|ОПИСАНИЕ|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| Этот параметр разделяет передаваемый большой двоичный объект на блоки. Для обеспечения максимальной производительности это значение должно в 8 раз превышать количество ядер. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| Да| Это свойство отключает проверку хэша MD5 отправляемого содержимого. При этом передача ускоряется. Но без проверки MD5 не будет подтверждения о достоверности или целостности передаваемых файлов.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Это свойство определяет, будет ли вычисляться и сохраняться в файле хэш MD5.   |
| [RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| Интервал задержки — 2 секунды. Максимальное количество повторений — 10. |Определяет политику повтора запросов. При сбоях подключения выполняются повторные попытки подключения. В этом примере для политики [ExponentialRetry](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.exponentialretry?view=azure-dotnet) настроен интервал задержки 2 секунды с максимальным количеством повторений — 10. Этот параметр важен, когда есть вероятность превышения приложением ограничений [целевых показателей масштабируемости хранилища BLOB-объектов](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).  |

Задание `UploadFilesAsync`, показано в следующем примере:

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

В следующем примере представлены усеченные выходные данные приложения, работающего под управлением Windows.

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

### <a name="validate-the-connections"></a>Проверка подключений

Во время передачи файлов можно проверить количество одновременных подключений к учетной записи хранения. Откройте окно **командной строки** и введите `netstat -a | find /c "blob:https"`. Эта команда показывает количество подключений, которые в настоящее время открыты с помощью `netstat`. В следующем примере показан результат, аналогичный тому, который отобразится при самостоятельном запуске руководства. Как видно из примера, при передаче случайных файлов в учетную запись хранения было открыто 800 подключений. Это значение меняется на протяжении выполнения передачи. При отправке в параллельных фрагментах блока значительно сокращается количество времени, необходимое для передачи содержимого.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Дополнительная информация

Из второй части в серии вы узнали не только о передаче больших объемов случайных данных в учетную запись хранения в параллельном режиме, но и о том, как выполнять такие задачи:

> [!div class="checklist"]
> * Настройка строки подключения
> * Создание приложения
> * Выполнение приложения
> * Проверка количества подключений.

Ознакомьтесь с третьей частью в серии, чтобы узнать, как загружать большие объемы данных из учетной записи хранения.

> [!div class="nextstepaction"]
> [Передача больших объемов случайных данных в параллельном режиме в службу хранилища Azure](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
