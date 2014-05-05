<properties linkid="develop-media-services-how-to-guides-deliver-apple-live-streaming" urlDisplayName="Доставка Apple HTTP Live Streaming (HLS)" pageTitle="Доставка Apple HTTP Live Streaming (HLS) - Azure" metaKeywords="" description="Сведения о создании локатора контента Apple HTTP Live Stream (HLS) на исходном сервере служб мультимедиа. Примеры кода написаны на C#, в них используется пакет SDK служб мультимедиа для .NET." metaCanonical="" services="media-services" documentationCenter="" title="Практическое руководство. Доставка контента потоковой передачи Apple HLS" authors="migree" solutions="" manager="" editor="" />





<h1>Практическое руководство. Доставка контента потоковой передачи Apple HLS</h1>

Данная статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущая статья: [Практическое руководство. Доставка контента потоковой передачи](http://go.microsoft.com/fwlink/?LinkID=301942&clcid=0x409).

В этом разделе демонстрируется, как создать локатор контента Apple HTTP Live Streaming (HLS) на исходном сервере служб мультимедиа. С помощью такого подхода можно создать URL-адрес контента Apple HLS и передать его на устройства Apple iOS для воспроизведения. В основе создания URL-адреса локатора лежит тот же подход. Это построение локатора пути ресурса потоковой передачи Apple HLS на исходном сервере с последующим построением полного URL-адреса, указывающий на манифест для контента потоковой передачи.

В следующем примере кода предполагается, что уже получена ссылка на ресурс потоковой передачи HLS и в коде есть ссылка на переменную с именем **assetToStream**. После выполнения этого кода для создания исходного локатора ресурса полученный URL-адрес можно использовать для воспроизведения контента потоковой передачи на устройстве под управлением iOS, таком как iPad или iPhone.

Чтобы создать локатор контента потоковой передачи Apple HLS, выполните следующие действия:

   1. Получение ссылки на файл манифеста в ресурсе
   2. Определение политики доступа
   3. Создание исходного локатора путем вызова CreateLocator
   4. Создание полного URL-адреса файла манифеста

В следующем коде показано, как реализовать эти действия:

<pre><code>
static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the HLS streaming manifest file from the  
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
    policy = _context.AccessPolicies.Create("Streaming HLS Policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

    // Create a URL to the HLS streaming manifest file. Use this for playback
    // in Apple iOS streaming clients.
    string urlForClientStreaming = originLocator.Path
        + manifestFile.Name + "/manifest(format=m3u8-aapl)";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();

    // Return the locator. 
    return originLocator;
}
</code></pre>

Дополнительные сведения о доставке ресурсов см. в следующих статьях:
<ul>
<li><a href="http://msdn.microsoft.com/ru-ru/library/jj129575.aspx">Доставка ресурсов с помощью служб мультимедиа для .NET</a></li>
<li><a href="http://msdn.microsoft.com/ru-ru/library/jj129578.aspx">Доставка ресурсов с помощью API-интерфейса REST служб мультимедиа</a></li>
</ul>

<h2>Дальнейшие действия</h2>

Этот раздел завершает серию разделов, посвященных использованию служб мультимедиа Azure. Мы рассмотрели настройку компьютера для разработки служб мультимедиа и выполнения типичных задач программирования. Дополнительные сведения о программировании служб мультимедиа см. в следующих ресурсах:

-   [Документация по службам мультимедиа Azure][]
-   [Приступая к работе с пакетом SDK служб мультимедиа для .NET][]
-   [Создание приложений с помощью пакета SDK служб мультимедиа для .NET][]
-   [Создание приложений с помощью API-интерфейса REST служб мультимедиа Azure][]
-   [Форум по службам мультимедиа][]
-	[Мониторинг учетной записи служб мультимедиа](http://www.windowsazure.com/ru-ru/manage/services/media-services/how-to-monitor-a-media-services-account/)
-	[Управление контентом в службах мультимедиа](http://www.windowsazure.com/ru-ru/manage/services/media-services/how-to-manage-content-in-media-services/)

[Документация по службам мультимедиа Azure]: http://go.microsoft.com/fwlink/?linkid=245437
[Приступая к работе с пакетом SDK служб мультимедиа для .NET]: http://go.microsoft.com/fwlink/?linkid=252966
[Создание приложений с помощью API-интерфейса REST служб мультимедиа Azure]: http://go.microsoft.com/fwlink/?linkid=252967
[Создание приложений с помощью пакета SDK служб мультимедиа для .NET]: http://go.microsoft.com/fwlink/?linkid=247821
[Форум по службам мультимедиа]: http://social.msdn.microsoft.com/Forums/ru-ru/MediaServices/threads

