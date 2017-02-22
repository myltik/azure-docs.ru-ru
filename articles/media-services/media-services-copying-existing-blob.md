---
title: "Копирование больших двоичных объектов из учетной записи хранения в файл служб мультимедиа Microsoft Azure | Документация Майкрософт"
description: "В этом разделе показано, как копировать существующий большой двоичный объект в ресурс служб мультимедиа. В примере кода используются расширения пакета SDK служб мультимедиа Azure для .NET."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: b74fbba254f73c1df388b2b0ff9134cc7b8a31e8
ms.openlocfilehash: acc4780ea16f5a5ef0b87788b72595f29f6dcd89


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>Копирование существующих BLOB-объектов в файл служб мультимедиа
В этой статье демонстрируется, как копировать большие двоичные объекты из учетной записи хранения в файл служб мультимедиа Azure (AMS) с помощью [расширений пакета SDK служб мультимедиа Azure для .NET](https://github.com/Azure/azure-sdk-for-media-services-extensions/).

Методы этого расширения работают со следующими компонентами:

- обычные файлы;
- файлы динамического архива (в формате FragBlob);
- исходные и целевые файлы, принадлежащие к разным учетным записям служб мультимедиа (или даже расположенные в разных центрах обработки данных). Но за это может взиматься плата. Дополнительные сведения о ценах см. [здесь](https://azure.microsoft.com/pricing/#header-11).

> [!NOTE]
> Не следует пытаться изменить содержимое контейнеров больших двоичных объектов, созданных службами мультимедиа, без использования интерфейсов API служб мультимедиа.
> 

В этом разделе представлено два образца кода.

1. Первый копирует большие двоичные объекты из ресурса в одной учетной записи AMS в новый ресурс в другой учетной записи AMS.
2. Второй копирует большие двоичные объекты из произвольной учетной записи хранения в новый ресурс в учетной записи AMS.

## <a name="copy-blobs-between-two-ams-accounts"></a>Копирование больших двоичных объектов между двумя учетными записями AMS  

### <a name="prerequisites"></a>Предварительные требования

Две учетные записи служб мультимедиа. Дополнительные сведения см. в статье [Создание учетной записи служб мультимедиа Azure с помощью портала Azure](media-services-portal-create-account.md).

### <a name="download-sample"></a>Скачивание образца
Можно выполнить действия, описанные в этой статье, или просто [скачать](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/) пример с используемым в этой статье кодом.

### <a name="set-up-your-project"></a>Настройка проекта

1. Чтобы создать новое решение, содержащее проект консольного приложения на C#, используйте Visual Studio. 
3. Используйте пакет [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions), чтобы установить расширения пакета SDK служб мультимедиа Azure для .NET. При установке этого пакета также устанавливается пакет SDK .NET служб мультимедиа и добавляются все остальные необходимые зависимости.
4. Добавьте другие ссылки, необходимые для этого проекта: System.Configuration.
6. Добавьте раздел appSettings в файл CONFIG и обновите значения в соответствии со значениями учетных записей служб мультимедиа и хранилища, а также идентификатора исходного файла. 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

### <a name="copy-blobs-from-an-asset-in-one-ams-account-into-an-asset-in-another-ams-account"></a>Копирование больших двоичных объектов из ресурса в одной учетной записи AMS в ресурс в другой учетной записи AMS

В следующем коде используется метод расширения **IAsset.Copy** для копирования всех файлов из исходного ресурса (файла) в целевой с помощью одного расширения. Обратите внимание на дополнительные перегрузки, связанные с асинхронной поддержкой.

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="copy-blobs-from-a-storage-account-into-an-ams-account"></a>Копирование больших двоичных объектов из учетной записи хранения в учетную запись AMS 

### <a name="prerequisites"></a>Предварительные требования

- Одна учетная запись хранения, из которой вы будете копировать BLOB-объекты.
- Одна учетная запись AMS, в которую вы будете копировать BLOB-объекты.

### <a name="set-up-your-project"></a>Настройка проекта

1. Чтобы создать новое решение, содержащее проект консольного приложения на C#, используйте Visual Studio. 
3. Используйте пакет [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions), чтобы установить расширения пакета SDK служб мультимедиа Azure для .NET. При установке этого пакета также устанавливается пакет SDK .NET служб мультимедиа и добавляются все остальные необходимые зависимости.
4. Добавьте другие ссылки, необходимые для этого проекта: System.Configuration.
6. Добавьте раздел appSettings в файл .config и обновите значения в соответствии с параметрами учетных записей AMS для исходного и целевого хранилищ.
   
          <appSettings>
            <add key="MediaServicesAccountName" value="name" />
            <add key="MediaServicesAccountKey" value="key" />
            <add key="MediaServicesStorageAccountName" value="name" />
            <add key="MediaServicesStorageAccountKey" value="key" />
            <add key="SourceStorageAccountName" value="name" />
            <add key="SourceStorageAccountKey" value="key" />
          </appSettings>

### <a name="copy-blobs-from-some-storage-account-into-a-new-asset-in-a-ams-account"></a>Копирование больших двоичных объектов из произвольной учетной записи хранения в новый ресурс в учетной записи AMS

Следующий код копирует большие двоичные объекты из учетной записи хранения в ресурс служб мультимедиа. 

    using System;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _mediaServicesStorageAccountName =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
            private static readonly string _mediaServicesStorageAccountKey =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
            private static readonly string _sourceStorageAccountName =
         ConfigurationManager.AppSettings["SourceStorageAccountName"];
            private static readonly string _sourceStorageAccountKey =
                ConfigurationManager.AppSettings["SourceStorageAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static CloudStorageAccount _sourceStorageAccount = null;
            private static CloudStorageAccount _destinationStorageAccount = null;
    
            static void Main(string[] args)
            {
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey));
    
                _sourceStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_sourceStorageAccountName, 
                        _sourceStorageAccountKey), true);
    
                _destinationStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_mediaServicesStorageAccountName, 
                        _mediaServicesStorageAccountKey), true);
                
                CloudBlobClient sourceCloudBlobClient = 
                    _sourceStorageAccount.CreateCloudBlobClient();
                CloudBlobContainer sourceContainer = 
                    sourceCloudBlobClient.GetContainerReference("NameOfBlobContainerYouWantToCopy");
    
                CreateAssetFromExistingBlobs(sourceContainer);
            }
            
            static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer sourceBlobContainer)
            {
                CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
    
                // Create a new asset. 
                IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);
    
                IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                    TimeSpan.FromHours(24), AccessPermissions.Write);
    
                ILocator destinationLocator = 
                    _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    
                // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
                CloudBlobContainer destAssetContainer =
                    destBlobStorage.GetContainerReference((new Uri(destinationLocator.Path)).Segments[1]);
    
                if (destAssetContainer.CreateIfNotExists())
                {
                    destAssetContainer.SetPermissions(new BlobContainerPermissions
                    {
                        PublicAccess = BlobContainerPublicAccessType.Blob
                    });
                }
    
                var blobList = sourceBlobContainer.ListBlobs();
    
                foreach (var sourceBlob in blobList)
                {
                    var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
    
                    ICloudBlob destinationBlob = destAssetContainer.GetBlockBlobReference(assetFile.Name);
    
                    // Call the CopyBlobHelpers.CopyBlobAsync extension method to copy blobs.
                    using (Task task = 
                        CopyBlobHelpers.CopyBlobAsync((CloudBlockBlob)sourceBlob, 
                            (CloudBlockBlob)destinationBlob, 
                            new BlobRequestOptions(), 
                            CancellationToken.None))
                    {
                        task.Wait();
                    }
    
                    assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                    assetFile.Update();
                    Console.WriteLine("File {0} is of {1} size", assetFile.Name, assetFile.ContentFileSize);
                }
    
                asset.Update();
    
                destinationLocator.Delete();
                writePolicy.Delete();
    
                // Set the primary asset file.
                // If, for example, we copied a set of Smooth Streaming files, 
                // set the .ism file to be the primary file. 
                // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
                var ismAssetFiles = asset.AssetFiles.ToList().
                    Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
    
                // The following code assigns the first .ism file as the primary file in the asset.
                // An asset should have one .ism file.  
                ismAssetFiles.First().IsPrimary = true;
                ismAssetFiles.First().Update();
    
                return asset;
            }
        }
    }
    
## <a name="next-steps"></a>Дальнейшие действия

Теперь можно закодировать отправленные ресурсы. Дополнительную информацию см. в статье, посвященной [кодированию ресурсов](media-services-portal-encode.md).

Можно также использовать функции Azure для запуска задания кодирования на основе файла, поступающего в настроенный контейнер. Дополнительные сведения см. в [этом примере](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO2-->


