<properties linkid="develop-media-services-how-to-guides-deliver-media-assets" urlDisplayName="Доставка мультимедийных активов" pageTitle="Доставка мультимедийных активов — Azure" metaKeywords="" description="Ознакомьтесь с возможностями доставки мультимедийных активов, которые были переданы в службы мультимедиа Azure. Примеры кода написаны на C# и используют пакет SDK служб мультимедиа для .NET." metaCanonical="" services="media-services" documentationCenter="" title="Доставка актива путем загрузки" authors="migree" solutions="" manager="" editor="" />





<h1>Доставка актива путем загрузки</h1>
Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущий раздел: [Практическое руководство. Управление активами](http://go.microsoft.com/fwlink/?LinkID=301815&clcid=0x409).

В этом разделе обсуждаются возможности доставки мультимедийных активов, переданных в службы мультимедиа. Для доставки контента служб мультимедиа можно использовать различные сценарии. Можно загрузить мультимедийные активы или получить к ним доступ с помощью указателя. Можно отправить мультимедийный контент в другое приложение или другой поставщик контента. Для повышения производительности и масштабируемости можно также доставлять контент с помощью сети доставки содержимого (CDN), например Azure CDN.

В этом примере показано, как загрузить мультимедийные активы из служб мультимедиа. Код запрашивает задания, связанные с учетной записью служб мультимедиа, по идентификатору задания и обращается к соответствующей коллекции **OutputMediaAssets** (которая представляет собой набор из одного или нескольких выходных мультимедийных активов, являющихся результатом выполнения задания). В этом примере показано, как загрузить выходные мультимедийные активы из задания, но вы можете применить тот же подход для загрузки других активов.

<pre><code> 
// Download the output asset of the specified job to a local folder.
static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
{
    // This method illustrates how to download a single asset. 
    // However, you can iterate through the OutputAssets
    // collection, and download all assets if there are many. 

    // Get a reference to the job. 
    IJob job = GetJob(jobId);

    // Get a reference to the first output asset. If there were multiple 
    // output media assets you could iterate and handle each one.
    IAsset outputAsset = job.OutputMediaAssets[0];

	// Create a SAS locator to download the asset
    IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

    BlobTransferClient blobTransfer = new BlobTransferClient
    {
        NumberOfConcurrentTransfers = 20,
        ParallelTransferThreadCount = 20
    };

    var downloadTasks = new List&lt;Task&gt;();
    foreach (IAssetFile outputFile in outputAsset.AssetFiles)
    {
        // Use the following event handler to check download progress.
        outputFile.DownloadProgressChanged += DownloadProgress;

        string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

        Console.WriteLine("File download path:  " + localDownloadPath);

        downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

        outputFile.DownloadProgressChanged -= DownloadProgress;
    }

    Task.WaitAll(downloadTasks.ToArray());

    return outputAsset;
}

static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
{
    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
}
</code></pre>

Дополнительные сведения о доставке ресурсов см. в следующих статьях:
<ul>
<li><a href="http://msdn.microsoft.com/ru-ru/library/jj129575.aspx">Доставка ресурсов с помощью служб носителей для .NET</a></li>
<li><a href="http://msdn.microsoft.com/ru-ru/library/jj129578.aspx">Доставка ресурсов с помощью API-интерфейса REST служб носителей</a></li>
</ul>

<h2>Следующие шаги</h2>
В этом разделе описана загрузка актива из хранилища Azure. Для получения сведений о других способах доставки активов перейдите в раздел [Практическое руководство. Доставка контента потоковой передачи](http://go.microsoft.com/fwlink/?LinkID=301942).

