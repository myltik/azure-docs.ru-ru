<properties 
	pageTitle="Предустановки задачи кодирования: настройка предустановок с целью управления задачами" 
	description="Вы можете передавать пользовательские файлы предустановок в кодировщик служб мультимедиа Azure. В этом разделе показано, как настроить файлы предустановок, чтобы наложить изображения на видеофайлы, совместить видеофайлы и управлять именами выходных файлов, которые создает кодировщик." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Предустановки задачи кодирования: настройка предустановок с целью управления задачами 

Вы можете передавать пользовательские файлы предустановок в кодировщик служб мультимедиа Azure. В этой статье показано, как настроить файлы предустановок для выполнения таких задач:

- наложение изображения на видео; 
- управление именами выходных файлов, которые создает кодировщик; 
- совмещение видео;
- кодирование презентаций, в которых преобладает речь.

##Управление именами выходных файлов кодировщика служб мультимедиа Azure 

По умолчанию кодировщик служб мультимедиа Azure создает имена выходных файлов, совмещая различные атрибуты входного ресурса и процесса кодирования. Каждый атрибут определяется с помощью макроса, как описано ниже.

Ниже приведен полный список макросов, которые можно использовать для именования выходных файлов. Audio Bitrate — скорость кодированного звука, указанная в килобитах в секунду.

- Audio Codec — кодек, используемый для кодирования звука. Допустимые значения: AAC, WMA и DDP.
- Channel Count — количество кодируемых каналов. Допустимые значения: 1, 2 и 6.
- Default extension — расширение файла по умолчанию. 
- Language — код языка BCP-47, который обозначает язык звуковой дорожки. Текущее значение по умолчанию: und. 
- Original File Name — имя файла, переданного в службу хранилища Azure.
- StreamId — идентификатор потока, указанный в атрибуте streamID элемента <StreamInfo> в файле с предустановками. 
- Video Codec — кодек, используемый для кодирования видео. Допустимые значения: H264 и VC1.
- Video Bitrate — скорость кодированного видео, указанная в килобитах в секунду.

Различные комбинации этих макросов позволяют определять имена файлов, которые будет создавать кодировщик служб мультимедиа. Например, по умолчанию применяется такой способ именования:

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

Способ именования файлов определяется с помощью атрибута DefaultMediaOutputFileName элемента [Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx). Например:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

Кодировщик вставит между макросами символы подчеркивания. Например, указанная выше конфигурации создаст такое имя файла: MyVideo\_H264\_4500kpbs\_AAC\_und\_ch2\_128kbps.mp4.


##Создание наложений

Кодировщик служб мультимедиа Azure позволяет наложить изображение (JPG, BMP, GIF, TIF), видео или звуковую дорожку (*.wma, *.mp3, *.wav) на существующее видео. Эта функциональность аналогична Expression Encoder 4 (пакет обновления 2).

###Наложение с помощью кодировщика служб мультимедиа

Вы можете указать, когда будет представлено наложение, с какой продолжительностью, а для наложения изображений и видео — в каком месте экрана они появятся. Кроме того, вы можете задать постепенное появление и исчезновение наложений. Звуковые и видеофайлы для наложения могут находиться в одном или нескольких активах. Наложениями управляет XML-код предустановки, который передается кодировщику. Полное описание схемы предустановки см. в разделе "Схемы кодировщика служб мультимедиа Azure". Наложения указываются в элементе <MediaFile>, как показано в приведенном ниже фрагменте кода предустановки.

	<MediaFile
	    ...
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="WholeSequence"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">
	    ...
	</MediaFile>

###Предустановки для наложений видео или изображений

Источником наложений может быть один или несколько активов. При создании наложений видео с использованием нескольких активов имя файла наложения указывается в атрибуте OverlayFileName с синтаксисом %n%, где n — отсчитываемый от нуля индекс входных активов для задачи кодирования. При создании наложений видео из одного актива имя файла наложения указывается непосредственно в атрибуте OverlayFileName, как показано в приведенных ниже фрагментах кода предустановок.

Пример 1

	<!-- Multiple Assets -->
	<MediaFile
		...
		OverlayFileName="%1%"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

