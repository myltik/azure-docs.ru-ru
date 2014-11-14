<properties urlDisplayName="Create Encrypted Asset and Upload to Storage" pageTitle="Создание зашифрованного актива и его отправка в хранилище Azure" metaKeywords="" description="Узнайте, как включить контент мультимедиа в службы мультимедиа, создав и отправив зашифрованный актив." metaCanonical="" services="media-services" documentationCenter="" title="Практическое руководство: Создание зашифрованного актива и его отправка в хранилище" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <a name="create-asset"> </a><span class="short header">Практическое руководство. Создание зашифрованного актива и его отправка в хранилище</span>

Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущая статья: [Настройка компьютера для служб мультимедиа][Настройка компьютера для служб мультимедиа].

Чтобы загрузить мультимедиа-контент в службы мультимедиа, сначала создайте актив и добавьте в него файлы, а затем отправьте актив в службы мультимедиа. Этот процесс называется "потреблением контента".

При создании актива можно указать три различных параметра шифрования.

-   **AssetCreationOptions.None**: без шифрования. Если вы хотите создать незашифрованный актив, задайте этот параметр.
-   **AssetCreationOptions.CommonEncryptionProtected**: для файлов CENC. Примером служит набор файлов, уже зашифрованных PlayReady.
-   **AssetCreationOptions.StorageEncrypted**: шифрование хранилища. Шифрование входного файла до его загрузки в хранилище Azure.

> WACOM.NOTE
> Службы мультимедиа обеспечивают шифрование активов на диске в хранилище, в отличие от шифрования при передаче по сети, как в Digital Rights Manager (DRM).

Пример кода ниже выполняет следующие действия:

-   Создает пустой актив.
-   Создает экземпляр AssetFile, который нужно связать с активом.
-   Создает экземпляр AccessPolicy, определяющий разрешения и длительность доступа к активам.
-   Создает экземпляр локатора, который предоставляет доступ к активам.
-   Передает один файл мультимедиа в службы мультимедиа.

<!-- -->

    static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
    {
        var asset = _context.Assets.Create(assetName, assetCreationOptions);

        Console.WriteLine("Asset name: " + asset.Name);
        Console.WriteLine("Time created: " + asset.Created.Date.ToString());

        return asset;
    }

    static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
    {
        var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
        var asset = CreateEmptyAsset(assetName, assetCreationOptions);

        var fileName = Path.GetFileName(singleFilePath);

        var assetFile = asset.AssetFiles.Create(fileName);

        Console.WriteLine("Created assetFile {0}", assetFile.Name);
        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

        return asset;
    }

В следующем примере кода показано, как создать актив и отправить несколько файлов.

    static public IAsset CreateAssetAndUploadMultipleFiles( AssetCreationOptions assetCreationOptions, string folderPath)
    {
        var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

        var asset = CreateEmptyAsset(assetName, assetCreationOptions);

        var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                            AccessPermissions.Write | AccessPermissions.List);
        var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

        var blobTransferClient = new BlobTransferClient();
        blobTransferClient.NumberOfConcurrentTransfers = 20;
        blobTransferClient.ParallelTransferThreadCount = 20;

        blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

        var filePaths = Directory.EnumerateFiles(folderPath);

        Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

        if (!filePaths.Any())
        {
            throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
        }

        var uploadTasks = new List<Task>();
        foreach (var filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            Console.WriteLine("Created assetFile {0}", assetFile.Name);
                    
            // It is recommended to validate AccestFiles before upload. 
            Console.WriteLine("Start uploading of {0}", assetFile.Name);
            uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
        }

        Task.WaitAll(uploadTasks.ToArray());
        Console.WriteLine("Done uploading the files");

        blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

        locator.Delete();
        accessPolicy.Delete();

        return asset;
    }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }

## Дальнейшие действия

После загрузки актива в службы мультимедиа перейдите к статье [Получение процессора мультимедиа][Получение процессора мультимедиа].

  [Настройка компьютера для служб мультимедиа]: http://go.microsoft.com/fwlink/?LinkID=301751&clcid=0x409
  [Получение процессора мультимедиа]: ../media-services-get-media-processor/
