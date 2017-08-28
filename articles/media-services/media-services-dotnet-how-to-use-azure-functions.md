---
title: "Разработка Функций Azure с помощью служб мультимедиа"
description: "В этой статье описывается разработка Функций Azure с помощью служб мультимедиа на портале Azure."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: e5103bf2c0bc1fb29e75407a901fdc3e371acb8c
ms.contentlocale: ru-ru
ms.lasthandoff: 08/16/2017

---
#<a name="develop-azure-functions-with-media-services"></a>Разработка Функций Azure с помощью служб мультимедиа

В этой статье описывается создание Функций Azure, использующих службы мультимедиа. Функция Azure, определенная в этом разделе, отслеживает контейнер учетной записи хранения **input** на наличие новых MP4-файлов. После попадания файла в контейнер хранилища триггер большого двоичного объекта выполнит функцию.

Если вы хотите изучить и развернуть существующие службы "Функции Azure", использующие службы мультимедиа Azure, ознакомьтесь с [функциями Azure для служб мультимедиа](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Этот репозиторий содержит примеры, которые используют службы мультимедиа, чтобы показать рабочие процессы, связанные с приемом содержимого напрямую от хранилища BLOB-объектов, шифрованием и записью содержимого обратно в хранилище BLOB-объектов. В нем также содержатся примеры отслеживания уведомлений заданий через объекты webhook и очереди Azure. Можно также разработать свои функции на основе примеров из репозитория [Функций Azure для служб мультимедиа](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Для развертывания функций нажмите кнопку **Развертывание в Azure**.

## <a name="prerequisites"></a>Предварительные требования

- Чтобы создавать функции, вам нужна активная учетная запись Azure. Если у вас ее нет, воспользуйтесь [бесплатной учетной записью Azure](https://azure.microsoft.com/free/).
- Если вы хотите создать Функции Azure, которые выполняют определенные действия в учетной записи служб мультимедиа Azure (AMS) или прослушивают события, отправляемые службами мультимедиа, вам необходимо создать учетную запись AMS, следуя приведенным [здесь](media-services-portal-create-account.md) инструкциям.
- Общие сведения об [использовании Функций Azure](../azure-functions/functions-overview.md). Кроме того, ознакомьтесь со следующими разделами:
    - [Привязки HTTP и webhook в функциях Azure](../azure-functions/functions-triggers-bindings.md)
    - [Настройка параметров приложения-функции Azure](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
    
## <a name="considerations"></a>Рекомендации

-  Функции Azure, стоимость которых рассчитывается, исходя из плана потребления, имеют ограничение времени ожидания 5 минут.

## <a name="create-a-function-app"></a>Создание приложения-функции

1. Перейдите на [портал Azure](http://portal.azure.com) и войдите, используя свою учетную запись Azure.
2. Создайте приложение-функцию, как описано [здесь](../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Учетная запись хранения, указанная в переменной среды **StorageConnection** (см. следующий шаг), должна находиться в том же регионе, что и ваше приложение.

## <a name="configure-function-app-settings"></a>Настройка параметров приложения-функции

При разработке функций служб мультимедиа удобно добавить переменные среды, которые будут использоваться в функциях. Чтобы настроить параметры приложения, щелкните ссылку "Настроить параметры приложения". Дополнительную информацию см. в разделе [Настройка параметров приложения-функции Azure](../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Например:

![Параметры](./media/media-services-azure-functions/media-services-azure-functions001.png)

В функции, определенной в этой статье, предполагается, что в параметрах приложения настроены следующие переменные среды.

**AMSAccount**: *имя учетной записи AMS* (например, testams).

**AMSKey**: *ключ учетной записи AMS* (например, IHOySnH+XX3LGPfraE5fKPl0EnzvEPKkOPKCr59aiMM=).

**MediaServicesStorageAccountName**: *имя учетной записи хранения* (например, testamsstorage).

**MediaServicesStorageAccountKey**: *ключ учетной записи хранения* (например, xx7RN7mvpcipkuXvn5g7jwxnKh5MwYQ или awZAzkSIxQA8tmCtn93rqobjgjt41Wb0zwTZWeWQHY5kSZF0XXXXXX==).

**StorageConnection**: *подключения к хранилищу* (например, DefaultEndpointsProtocol=https;AccountName=testamsstorage;AccountKey=xx7RN7mvpcipkuXvn5g7jwxnKh5MwYQ/awZAzkSIxQA8tmCtn93rqobjgjt41Wb0zwTZWeWQHY5kSZF0XXXXX==).

## <a name="create-a-function"></a>Создание функции

После развертывания приложения-функции его можно найти в Функциях Azure **службы приложений**.

1. Выберите свое приложение-функцию и щелкните **Новая функция**.
2. Выберите язык **C#** и сценарий **Обработка данных**.
3. Выберите шаблон **BlobTrigger**. Эта функция активируется каждый раз, когда в контейнер **input** передается большой двоичный объект. На следующем шаге в **Path** указывается имя **input**.

    ![файлов](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. После выбора **BlobTrigger** на странице появится несколько дополнительных элементов управления.

    ![файлов](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Щелкните **Создать**. 


## <a name="files"></a>Файлы

Функция Azure связана с файлами кода и другими файлами, описание которых представлено в данной статье. По умолчанию она связана с файлами **function.json** и **run.csx** (C#). Вам потребуется добавить файл **project.json**. Ниже приведены определения этих файлов.

![файлов](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Файл function.json определяет привязки функций и другие параметры конфигурации. В среде выполнения этот файл используется для определения событий, которые необходимо отслеживать, и способа передачи данных в выполнение функции и возвращения данных из него. Дополнительные сведения см. в статье [Привязки HTTP и webhook в функциях Azure](../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Чтобы функция не выполнялась, задайте для свойства **disabled** значение **true**. 


Ниже приведен пример файла **function.json**.

    {
    "bindings": [
      {
        "name": "myBlob",
        "type": "blobTrigger",
        "direction": "in",
        "path": "input/{fileName}.mp4",
        "connection": "StorageConnection"
      }
    ],
    "disabled": false
    }

### <a name="projectjson"></a>project.json

Файл project.json содержит зависимости. Ниже приведен пример файла **project.json**, содержащего необходимые пакеты служб мультимедиа Azure для .NET из NuGet. Обратите внимание, что номера версий изменятся после выпуска последних обновлений для пакетов, поэтому следует убедиться в наличии самых последних версий. 

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

Это код C# для функции.  Функция, определенная ниже, отслеживает контейнер учетной записи хранения **input** (который был указан в пути) на наличие новых MP4-файлов. После попадания файла в контейнер хранилища триггер большого двоичного объекта выполнит функцию.
    
В примере, определенном в этом разделе, демонстрируется, 

1. как принять ресурс в учетной записи служб мультимедиа (скопировать большой двоичный объект в ресурс AMS) и 
2. как отправить задание кодирования, использующее предустановку Adaptive Streaming из Media Encoder Standard.

В реальном сценарии скорее всего потребуется отслеживать ход выполнения задания, а затем опубликовать закодированный ресурс. Дополнительные сведения см. в статье [Использование объектов Webhook Azure для наблюдения за уведомлениями о заданиях служб мультимедиа с использованием .NET](media-services-dotnet-check-job-progress-with-webhooks.md). Дополнительные примеры приведены в разделе [функций Azure для служб мультимедиа](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

После определения функции щелкните **Сохранить и запустить**.

    #r "Microsoft.WindowsAzure.Storage"
    #r "Newtonsoft.Json"
    #r "System.Web"

    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Auth;

    private static readonly string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    private static readonly string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");

    static string _storageAccountName = Environment.GetEnvironmentVariable("MediaServicesStorageAccountName");
    static string _storageAccountKey = Environment.GetEnvironmentVariable("MediaServicesStorageAccountKey");

    private static CloudStorageAccount _destinationStorageAccount = null;

    // Field for service context.
    private static CloudMediaContext _context = null;
    private static MediaServicesCredentials _cachedCredentials = null;

    public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
    {
        // NOTE that the variables {fileName} here come from the path setting in function.json
        // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
        // was dropped into the input container for the function. 

        // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
        // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

        log.Info($"C# Blob trigger function processed: {fileName}.mp4");
        log.Info($"Using Azure Media Services account : {_mediaServicesAccountName}");


        try
        {
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                _mediaServicesAccountName,
                _mediaServicesAccountKey);

        // Used the chached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);

        // Step 1:  Copy the Blob into a new Input Asset for the Job
        // ***NOTE: Ideally we would have a method to ingest a Blob directly here somehow. 
        // using code from this sample - https://azure.microsoft.com/en-us/documentation/articles/media-services-copying-existing-blob/

        StorageCredentials mediaServicesStorageCredentials =
            new StorageCredentials(_storageAccountName, _storageAccountKey);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

        }
        catch (Exception ex)
        {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


    public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
        IAsset newAsset = null;

        try{
            Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
            newAsset = await copyAssetTask;
            log.Info($"Asset Copied : {newAsset.Id}");
        }
        catch(Exception ex){
            log.Info("Copy Failed");
            log.Info($"ERROR : {ex.Message}");
            throw ex;
        }

        return newAsset;
    }

    /// <summary>
    /// Creates a new asset and copies blobs from the specifed storage account.
    /// </summary>
    /// <param name="blob">The specified blob.</param>
    /// <returns>The new asset.</returns>
    public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
    {
         //Get a reference to the storage account that is associated with the Media Services account. 
        StorageCredentials mediaServicesStorageCredentials =
        new StorageCredentials(_storageAccountName, _storageAccountKey);
        _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);

        // Create a new asset. 
        var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
        log.Info($"Created new asset {asset.Name}");

        IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
        TimeSpan.FromHours(4), AccessPermissions.Write);
        ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
        CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

        // Get the destination asset container reference
        string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
        CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

        try{
        assetContainer.CreateIfNotExists();
        }
        catch (Exception ex)
        {
        log.Error ("ERROR:" + ex.Message);
        }

        log.Info("Created asset.");

        // Get hold of the destination blob
        CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

        // Copy Blob
        try
        {
        using (var stream = await blob.OpenReadAsync()) 
        {            
            await destinationBlob.UploadFromStreamAsync(stream);          
        }

        log.Info("Copy Complete.");

        var assetFile = asset.AssetFiles.Create(blob.Name);
        assetFile.ContentFileSize = blob.Properties.Length;
        assetFile.IsPrimary = true;
        assetFile.Update();
        asset.Update();
        }
        catch (Exception ex)
        {
        log.Error(ex.Message);
        log.Info (ex.StackTrace);
        log.Info ("Copy Failed.");
        throw;
        }

        destinationLocator.Delete();
        writePolicy.Delete();

        return asset;
    }
##<a name="test-your-function"></a>Тестирование функции

Чтобы протестировать функцию, необходимо передать MP4-файл в контейнер учетной записи хранения **input**, указанный в строке подключения.  

## <a name="next-step"></a>Дальнейшие действия

На этом этапе вы готовы начать разработку приложения служб мультимедиа. 
 
Дополнительные сведения и полные примеры и решения на основе Функций Azure и Logic Apps с использованием служб мультимедиа Azure, предназначенные для создания рабочих процессов, создающих пользовательское содержимое, можно изучить на [примере интеграции Функций Azure и служб мультимедиа с помощью .NET, приведенном на сайте GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).

Кроме того, ознакомьтесь с разделом [Использование объектов Webhook Azure для наблюдения за уведомлениями о заданиях служб мультимедиа с использованием .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


