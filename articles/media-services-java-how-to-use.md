<properties 
	pageTitle="Использование служб мультимедиа (на Java) - руководство по компонентам Azure" 
	description="Описание использования служб мультимедиа Azure для выполнения стандартных задач, включая кодирование, шифрование и потоковую передачу ресурсов." 
	services="media-services" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="robmcm"/>

# Использование служб мультимедиа

В этом руководстве демонстрируется, как начать программирование с использованием служб носителей Azure на Java. Руководство включает технический обзор служб носителей, этапы настройки учетной записи Azure для служб носителей, а также код, который показывает, как выполнять типичные задачи программирования. 

## Оглавление

-   [Что такое службы мультимедиа](#media_services)
-   [Настройка учетной записи Azure для служб мультимедиа](#setup-account)
-   [Настройка для разработки с использованием служб мультимедиа](#setup-dev)
-   [Практическое руководство. использование служб мультимедиа с Java](#connect)
-   [Дополнительные ресурсы](#additional-resources)

 
## <a id="media_services"></a>Что такое службы мультимедиа

Службы мультимедиа Azure формируют расширяемую платформу, которая интегрирует оптимальные функциональные возможности платформы мультимедиа от корпорации Майкрософт и лучшие сторонние мультимедийные компоненты в Azure. Службы мультимедиа предоставляют конвейер обработки мультимедиа в облаке, что позволяет партнерам расширять или заменять технологии компонентов. Независимые поставщики программного обеспечения и поставщики мультимедиа могут использовать службы мультимедиа для создания комплексных решений в области мультимедиа. В этом обзоре рассматриваются архитектура в целом и типичные сценарии разработки для служб мультимедиа.

На следующей схеме показана базовая архитектура служб мультимедиа.

![Media Services Architecture](./media/media-services-dotnet-how-to-use/wams-01.png)

### Поддержка возможностей служб мультимедиа
Текущая версия служб мультимедиа предоставляет описанный ниже набор компонентов для разработки мультимедийных приложений в облаке. 

- **Получение**. Операции получения позволяют добавлять активы в систему, например путем их загрузки и шифрования перед помещением в хранилище Azure. В выпуске RTM службы носителей будут обеспечивать интеграцию с партнерскими компонентами для поддержки решений быстрой загрузки по протоколу UDP (User Datagram Protocol).
- **Кодирование**. Операции кодирования включают в себя кодирование, преобразование и конвертацию мультимедийных активов. Задачи кодирования можно выполнять в облаке с помощью средства кодирования мультимедиа, которое включено в службы носителей. Параметры кодирования включают следующее:
   - Использование кодировщика мультимедиа Azure и работа с набором стандартных кодеков и форматов, включая лидирующие в отрасли форматы IIS Smooth Streaming и MP4, а также преобразование в формат Apple HTTP Live Streaming.
   - Преобразование библиотек целиком или отдельных файлов с сохранением полного контроля над входными и выходными данными.
   - Большой набор поддерживаемых типов файлов, форматов и кодеков (см. [поддерживаемые типы файлов для служб мультимедиа][]).
   - Поддерживаемые виды преобразования форматов. Службы мультимедиа позволяют преобразовывать формат ISO MP4 (.mp4) в формат файла бесперебойной потоковой передачи (PIFF 1.3) (.ismv; .isma). Можно также преобразовать формат бесперебойной потоковой передачи (PIFF) в формат Apple HTTP Live Streaming (.msu8, .ts).
- **Защита**. Защита контента означает шифрование контента потоковой передачи в режиме реального времени или по требованию для безопасной передачи, хранения и доставки. Службы мультимедиа предоставляют независимое от технологий DRM решение для защиты контента.  В настоящее время поддерживаются такие технологии DRM, как Microsoft PlayReady Protection и MPEG Common Encryption. Будет доступна поддержка дополнительных технологий DRM. 
- **Поток**. Контент потоковой передачи включает в себя пересылку клиентам в режиме реального времени или по требованию, а также извлечение или загрузку конкретных файлов мультимедиа из облака. Службы мультимедиа предоставляют независимое от формата решение для контента потоковой передачи.  Службы мультимедиа обеспечивают поддержку источника потоковой передачи для форматов Smooth Streaming, Apple HTTP Live Streaming и MP4. Будет доступна поддержка дополнительных форматов. Можно также обеспечить бесперебойную доставку контента потоковой передачи с помощью сети CDN Azure или сторонних CDN, что позволяет масштабировать решение для миллионов пользователей.   

### Сценарии разработки для служб мультимедиа
Службы мультимедиа поддерживают несколько типичных сценариев разработки в области мультимедиа, которые рассматриваются в следующей таблице. 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Сценарий</th>
       <th>Описание</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>Создание сквозных рабочих процессов</td>
        <td>Создание сквозных рабочих процессов для мультимедиа полностью в облаке. Службы мультимедиа предоставляют ряд компонентов, которые можно сочетать для обработки конкретных рабочих процессов приложений, от отправки мультимедиа до распределения контента. К числу доступных возможностей относятся отправка, хранение, кодирование данных, преобразование форматов, защита контента и доставка потоковой передачи по требованию.</td>
    </tr>
    <tr>
        <td>Создание гибридных рабочих процессов</td>
        <td>Службы мультимедиа можно интегрировать с существующими средствами и процессами. Например, можно кодировать контент на сайте, а затем загружать его в службы мультимедиа для преобразования в различные форматы и доставки с помощью сети CDN Azure или сторонней сети CDN. Службы мультимедиа могут вызываться по отдельности через стандартные API-интерфейсы REST для обеспечения интеграции с внешними приложениями и службами.</td>
    </tr>
    <tr>
        <td>Предоставление поддержки облака для проигрывателей мультимедиа</td>
        <td>Можно создавать мультимедиа, управлять им и доставлять его на различные устройства (включая устройства Windows, iOS и Android) и платформы.</td>
    </tr>
  </tbody>
</table>

### Разработка клиента для служб мультимедиа
Использование пакетов SDK и платформ проигрывателя при создании клиентских приложений в области мультимедиа позволяет расширить сферу применения решения со службами мультимедиа. Эти клиенты предназначены для разработчиков, желающих создавать приложения на основе служб мультимедиа, которые предлагают единообразную среду работы пользователя на различных устройствах и платформах. В зависимости от устройств, для которых создаются клиентские приложения, есть разные варианты SDK и платформ проигрывателя от корпорации Майкрософт и сторонних партнеров.  

Ниже приводится список доступных клиентских пакетов SDK и платформ проигрывателя.  Дополнительные сведения об этих и других запланированных пакетах SDK и платформах проигрывателя, а также поддерживаемых ими функциональных возможностях см. в разделе [Разработка клиента для служб носителей]. 

#### Поддержка клиентов на Макинтошах и ПК  
Для ПК и Макинтошей можно назначить интерфейс потоковой передачи с использованием платформы Microsoft Silverlight или Adobe Open Source Media Framework.

-	[Клиент Smooth Streaming для Silverlight](http://www.iis.net/download/smoothclient)
-	[Платформа Microsoft Media: платформа проигрывателя для Silverlight](http://smf.codeplex.com/documentation)
-	[Подключаемый модуль бесперебойной потоковой передачи для OSMF 2.0](http://go.microsoft.com/fwlink/?LinkId=275022). Сведения об этом подключаемом модуле см. в разделе [Использование подключаемого модуля бесперебойной потоковой передачи для Adobe Open Source Media Framework](http://go.microsoft.com/fwlink/?LinkId=275034).

#### Приложения Windows 8
Для Windows 8 можно создавать приложения для Магазина Windows с использованием любого из поддерживаемых языков разработки и конструкций, таких как HTML, Javascript, XAML, C# и C+.

-	[Клиентский пакет SDK для потоковой передачи Smooth Streaming для Windows 8](http://go.microsoft.com/fwlink/?LinkID=246146). Дополнительную информацию о создании приложения для Магазина Windows с помощью этого SDK см. в статье [Создание приложения для Магазина Windows с бесперебойной потоковой передачей](http://go.microsoft.com/fwlink/?LinkId=271647). Сведения о том, как создать проигрыватель потоковой передачи Smooth Streaming в HTML5, см. в разделе [Пошаговое руководство: Создание первого проигрывателя с потоковой передачей Smooth Streaming на HTML5](http://msdn.microsoft.com/library/jj573656.aspx).

-	[Платформа Microsoft Media - платформа проигрывателя для приложений Магазина Windows для Windows 8](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

#### Xbox
Xbox поддерживает приложения Xbox LIVE, которые могут использовать контент бесперебойной потоковой передачи. Пакет разработки приложений Xbox LIVE (ADK) включает следующее:

-	Клиент потоковой передачи Smooth Streaming для Xbox LIVE ADK
-	Платформа Мicrosoft Media: платформа проигрывателя для Xbox LIVE ADK

#### Внедренные или специализированные устройства
Такие устройства, как подключенные телевизоры, абонентские приставки, проигрыватели Blu-Ray, приставки OTT TV и мобильные устройства, которые имеют собственную платформу разработки приложений и собственный конвейер обработки мультимедиа. Корпорация Майкрософт предоставляет перечисленные далее пакеты портирования приложений, которые могут быть лицензированы, и позволяет партнерам портировать функции воспроизведения бесперебойной потоковой передачи для соответствующей платформы.

-	[Пакет для перемещения клиента потоковой передачи Smooth Streaming](http://www.microsoft.com/ru-ru/mediaplatform/sspk.aspx)
-	[Пакет для перемещения на устройство Microsoft PlayReady](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

#### Windows Phone
Корпорация Майкрософт предоставляет пакет SDK, который можно использовать для создания приложений для работы с платным видео для Windows Phone. 

-	[Клиент Smooth Streaming для Silverlight](http://www.iis.net/download/smoothclient)
-	[Платформа Microsoft Media: платформа проигрывателя для Silverlight](http://smf.codeplex.com/documentation)

#### Устройства iOS
Для устройств iOS, включая iPhone, iPod и iPad, корпорация Майкрософт поставляет пакет SDK, который можно использовать для создания приложений для этих платформ и доставки видеоконтента высокого качества: пакет SDK для бесперебойной потоковой передачи для устройств iOS с PlayReady.  Этот пакет SDK доступен только для лицензиатов, поэтому для получения дополнительной информации следует отправить [письмо в корпорацию Майкрософт](mailto:askdrm@microsoft.com). Сведения о разработке для iOS см. в [Центре разработчиков iOS](https://developer.apple.com/devcenter/ios/index.action).

#### Устройства Android
Несколько партнеров Microsoft поставляют пакеты SDK для платформы Android, которые добавляют возможность воспроизведения бесперебойной потоковой передачи на устройстве Android. Для получения дополнительной информации о партнерах обратитесь [по электронной почте в корпорацию Майкрософт](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices).


## <a id="setup-account"></a>Настройка учетной записи Azure для служб мультимедиа

Для настройки учетной записи служб носителей следует воспользоваться порталом управления Azure. Дополнительную информацию см. в разделе [Создание учетной записи служб мультимедиа][]. После создания учетной записи на портале управления можно приступить к настройке компьютера для разработки с использованием служб носителей. 

## <a id="setup-dev"></a>Настройка для разработки с использованием служб мультимедиа

В этом разделе содержатся общие предварительные требования, необходимые для разработки с использованием пакета SDK служб носителей для Java.

### Предварительные требования

-   Учетная запись служб мультимедиа в новой или существующей подписке Azure. Дополнительную информацию см. в разделе [Создание учетной записи служб мультимедиа][].
-   Библиотеки Azure для Java, которые можно установить из [Центра разработчиков Java Azure][].

## <a if="connect"></a>Практическое руководство. Использование служб мультимедиа с Java

В следующем коде демонстрируется создание актива, загрузка файла мультимедиа в актив, выполнение задания с задачей преобразования актива, последующая загрузка выводимых файлов преобразованного актива.

Для использования этого кода необходимо настроить учетную запись служб носителей. Сведения о настройке учетной записи см. в разделе [Создание учетной записи служб носителей](http://azure.microsoft.com/manage/services/media-services/how-to-create-a-media-services-account/)

Подставьте нужные значения для переменных `clientId` и `clientSecret`. Код также использует локально хранимый файл `c:/media/MPEG4-H264.mp4`. You'll need to provide your own file to use. The code also requires an output folder, `c:/output`, который является местом для загрузки файлов результатов.

	import java.io.*;
	import java.net.URI;
	import java.net.URISyntaxException;
	import java.security.NoSuchAlgorithmException;
	import java.util.EnumSet;
	import java.util.List;
	
	import com.microsoft.windowsazure.services.blob.client.*;
	import com.microsoft.windowsazure.services.core.Configuration;
	import com.microsoft.windowsazure.services.core.ServiceException;
	import com.microsoft.windowsazure.services.core.storage.StorageException;
	import com.microsoft.windowsazure.services.media.*;
	import com.microsoft.windowsazure.services.media.models.*;
	
	public class HelloMediaServices 
	{
	
	    private static MediaContract mediaService;
	    private static AssetInfo assetToEncode, encodedAsset;
	
	    public static void main(String[] args) 
	    {
	        try 
	        {
	
	            // Set up the MediaContract object to call into the media services.
	            Init();
	            
	            // Upload a local file to a media asset.
	            Upload();
	
	            // Transform the asset.
	            Transform();
	
	            // Retrieve the URL of the asset's transformed output.
	            Download();
	
	            // Delete all assets. 
	            // When you want to delete the assets that have been uploaded, 
	            // comment out the calls to Upload(), Transfer(), and Download(), 
	            // and uncomment the following call to Cleanup().
	            //Cleanup();
	
	            System.out.println("Application completed.");
	        }
	        catch (ServiceException se) 
	        {
	            System.out.println("ServiceException encountered.");
	            System.out.println(se.getMessage());
	        }
	        catch (Exception e) 
	        {
	            System.out.println("Exception encountered.");
	            System.out.println(e.getMessage());
	        }
	    }
	
	    // Initialize the server context to get programmatic access to the Media Services programming objects.
	    // The media services URI, OAuth URI and scope can be used exactly as shown.
	    // Substitute your media service account name and access key for the clientId and clientSecret variables.
	    // You can obtain your media service account name and access key from the Media Services section
	    // of the Azure Management portal, https://manage.windowsazure.com.
	    private static void Init() throws ServiceException 
	    {
	        String mediaServiceUri = "https://media.windows.net/API/";
	        String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
	        String clientId = "your_client_id";  // Use your media service account name.
	        String clientSecret = "your_client_secret"; // Use your media service access key. 
	        String scope = "urn:WindowsAzureMediaServices";
	
	        // Specify the configuration values to use with the MediaContract object.
	        Configuration configuration = MediaConfiguration
	                .configureWithOAuthAuthentication(mediaServiceUri, oAuthUri, clientId, clientSecret, scope);
	
	        // Create the MediaContract object using the specified configuration.
	        mediaService = MediaService.create(configuration);
	        
	    }
	
	    // Upload a media file to your Media Services account.
	    // This code creates an asset, an access policy (using Write access) and a locator, 
	    // and uses those objects to upload a local file to the asset.
	    private static void Upload() throws ServiceException, FileNotFoundException, NoSuchAlgorithmException 
	    {
	    	
	    	WritableBlobContainerContract uploader;
	    	
	    	AccessPolicyInfo uploadAccessPolicy;
	    	LocatorInfo uploadLocator = null;
	    	
	        // Create an asset.
	    	assetToEncode = mediaService.create(Asset.create().setName("myAsset").setAlternateId("altId"));
	        System.out.println("Created asset with id: " + assetToEncode.getId());
	
	        // Create an access policy that provides Write access for 15 minutes.
	        uploadAccessPolicy = mediaService.create(AccessPolicy.create("uploadAccessPolicy", 
	        		                                                     15.0, 
	        		                                                     EnumSet.of(AccessPolicyPermission.WRITE)));
	        System.out.println("Created upload access policy with id: "
	                + uploadAccessPolicy.getId());
	
	        // Create a locator using the access policy and asset.
	        // This will provide the location information needed to add files to the asset.
	        uploadLocator = mediaService.create(Locator.create(uploadAccessPolicy.getId(),
	        		assetToEncode.getId(), LocatorType.SAS));
	        System.out.println("Created upload locator with id: " + uploadLocator.getId());
	
	        // Create the blob writer using the locator.
	        uploader = mediaService.createBlobWriter(uploadLocator);
	
	        // The name of the file as it will exist in your Media Services account.
	        String fileName = "MPEG4-H264.mp4";  
	
	        // The local file that will be uploaded to your Media Services account.
	        InputStream input = new FileInputStream(new File("c:/media/" + fileName));
	
	        // Upload the local file to the asset.
	        uploader.createBlockBlob(fileName, input);
	
	        // Inform Media Services about the uploaded files.
	        mediaService.action(AssetFile.createFileInfos(assetToEncode.getId()));
	        System.out.println("File uploaded.");
	        
	       
	        System.out.println("Deleting upload locator and access policy.");
	        mediaService.delete(Locator.delete(uploadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));
	        
	    }
	
	    // Create a job that contains a task to transform the asset.
	    // In this example, the asset will be transformed using the Azure Media Encoder.
	    private static void Transform() throws ServiceException, InterruptedException 
	    {
	        // Use the Azure Media Encoder, by specifying it by name.
	        // Retrieve the list of media processors that match this name.   	
	    	ListResult<MediaProcessorInfo> mediaProcessors = mediaService
	    			.list(MediaProcessor.list()
	    			.set("$filter", "Name eq 'Azure Media Encoder'"));
	    	
	    	// Use the latest version of the media processor.
	    	MediaProcessorInfo mediaProcessor = null;
	    	for (MediaProcessorInfo info : mediaProcessors)
	    	{
	    		if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0)
	    		{
	    			mediaProcessor = info;
	    		}
	    	}
	
	    	System.out.println("Using processor: " + mediaProcessor.getName() +
	    			" " + mediaProcessor.getVersion());
	
	        // Create a task with the specified media processor, in this case to transform the original asset to the H264 Broadband 720p preset.
	        // Information on the various configurations can be found at
	        // http://msdn.microsoft.com/library/windowsazure/jj129582.aspx.
	        // This example uses only one task, but others could be added.
	        Task.CreateBatchOperation task = Task.create(
	                mediaProcessor.getId(),
	                "<taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>")
	                .setConfiguration("H264 Broadband 720p").setName("MyTask");
	
	        // Create a job creator that specifies the asset, priority and task for the job. 
	        Job.Creator jobCreator = Job.create()
	            .setName("myJob")
	            .addInputMediaAsset(assetToEncode.getId())
	            .setPriority(2)
	            .addTaskCreator(task);
	
	        // Create the job within your Media Services account.
	        // Creating the job automatically schedules and runs it.
	        JobInfo jobInfo = mediaService.create(jobCreator);
	        String jobId = jobInfo.getId();
	        System.out.println("Created job with id: " + jobId);
	        // Check to see if the job has completed.
	        CheckJobStatus(jobId);
	        // Done with the job.
	
	        // Retrieve the output asset.
	        ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(jobInfo.getOutputAssetsLink()));
	        encodedAsset = outputAssets.get(0);
	    }
	
	    // Download the output assets of the transformed asset.
	    private static void Download() throws ServiceException, URISyntaxException, FileNotFoundException, StorageException, IOException 
	    {
	    	
	    	AccessPolicyInfo downloadAccessPolicy = null;
	
	        downloadAccessPolicy =
	                mediaService.create(AccessPolicy.create("Download", 15.0, EnumSet.of(AccessPolicyPermission.READ)));
	        System.out.println("Created download access policy with id: "
	                + downloadAccessPolicy.getId());
	    	
	    	LocatorInfo downloadLocator = null;
	        downloadLocator = mediaService.create(
	        		Locator.create(downloadAccessPolicy.getId(), encodedAsset.getId(), LocatorType.SAS));
	        System.out.println("Created download locator with id: " + downloadLocator.getId());        
	
	        System.out.println("Accessing the output files of the encoded asset.");
	        // Iterate through the files associated with the encoded asset.
	        for(AssetFileInfo assetFile: mediaService.list(AssetFile.list(encodedAsset.getAssetFilesLink())))
	        {
	            String fileName = assetFile.getName();
	            
	            System.out.print("Downloading file: " + fileName + ". ");
	            String locatorPath = downloadLocator.getPath();
	            int startOfSas = locatorPath.indexOf("?");
	
	            String blobPath = locatorPath + fileName;
	            if (startOfSas >= 0) 
	            {
	                blobPath = locatorPath.substring(0, startOfSas) + "/" + fileName + locatorPath.substring(startOfSas);
	            }
	            URI baseuri = new URI(blobPath);
	            CloudBlobClient blobClient;
	            blobClient = new CloudBlobClient(baseuri);
	            
	            // Ensure that you have a c:\output folder, or modify the path specified in the following statement.
	            String localFileName = "c:/output/" + fileName;
	            
	            CloudBlockBlob sasBlob;
	            sasBlob = new CloudBlockBlob(baseuri, blobClient);
	            File fileTarget = new File(localFileName);
	            
	            sasBlob.download(new FileOutputStream(fileTarget));
	            System.out.println("Download complete.");
	            
	        }
	
	        System.out.println("Deleting download locator and access policy.");
	        mediaService.delete(Locator.delete(downloadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(downloadAccessPolicy.getId()));
	      
	    }
	    
	    // Remove all assets from your Media Services account.
	    // You could instead remove assets by name or ID, etc., but for 
	    // simplicity this example removes all of them.
	    private static void Cleanup() throws ServiceException 
	    {
	        // Retrieve a list of all assets.
	        List<AssetInfo> assets = mediaService.list(Asset.list());
	
	        // Iterate through the list, deleting each asset.
	        for (AssetInfo asset: assets)
	        {
	        	System.out.println("Deleting asset named " + asset.getName() + " (" + asset.getId() + ")");
	            mediaService.delete(Asset.delete(asset.getId()));
	        }
	    }
	
	    // Helper function to check to on the status of the job.
	    private static void CheckJobStatus(String jobId) throws InterruptedException, ServiceException
	    {
	        int maxRetries = 12; // Number of times to retry. Small jobs often take 2 minutes.
	        JobState jobState = null;
	        while (maxRetries > 0) 
	        {
	            Thread.sleep(10000);  // Sleep for 10 seconds, or use another interval.
	            // Determine the job state.
	            jobState = mediaService.get(Job.get(jobId)).getState();
	            System.out.println("Job state is " + jobState);
	
	            if (jobState == JobState.Finished || 
	                jobState == JobState.Canceled || 
	                jobState == JobState.Error) 
	            {
	                // The job is done.
	                break;
	            }
	            // The job is not done. Sleep and loop if max retries 
	            // has not been reached.
	            maxRetries--;
	        }
	  
	    }
	
	}

Активы, которые можно создать, хранятся в хранилище Azure. Тем не менее, для добавления, обновления или удаления активов следует использовать только API-интерфейсы служб носителей Azure (а не API-интерфейсы хранилища Azure).

### Определение доступных мультимедийных процессоров

В приведенном выше коде использовался процессор мультимедиа с доступом при помощи имени конкретного процессора мультимедиа (при наличии нескольких версий использовалась бы последняя версия). Чтобы определить, какие процессоры мультимедиа доступны, можно использовать следующий код.

    for (MediaProcessorInfo mediaProcessor:  mediaService.list(MediaProcessor.list()))
    {
        System.out.print(mediaProcessor.getName() + ", ");
        System.out.print(mediaProcessor.getId() + ", ");  
        System.out.print(mediaProcessor.getVendor() + ", ");  
        System.out.println(mediaProcessor.getVersion());  
    }

Альтернативным образом, следующий код демонстрирует, как получить идентификатор процессора мультимедиа по имени.

    String mediaProcessorName = "Storage Decryption"; 
    EntityListOperation<MediaProcessorInfo> operation;
    MediaProcessorInfo processor;

    operation = MediaProcessor.list();
    operation.getQueryParameters().putSingle("$filter", "Name eq '" + mediaProcessorName + "'");
    processor = mediaService.list(operation).get(0); 
    System.out.println("Processor named " + mediaProcessorName + 
                       " has ID of " + processor.getId());

### Отмена задания
Следующий код показывает, как отменить задание, обработка которого еще не завершена, по идентификатору этого задания.

    mediaService.action(Job.cancel(jobId));

## Дополнительные ресурсы

Документацию Javadoc по службам мультимедиа см. в [документации по библиотекам Azure для Java][].

<!-- URLs. -->

  [Создание учетной записи служб мультимедиа]: http://go.microsoft.com/fwlink/?linkid=256662
  [Центр разработчиков Azure Java]: http://azure.microsoft.com/develop/java/
  [документации по библиотекам Azure для Java]: http://dl.windowsazure.com/javadoc/
  [Разработка клиента для служб носителей]: http://msdn.microsoft.com/library/windowsazure/dn223283.aspx


<!--HONumber=45--> 
