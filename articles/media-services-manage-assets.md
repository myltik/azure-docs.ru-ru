<properties linkid="develop-media-services-how-to-guides-manage-assets" urlDisplayName="Управление активами служб мультимедиа" pageTitle="Как управлять активами в службах мультимедиа — Azure" metaKeywords="" description="Узнайте, как управлять активами в службах мультимедиа. Можно также управлять заданиями, задачами, политиками доступа, указателями и многим другим. Примеры кода написаны на C#, в них используется пакет SDK служб мультимедиа для .NET." metaCanonical="" services="media-services" documentationCenter="" title="Практическое руководство. Управление активами в хранилище" authors="migree" solutions="" manager="" editor="" />




<h1>Практическое руководство. Управление активами в хранилище</h1>

Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущий раздел: [Практическое руководство. Защита актива](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409).

После создания активов мультимедиа и их передачи службам мультимедиа можно получить доступ к активам и управлять ими на сервере. Можно также управлять другими объектами на сервере, являющиеся частью служб мультимедиа, включая задания, задачи, политики доступа, указатели и многое другое.

В следующем примере показано, как запрашивать актив по его коду. 
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

Дополнительные сведения об управлении активами см. в следующих статьях:
<ul>
<li><a href="http://msdn.microsoft.com/ru-ru/library/jj129589.aspx">Управление активами с помощью пакета служб мультимедиа для .NET</a></li>
<li><a href="http://msdn.microsoft.com/ru-ru/library/jj129583.aspx">Управление активами с помощью API REST служб мультимедиа</a></li></ul>


<h2>Дальнейшие действия</h2>
Теперь, когда вы знаете, как управлять активами, перейдите к разделу [Доставка актива путем загрузки](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409).