Пример 2

	<!-- Single Asset -->
	<MediaFile
		...
		OverlayFileName="videoOverlay.mp4"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

Расположение и размер наложения видео регулирует атрибут OverlayRect. Размер содержимого, которое будет наложено, будет изменен в соответствии с размерами указанного прямоугольника. Для управления прозрачностью служит атрибут OverlayOpacity. Допустимые значения: 0,0–1,0, где 1,0 означает 100 % прозрачности. Наложение будет отображено в момент времени, указанный атрибутом OverlayStartTime, и прекращено в момент, указанный атрибутом OverlayEndTime. Значения OverlayStartTime и OverlayEndTime должны быть в пределах временной шкалы исходного видео. Дополнительные сведения о каждом атрибуте, характерном для наложения, см. в разделе "Схемы кодировщика служб мультимедиа Azure".

###Предустановки для наложений звука

Наложения звука могут быть в любом поддерживаемом звуковом формате. Полный список поддерживаемых форматов звуковых файлов см. в разделе "Форматы, поддерживаемые кодировщиком служб мультимедиа". Наложения звука также указываются в элементе <MediaFile>, как показано в приведенном ниже фрагменте кода предустановки.

	<MediaFile
		...
		AudioOverlayFileName="%1%" <!-- or AudioOverlayFileName=”audioOverlay.mp3” for overlays from a single asset -->
		AudioOverlayLoop="True"
		AudioOverlayLoopingGap="00:00:00"
		AudioOverlayLayoutMode="Custom"
		AudioOverlayStartTime="00:05:00"
		AudioOverlayEndTime="00:10:00"
		AudioOverlayGainLevel="2.2"
		AudioOverlayFadeInDuration="00:00:00"
		AudioOverlayFadeOutDuration="00:00:00">

Для наложений звука, хранящихся в нескольких активах, имя звукового файла наложения указывается в атрибуте AudioOverlayFileName с синтаксисом %n%, где n — отсчитываемый от нуля индекс коллекции входных активов для задачи кодирования. Для наложений звука из одного актива имя файла наложения указывается непосредственно в атрибуте AudioOverlayFileName. Атрибут AudioOverlayLayoutMode определяет, когда используется наложение звука. Если задано значение WholeSequence, звуковая дорожка используется на протяжении всего видео. Если задано значение Custom, атрибуты AudioOverlayStartTime и AudioOverlayEndTime определяют начало и окончание наложения звука. Значения OverlayStartTime и OverlayEndTime должны быть в пределах временной шкалы исходного видео. Дополнительные сведения о всех атрибутах наложения звука см. в разделе "Схемы кодировщика служб мультимедиа Azure". Наложения звука можно объединять с наложениями видео, как показано в приведенном ниже фрагменте кода предустановки:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch"
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="Custom"
	    AudioOverlayStartTime="00:05:00"
	    AudioOverlayEndTime="00:10:00"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">


###Отправка задач с наложениями

После создания файла предустановки вам нужно выполнить такие действия:

- отправить активы;
- загрузить конфигурацию предустановки (обратите внимание, что в приведенном ниже коде предполагается указанная выше предустановка);
- создать задание;
- получить ссылку на кодировщик служб мультимедиа;
- создать задачу с указанием коллекции входных активов, конфигурации предустановки, кодировщика мультимедиа и выходного актива;
- отправить задание.

В приведенном ниже фрагменте кода показано, как выполнить эти шаги.

	static public void CreateOverlayJob()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);


		// Upload assets to overlay
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4); // this is the input mezzanine
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);// this will be used as a video overlay
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv); // this will be used as an audio overlay
		
		// Load the preset configuration
		string presetFileName = "OverlayPreset.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME overlay job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for overlay, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of an overlay job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
	}



