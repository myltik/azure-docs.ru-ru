---
title: Копирование существующего большого двоичного объекта в ресурс служб мультимедиа | Microsoft Docs
description: В этом разделе показано, как копировать существующий большой двоичный объект в ресурс служб мультимедиа.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/13/2016
ms.author: juliako

---
# <a name="copying-an-existing-blob-into-a-media-services-asset"></a>Копирование существующего BLOB-объекта в файл служб мультимедиа
В этом разделе показано, как копировать большие двоичные объекты из учетной записи хранения в новый ресурс служб мультимедиа Microsoft Azure.

Ваши BLOB-объекты могут храниться в учетной записи хранения, которая связана или не связана с учетной записью служб мультимедиа. В этом разделе демонстрируется, как копировать большие двоичные объекты из учетной записи хранения в ресурс служб мультимедиа. Обратите внимание, что объекты также можно копировать между центрами обработки данных. Но за это может взиматься плата. Дополнительные сведения о ценах см. [здесь](https://azure.microsoft.com/pricing/#header-11).

> [!NOTE]
> Не следует пытаться изменить содержимое контейнеров больших двоичных объектов, созданных службами мультимедиа, без использования интерфейсов API служб мультимедиа.
> 
> 

## <a name="download-sample"></a>Скачивание образца
Скачать и запустить пример вы можете [здесь](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/).

## <a name="prerequisites"></a>Предварительные требования
* Две учетные записи служб мультимедиа в новой или существующей подписке Azure. Дополнительные сведения см. в статье [Создание учетной записи служб мультимедиа Azure с помощью портала Azure](media-services-portal-create-account.md).
* Операционные системы: Windows 10, Windows 7, Windows 2008 R2 или Windows 8.
* .NET Framework 4.5
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate или Express) или более поздняя версия.

## <a name="set-up-your-project"></a>Настройка проекта
В этом разделе вы создадите и настроите проект консольного приложения на C#.

1. Чтобы создать новое решение, содержащее проект консольного приложения на C#, используйте Visual Studio. 
2. В поле "Имя" введите CopyExistingBlobsIntoAsset и нажмите кнопку "ОК".
3. С помощью Nuget добавьте ссылки на связанные библиотеки DLL служб мультимедиа. В главном меню Visual Studio последовательно выберите элементы ИНСТРУМЕНТЫ > Диспетчер пакетов библиотеки > Консоль диспетчера пакетов. В окне консоли введите Install-Package windowsazure.mediaservices и нажмите клавишу ВВОД.
4. Добавьте другие ссылки, необходимые для этого проекта: System.Configuration.
5. Замените операторы using, добавленные в файл Programs.cs по умолчанию, на следующие:
   
        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure;
        using System.Web;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Auth;
6. Добавьте раздел appSettings в файл .config и обновите значения в соответствии со значениями ключа и имени служб мультимедиа и хранилища. 
   
        <appSettings>
          <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
          <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
          <add key="MediaServicesStorageAccountName" value="Media-Services-Storage-Account-Name"/>
          <add key="MediaServicesStorageAccountKey" value="Media-Services-Storage-Account-Key"/>
          <add key="ExternalStorageAccountName" value="External-Storage-Account-Name"/>
          <add key="ExternalStorageAccountKey" value="External-Storage-Account-Key"/>
        </appSettings>

## <a name="copy-blobs-from-a-storage-account-into-a-media-services-asset"></a>Копирование BLOB-объектов из учетной записи хранения в файл служб мультимедиа
Пример кода ниже выполняет следующие задачи.

1. Создает экземпляр CloudMediaContext. 
2. Создает экземпляры CloudStorageAccount: _sourceStorageAccount и _destinationStorageAccount.
3. Передает файлы Smooth Streaming из локального каталога в контейнер больших двоичных объектов, который находится в _sourceStorageAccount. 
4. Создает новый файл. Контейнер больших двоичных объектов, созданный для этого ресурс-контейнера, находится в _destinationStorageAccount. 
5. Использует пакет SDK хранилища Azure для копирования указанных BLOB-объектов в контейнер, связанный с файлом.
   
   > [!NOTE]
   > Если срок действия указателя истек, операция копирования не выдает исключение.
   > 
   > 
