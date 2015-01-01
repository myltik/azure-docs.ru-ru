<properties urlDisplayName="Delivering Media Assets" pageTitle="Доставка мультимедийных файлов - Azure" metaKeywords="" description="Learn about options for delivering media assets that have been uploaded to Media Services in Azure. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>Практическое руководство: доставка ресурса путем скачивания</h1>
Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущая статья: [Практическое руководство. Управление ресурсами](../media-services-manage-assets/).

В этом разделе обсуждаются возможности доставки мультимедийных активов, переданных в службы мультимедиа. Для доставки контента служб мультимедиа можно использовать различные сценарии. Можно загрузить мультимедийные активы или получить к ним доступ с помощью указателя. Можно отправить мультимедийный контент в другое приложение или другой поставщик контента. Для повышения производительности и масштабируемости можно также доставлять контент с помощью сети доставки содержимого (CDN), например Azure CDN.

В этом примере показано, как загрузить мультимедийные активы из служб мультимедиа. Код запрашивает задания, связанные с учетной записью служб мультимедиа, по идентификатору задания и обращается к соответствующей коллекции **OutputMediaAssets** (которая представляет собой набор из одного или нескольких выходных мультимедийных файлов, являющихся результатом выполнения задания). В этом примере показано, как загрузить выходные мультимедийные активы из задания, но вы можете применить тот же подход для загрузки других активов.

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

    var downloadTasks = new List<Task>();
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

<h2>Дальнейшие действия</h2>
В этом разделе описана загрузка актива из хранилища Azure. Дополнительную информацию о других способах доставки ресурсов см. в статье [Практическое руководство. Доставка содержимого потоковой передачи](../media-services-deliver-streaming-content/) .

<!--HONumber=35_1-->
