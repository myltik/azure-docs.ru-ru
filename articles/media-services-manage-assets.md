<properties linkid="develop-media-services-how-to-guides-manage-assets" urlDisplayName="Manage Assets in Media Services" pageTitle="How to Manage Assets in Media Services - Azure" metaKeywords="" description="Learn how to manage assets on Media Services. You can also manage jobs, tasks, access policies, locators, and more. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Manage Assets in storage" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"/>

# Практическое руководство: Управление активами в хранилище

Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущий раздел [Практическое руководство: Защита активов][].

После создания активов мультимедиа и их передачи службам мультимедиа можно получить доступ к активам и управлять ими на сервере. Можно также управлять другими объектами на сервере, являющиеся частью служб мультимедиа, включая задания, задачи, политики доступа, указатели и многое другое.

В следующем примере показано, как запрашивать актив по его идентификатору assetId.

   <pre><code>
static IAsset GetAsset(string assetId)
{
    // Use a LINQ Select query to get an asset.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Reference the asset as an IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
</code></pre> 
</p>
Чтобы получить список всех активов, доступных на сервере, можно использовать следующий метод, при котором выполняются итерации по коллекции активов и отображаются подробные сведения о каждом активе.

  <pre><code> 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());
}
</code></pre>
В следующем фрагменте кода удаляются все активы из учетной записи служб мультимедиа.

 <pre><code>
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
</code></pre>

</p>
Дополнительные сведения об управлении активами см. в следующих статьях:

-   [Управление активами с помощью пакета служб мультимедиа для .NET][]
-   [Управление активами с помощью API REST служб мультимедиа][]

</p>
## Дальнейшие действия

Теперь, когда вы знаете, как управлять активами, перейдите к разделу [Доставка актива путем загрузки][].

  [Практическое руководство: Защита активов]: http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409
  [Управление активами с помощью пакета служб мультимедиа для .NET]: http://msdn.microsoft.com/en-us/library/jj129589.aspx
  [Управление активами с помощью API REST служб мультимедиа]: http://msdn.microsoft.com/en-us/library/jj129583.aspx
  [Доставка актива путем загрузки]: http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409
