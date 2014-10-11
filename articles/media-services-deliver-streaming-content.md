<properties linkid="develop-media-services-how-to-guides-deliver-streaming-content" urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="How to Deliver Streaming Content from Media Services – Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author/>

# Практическое руководство: Доставка потокового контента

Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущий раздел [Практическое руководство: Доставка актива путем загрузки][].

Помимо загрузки мультимедиа-контента из служб мультимедиа для доставки контента можно использовать потоковую передачу с адаптивной скоростью передачи данных. Например, вы можете создать прямой URL-адрес, который называется локатором, для потоковой передачи контента на исходный сервер служб мультимедиа. Клиентские приложения, например Microsoft Silverlight, могут воспроизводить потоковый контент непосредственно из локатора.

Ниже показано, как создать исходный локатор основание для выходного актива, созданного заданием. В примере предполагается, что уже получена ссылка на актив, содержащий файлы Smooth Streaming, и в коде есть ссылка на переменную с именем **assetToStream**.

Создание исходного локатора для потоковой передачи контента:

1.  Получение ссылки на файл манифеста потоковой передачи (ISM) в активе
2.  Определение политики доступа
3.  Создание исходного локатора путем вызова метода CreateLocator
4.  Создание URL-адреса файла манифеста

В следующем примере кода показано, как реализовать эти действия:

<pre><code>
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the streaming manifest file from the  
    // collection of files in the asset. 
    var theManifest =
                        from f in assetToStream.AssetFiles
                        where f.Name.EndsWith(".ism")
                        select f;

    // Cast the reference to a true IAssetFile type. 
    IAssetFile manifestFile = theManifest.First();
    IAccessPolicy policy = null;
    ILocator originLocator = null;
            
    // Create a 30-day readonly access policy. 
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Create an OnDemandOrigin locator to the asset. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Display some useful values based on the locator.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Return the locator. 
    return originLocator;
}
</code></pre>
	
Дополнительные сведения о доставке активов см. в следующих статьях:

-   [Доставка ресурсов с помощью служб носителей для .NET][]
-   [Доставка ресурсов с помощью API-интерфейса REST служб носителей][]

</p>
## Дальнейшие действия

На данный момент мы рассмотрели доставку мультимедиа путем загрузки из хранилища Azure и с использованием Smooth Streaming. В следующем разделе, [Доставка контента HLS][], описывается доставка потокового контента с помощью службы Apple HTTP Live Streaming (HLS).

  [Практическое руководство: Доставка актива путем загрузки]: http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409
  [Доставка ресурсов с помощью служб носителей для .NET]: http://msdn.microsoft.com/en-us/library/jj129575.aspx
  [Доставка ресурсов с помощью API-интерфейса REST служб носителей]: http://msdn.microsoft.com/en-us/library/jj129578.aspx
  [Доставка контента HLS]: http://go.microsoft.com/fwlink/?LinkId=301817