>[AZURE.NOTE]Этот фрагмент кода загружает каждый из активов по очереди для простоты. В производственных средах загрузка активов обычно является массовой. Дополнительные сведения о массовой отправке множества ресурс-контейнеров см. в разделе [Массовый прием ресурсов с помощью пакета SDK служб мультимедиа для .NET](media-services-dotnet-upload-files.md#ingest_in_bulk).

Полный пример кода см. в разделе [Создание наложений с помощью кодировщика служб мультимедиа](https://code.msdn.microsoft.com/Creating-Audio-and-Video-c2942c47).

###Ошибочные условия

При изменении строки предустановки вам нужно учесть такие факторы:

- Для наложений видео или изображений прямоугольник наложения должен полностью вмещаться в размеры исходного видео.
- Время начала и окончания наложений должно быть в пределах временной шкалы исходного видео.
- Если XML-код предустановки содержит ссылку на ?OverlayFileName=”%n%”, то коллекция InputAssets для задач (Tasks) должна содержать по крайней мере n+1 активов.



##Стыковка сегментов видео

Кодировщик служб мультимедиа поддерживает совмещение набора видеофайлов. Видео можно совмещать целиком или же можно указать части одного или обоих видео, которые будут совмещены. Результатом совмещения является единый выходной актив, содержащий указанное видео из входных активов. Совмещаемые видеоматериалы могут содержаться в нескольких активах или в одном активе. Совмещение регулируется XML-кодом предустановки, который передается кодировщику. Полное описание схемы предустановки см. в разделе [Схема кодировщика мультимедиа Azure](https://msdn.microsoft.com/library/azure/dn584702.aspx).

###Совмещение с помощью кодировщика служб мультимедиа

Совмещение регулируется в элементе <MediaFile>, как показано в приведенной ниже предустановке.
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Для каждого совмещаемого видео в элемент <Sources> добавляется элемент <Source>. В каждом элементе <Source> содержится элемент <Clips>. В каждом элементе <Clips> содержится один или несколько элементов <Clip>, которые указывают время начала и время окончания, определяя тем самым, какая часть видео будет вставлена в выходной ресурс-контейнер. Элемент <Source> ссылается на ресурс-контейнер, к которому он применяется. Формат ссылки зависит от того, содержатся ли совмещаемые видеоматериалы в отдельных активах или в едином активе. Если требуется совместить видео полностью, просто опустите элемент <Clips>.

###Совмещение видео из нескольких активов

Когда совмещаются видеофайлы из нескольких ресурс-контейнеров, в атрибуте MediaFile элемента <Source> используется отсчитываемый от нуля индекс, позволяющий определить, какому ресурс-контейнеру соответствует элемент <Source>. Нулевой индекс не указывается, и элемент <Source>, для которого не указан атрибут MediaFile, ссылается на первый входной ресурс-контейнер. Все остальные элементы <Source> должны указывать отсчитываемый от нуля индекс входного ресурс-контейнера, на который они ссылаются, с использованием синтаксиса %n%, где n — отсчитываемый от нуля индекс входного ресурс-контейнера. В предыдущем примере первый элемент <Source> задает первый входной ресурс-контейнер, второй элемент <Source> задает второй входной ресурс-контейнер и т. д. Обращаться к входным активам по порядку необязательно (см. пример ниже).
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip EndTime="00:00:05" 
	                StartTime="00:00:00" />
	        </Clips>
	                  
	        </Source>
	      <Source
	       AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Этот пример совмещает части второго, первого и третьего входных активов. Обратите внимание, что, так как обращение к каждому видео выполняется по отсчитываемому от нуля индексу, можно совместить два видеоматериала с одинаковыми именами. После создания файла предустановки вам нужно выполнить такие действия:
 
- отправить активы;
- загрузить конфигурацию предустановки;
- создать задание;
- получить ссылку на кодировщик служб мультимедиа;
- создать задачу с указанием входных активов, конфигурации предустановки, кодировщика мультимедиа и выходного актива;
- отправить задание.

В приведенном ниже фрагменте кода показано, как выполнить эти шаги.
	
	static public void StitchWithMultipleAssets()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);
		
		// Upload assets to stitch
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4);
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv);
		
		// Load the preset configuration
		string presetFileName = "StitchingWithMultipleAssets.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
		} 


