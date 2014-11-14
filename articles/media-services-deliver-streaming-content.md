<properties urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="Доставка потокового контента из служб мультимедиа &mdash; Azure" metaKeywords="" description="Узнайте, как передавать потоковый контент из служб мультимедиа, используя прямой URL-адрес. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Практическое руководство: Доставка потокового контента" authors="juliako" manager="dwrede" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Практическое руководство: Доставка потокового контента

Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущий раздел [Практическое руководство: Доставка актива путем загрузки][Практическое руководство: Доставка актива путем загрузки].

Помимо загрузки мультимедиа-контента из служб мультимедиа для доставки контента можно использовать потоковую передачу с адаптивной скоростью передачи данных. Например, вы можете создать прямой URL-адрес, который называется локатором, для потоковой передачи контента на исходный сервер служб мультимедиа. Клиентские приложения, например Microsoft Silverlight, могут воспроизводить потоковый контент непосредственно из локатора.

Ниже показано, как создать исходный локатор основание для выходного актива, созданного заданием. В примере предполагается, что уже получена ссылка на актив, содержащий файлы Smooth Streaming, и в коде есть ссылка на переменную с именем **assetToStream**.

Создание исходного локатора для потоковой передачи контента:

1.  Получение ссылки на файл манифеста потоковой передачи (ISM) в активе
2.  Определение политики доступа
3.  Создание исходного локатора путем вызова метода CreateLocator
4.  Создание URL-адреса файла манифеста

В следующем примере кода показано, как реализовать эти действия:

    private static ILocator GetStreamingOriginLocator( string targetAssetID){ // Get a reference to the asset you want to stream. IAsset assetToStream = GetAsset(targetAssetID);
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

<p>
}
</code>

</pre>
</p>
Дополнительные сведения о доставке активов см. в следующих статьях:

-   [Доставка ресурсов с помощью служб носителей для .NET][Доставка ресурсов с помощью служб носителей для .NET]
-   [Доставка ресурсов с помощью API-интерфейса REST служб носителей][Доставка ресурсов с помощью API-интерфейса REST служб носителей]

</p>
## Дальнейшие действия

На данный момент мы рассмотрели доставку мультимедиа путем загрузки из хранилища Azure и с использованием Smooth Streaming. В следующем разделе, [Доставка контента HLS][Доставка контента HLS], описывается доставка потокового контента с помощью службы Apple HTTP Live Streaming (HLS).

  [Практическое руководство: Доставка актива путем загрузки]: ../media-services-deliver-asset-download/
  [Доставка ресурсов с помощью служб носителей для .NET]: http://msdn.microsoft.com/ru-ru/library/jj129575.aspx
  [Доставка ресурсов с помощью API-интерфейса REST служб носителей]: http://msdn.microsoft.com/ru-ru/library/jj129578.aspx
  [Доставка контента HLS]: ../media-services-deliver-http-live-streaming-content/