6. Так как в данном примере копируются файлы Smooth Streaming, в нем показано, как задать ISM-файл в качестве основного файла. Например, если скопировать MP4-файл, то в качестве основного файла устанавливается MP4-файл.
7. Создает URL-адрес Smooth Streaming для указателя OnDemandOrigin, связанного с файлом. 
   
        class Program
        {
            // Read values from the App.config file. 
            static string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
            static string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            static string _storageAccountName = ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
            static string _storageAccountKey = ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
            static string _externalStorageAccountName = ConfigurationManager.AppSettings["ExternalStorageAccountName"];
            static string _externalStorageAccountKey = ConfigurationManager.AppSettings["ExternalStorageAccountKey"];
   
            private static MediaServicesCredentials _cachedCredentials = null;
            private static CloudMediaContext _context = null;
   
            private static CloudStorageAccount _sourceStorageAccount = null;
            private static CloudStorageAccount _destinationStorageAccount = null;
   
            static void Main(string[] args)
            {
            _cachedCredentials = new MediaServicesCredentials(
                    _accountName,
                    _accountKey);
            // Use the cached credentials to create CloudMediaContext.
            _context = new CloudMediaContext(_cachedCredentials);
   
            // In this example the storage account from which we copy blobs is not 
            // associated with the Media Services account into which we copy blobs.
            // But the same code will work for coping blobs from a storage account that is 
            // associated with the Media Services account.
            //
            // Get a reference to a storage account that is not associated with a Media Services account
            // (an external account).  
            StorageCredentials externalStorageCredentials =
                new StorageCredentials(_externalStorageAccountName, _externalStorageAccountKey);
            _sourceStorageAccount = new CloudStorageAccount(externalStorageCredentials, true);
   
            //Get a reference to the storage account that is associated with a Media Services account. 
            StorageCredentials mediaServicesStorageCredentials =
                new StorageCredentials(_storageAccountName, _storageAccountKey);
            _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);
   
            // Upload Smooth Streaming files into a storage account.
            string localMediaDir = @"C:\supportFiles\streamingfiles";
            CloudBlobContainer blobContainer =
                UploadContentToStorageAccount(localMediaDir);
   
            // Create a new asset and copy the smooth streaming files into 
            // the container that is associated with the asset.
            IAsset asset = CreateAssetFromExistingBlobs(blobContainer);
   
            // Get the streaming URL for the smooth streaming files 
            // that were copied into the asset.   
            string urlForClientStreaming = CreateStreamingLocator(asset);
            Console.WriteLine("Smooth Streaming URL: " + urlForClientStreaming);
   
            Console.ReadLine();
            }
   
            /// <summary>
            /// Uploads content from a local directory into the specified storage account.
            /// In this example the storage account is not associated with the Media Services account.
            /// </summary>
            /// <param name="localPath">The path from which to upload the files.</param>
            /// <returns>The container that contains the uploaded files.</returns>
            static public CloudBlobContainer UploadContentToStorageAccount(string localPath)
            {
            CloudBlobClient externalCloudBlobClient = _sourceStorageAccount.CreateCloudBlobClient();
   
            CloudBlobContainer externalMediaBlobContainer = externalCloudBlobClient.GetContainerReference("streamingfiles");
   
            externalMediaBlobContainer.CreateIfNotExists();
   
            // Upload files to the blob container.  
            DirectoryInfo uploadDirectory = new DirectoryInfo(localPath);
            foreach (var file in uploadDirectory.EnumerateFiles())
            {
                CloudBlockBlob blob = externalMediaBlobContainer.GetBlockBlobReference(file.Name);
   
                blob.UploadFromFile(file.FullName, FileMode.Open);
            }
   
            return externalMediaBlobContainer;
            }
   
            /// <summary>
            /// Creates a new asset and copies blobs from the specifed storage account.
            /// </summary>
            /// <param name="mediaBlobContainer">The specified blob container.</param>
            /// <returns>The new asset.</returns>
            static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer mediaBlobContainer)
            {
            // Create a new asset. 
            IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);
   
            IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                TimeSpan.FromHours(24), AccessPermissions.Write);
            ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
   
            CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
   
            // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
            string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
   
            CloudBlobContainer assetContainer =
                destBlobStorage.GetContainerReference(destinationContainerName);
   
            if (assetContainer.CreateIfNotExists())
            {
                assetContainer.SetPermissions(new BlobContainerPermissions
                {
                PublicAccess = BlobContainerPublicAccessType.Blob
                });
            }
   
            var blobList = mediaBlobContainer.ListBlobs();
            foreach (var sourceBlob in blobList)
            {
                var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
                CopyBlob(sourceBlob as ICloudBlob, assetContainer);
                assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                assetFile.Update();
            }
   
            asset.Update();
   
            destinationLocator.Delete();
            writePolicy.Delete();
   
            // Since we copied a set of Smooth Streaming files, 
            // set the .ism file to be the primary file. 
            // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
            SetISMFileAsPrimary(asset);
   
            return asset;
            }
   
            /// <summary>
            /// Creates the OnDemandOrigin locator in order to get the streaming URL.
            /// </summary>
            /// <param name="asset">The asset that contains the smooth streaming files.</param>
            /// <returns>The streaming URL.</returns>
            static public string CreateStreamingLocator(IAsset asset)
            {
            var ismAssetFile = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).First();
   
            // Create a 30-day readonly access policy. 
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                TimeSpan.FromDays(30),
                AccessPermissions.Read);
   
            // Create a locator to the streaming content on an origin. 
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                policy,
                DateTime.UtcNow.AddMinutes(-5));
   
            return originLocator.Path + ismAssetFile.Name + "/manifest";
            }
   
            /// <summary>
            /// Copies the specified blob into the specified container.
            /// </summary>
            /// <param name="sourceBlob">The source container.</param>
            /// <param name="destinationContainer">The destination container.</param>
            static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
            {
            var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
            {
                Permissions = SharedAccessBlobPermissions.Read,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
            });
   
            ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);
   
            if (destinationBlob.Exists())
            {
                Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
            }
            else
            {
   
                // Display the size of the source blob.
                Console.WriteLine(sourceBlob.Properties.Length);
   
                Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));
   
                while (true)
                {
                // The StartCopyFromBlob is an async operation, 
                // so we want to check if the copy operation is completed before proceeding. 
                // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                destinationBlob.FetchAttributes();
                if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                {
                    break;
                }
                //It's still not completed. So wait for some time.
                System.Threading.Thread.Sleep(1000);
                }

                // Display the size of the destination blob.
                Console.WriteLine(destinationBlob.Properties.Length);

            }
            }

            /// <summary>
            /// Sets a file with the .ism extension as a primary file.
            /// </summary>
            /// <param name="asset">The asset that contains the smooth streaming files.</param>
            static private void SetISMFileAsPrimary(IAsset asset)
            {

            //If you expect the asset to contain the .ism asset file, set the .ism file as the primary file.
            var ismAssetFiles = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();

            // The following code assigns the first .ism file as the primary file in the asset.
            // An asset should have one .ism file.  
            ismAssetFiles.First().IsPrimary = true;
            ismAssetFiles.First().Update();
            }
        }



## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!--HONumber=Oct16_HO2-->