Этот фрагмент кода загружает каждый из активов по очереди для простоты. В производственных средах загрузка активов обычно является массовой. Дополнительные сведения о массовой отправке множества ресурс-контейнеров см. в разделе [Массовый прием ресурсов с помощью пакета SDK служб мультимедиа для .NET](media-services-dotnet-upload-files.md#ingest_in_bulk). Полный пример кода см. в разделе [Совмещение с помощью кодировщика служб мультимедиа](https://code.msdn.microsoft.com/Stitching-with-Media-8fd5f203).

###Совмещение видеофайлов из одного актива

При совмещении видео в пределах одного актива у каждого видео должно быть уникальное имя. Видеофайлы указываются с помощью атрибута MediaFile, в качестве значения которого используется имя файла. Например:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="video1.mp4">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	       AudioStreamIndex="0"
	       MediaFile="video2.wmv">
	
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="video3.wmv">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Для создания выходного актива эта предустановка совмещает части файлов video1.mp4, video2.wmv и video3.wmv. Создание задания и задач аналогично совмещению видео из нескольких активов. Вам необходимо только отправить одиночный актив, как показано в приведенном ниже коде.

	// Creates a stitching job that uses a single asset 
    static public void StitchWithASingleAsset()
    {
        string presetFileName = "StitchingWithASingleAsset.xml";
        string configuration = File.ReadAllText(presetFileName);

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);

        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
        IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
        IAsset asset = CreateAssetAndUploadMultipleFiles(AssetCreationOptions.None, _stitchingFiles);

        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);

        job.Submit();
    }

##Кодирование презентаций или аудиопотоков с преобладанием речи
 
При кодировании видео, в звуковой дорожке которого преобладает речь, используемые по умолчанию предустановки кодировщика могут вызывать усиление фонового шума в кодируемом активе. Такое поведение возникает, когда для атрибута NormalizeAudio задано значение true.

###Кодирование презентаций, в которых преобладает речь

Чтобы предотвратить усиление фонового шума, выполните такие действия:

1. Скопируйте содержимое используемой предустановки кодировщика в XML-файл. Предустановки кодировщика можно найти в разделе "Схемы кодировщика служб мультимедиа Azure"
1. Удалите атрибут NormalizeAudio. Он находится в верхней части файла предустановки под элементом <MediaFile>:
	
	<MediaFile
	     DeinterlaceMode="AutoPixelAdaptive"
	     ResizeQuality="Super"
	     NormalizeAudio="True"
	     AudioGainLevel="1"
	     VideoResizeMode="Stretch">

1. Сохраните измененный файл предустановки на локальный жесткий диск. Для кодирования с настраиваемой предустановкой используйте код, подобный приведенному ниже.
		
		// Upload file and create asset
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, @"C:\TEMP\Original.mp4");
		 
		string inputPresetFile = @"C:\TEMP\H264 Broadband 720p NoAudioNorm.xml";
		string presetName = Path.GetFileNameWithoutExtension(inputPresetFile);
		 
		IJob job = _context.Jobs.Create("Encode Job for " + asset.Name + ", encoded using " +  presetName);
		
		Console.WriteLine("Encode Job for " + asset.Name + ", encoded using " + presetName);
		
		// Get a media processor reference, and pass to it the name of the processor to use for the specific task.
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		Console.WriteLine("Got MP " + processor.Name + ", ID : " + processor.Id + ", version: " + processor.Version);
		 
		// Read the configuration data into a string. 
		string configuration = File.ReadAllText(inputPresetFile);
		 
		// Create a task with the encoding details, using a string preset.
		ITask task = job.Tasks.AddNew("Encode Task for " + asset.Name + ", encoded using " + presetName, processor, configuration,
		                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
		 
		// Specify the input asset to be encoded.
		task.InputAssets.Add(asset);
		 
		// Add an output asset to contain the results of the job.
		task.OutputAssets.AddNew("Output asset for " + asset.Name + ", encoded using " + presetName, AssetCreationOptions.None);
		 
		// Launch the job. 
		job.Submit();

##Схемы обучения работе со службами мультимедиа

Схемы обучения AMS можно просмотреть здесь:

- [Рабочий процесс для потоковой передачи в реальном времени в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Рабочий процесс для потоковой передачи по запросу в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##См. также

[XML-схема кодировщика служб мультимедиа Azure](https://msdn.microsoft.com/library/azure/dn584702.aspx)

<!---HONumber=Oct15_HO3-->