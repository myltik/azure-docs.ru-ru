<properties 
	pageTitle="Кодирование ресурса-контейнера с помощью Media Encoder Standard и .NET | Microsoft Azure" 
	description="В этом разделе показано, как использовать .NET для кодирования ресурса-контейнера с помощью Media Encoder Strandard." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="09/19/2016"
	ms.author="juliako;anilmur"/>


# Кодирование ресурса-контейнера с помощью Media Encoder Standard и .NET

Задания кодирования — одни из самых распространенных операций обработки в службах мультимедиа. Они создаются для преобразования файлов мультимедиа из одного формата кодирования в другой. При кодировании можно использовать встроенный кодировщик служб мультимедиа. Также можно использовать кодировщик, предоставленный партнером служб мультимедиа, кодировщики сторонних производителей доступны в Магазине Azure.

В этом разделе показано, как использовать .NET для кодирования ресурсов-контейнеров с помощью Media Encoder Standard (MES). Стандартный кодировщик мультимедиа настраивается с помощью одной из предустановок кодировщика, описанных [здесь](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Мы советуем всегда кодировать мезонинные файлы в набор MP4-файлов с адаптивной скоростью, а затем преобразовывать его в нужный формат с помощью [динамической упаковки](media-services-dynamic-packaging-overview.md). Для использования динамической упаковки вам потребуется получить по крайней мере одну единицу потокового воспроизведения по запросу для конечной точки потоковой передачи, из которой планируется передавать содержимое. Дополнительные сведения см. в статье [Масштабирование служб мультимедиа](media-services-portal-manage-streaming-endpoints.md).

Если выходящий ресурс зашифрован в хранилище, необходимо настроить политику доставки ресурсов. Дополнительную информацию см. в разделе [Настройка политики доставки ресурсов](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]Кодировщик MES создает выходной файл с именем, содержащим первые 32 символа имени входного файла. Имя основывается на параметрах, указанных в файле предустановки. Например, "FileName": "{Basename}\_{Index}{Extension}". {Basename} заменяется первыми 32 символами имени входного файла.

###Форматы стандартного кодировщика мультимедиа

[Форматы и кодеки](media-services-media-encoder-standard-formats.md)

###Предустановки стандартного кодировщика мультимедиа

Стандартный кодировщик мультимедиа настроен с помощью одной из предустановок кодировщика, описанных [здесь](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Входные и выходные метаданные

Кодируя входной актив (или активы) с использованием MES, вы получаете выходной актив после успешного выполнения задачи кодирования. Выходной актив содержит видео- и аудиофайлы, эскизы, манифест и т. д., в зависимости от используемой предустановки кодирования.

Выходной актив также содержит файл с метаданными входного актива. Имя XML-файла метаданных имеет следующий формат: <ИД\_ресурса-контейнера>\_metadata.xml (например, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4\_metadata.xml), где <ИД\_ресурса-контейнера> — это значение AssetId для входного ресурса-контейнера. Схема входного XML-файла метаданных описана [здесь](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Выходной актив также содержит файл с метаданными выходного актива. Имя XML-файла метаданных имеет следующий формат: <source_file_name>\_manifest.xml (например, BigBuckBunny\_manifest.xml). Схема выходного XML-файла метаданных описана [здесь](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Если вы хотите изучить один из двух файлов метаданных, создайте указатель SAS и загрузите файл на локальный компьютер. Пример создания указателя SAS и загрузки файла см. в статье "Использование расширения пакета SDK служб мультимедиа для .NET".

##Скачивание образца

Скачать и запустить образец можно [здесь](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##Пример

В следующем примере кода пакет SDK служб мультимедиа используется для выполнения следующих задач.

- Создание задания кодирования.
- Получение ссылки на стандартный кодировщик мультимедиа.
- Укажите использование предустановки «H264 Multiple Bitrate 720p». Все предустановки можно просмотреть [здесь](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). Кроме того, [в этом](https://msdn.microsoft.com/library/mt269962.aspx) разделе вы можете проверить схему, которой должны соответствовать эти предустановки.
- Добавление одной задачи кодирования в задание.
- Указание входного ресурса-контейнера для кодирования.
- Создание выходного ресурса-контейнера, который будет содержать закодированный ресурс-контейнер.
- Добавление обработчика событий для проверки хода выполнения задания.
- Отправка задания.
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		

		    // Create a task with the encoding details, using a string preset.
		    // In this case "H264 Multiple Bitrate 720p" preset is used.
		    ITask task = job.Tasks.AddNew("My encoding task",
		        processor,
		        "H264 Multiple Bitrate 720p",
		        TaskOptions.None);
		
		    // Specify the input asset to be encoded.
		    task.InputAssets.Add(asset);
		    // Add an output asset to contain the results of the job. 
		    // This output is specified as AssetCreationOptions.None, which 
		    // means the output asset is not encrypted. 
		    task.OutputAssets.AddNew("Output asset",
		        AssetCreationOptions.None);
		
		    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		    job.Submit();
		    job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		    return job.OutputMediaAssets[0];
		}
		
		private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
		{
		    Console.WriteLine("Job state changed event:");
		    Console.WriteLine("  Previous state: " + e.PreviousState);
		    Console.WriteLine("  Current state: " + e.CurrentState);
		    switch (e.CurrentState)
		    {
		        case JobState.Finished:
		            Console.WriteLine();
		            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
		            break;
		        case JobState.Canceling:
		        case JobState.Queued:
		        case JobState.Scheduled:
		        case JobState.Processing:
		            Console.WriteLine("Please wait...\n");
		            break;
		        case JobState.Canceled:
		        case JobState.Error:
		
		            // Cast sender as a job.
		            IJob job = (IJob)sender;
		
		            // Display or log error details as needed.
		            break;
		        default:
		            break;
		    }
		}
		
		
		private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		{
		    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		    if (processor == null)
		        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		    return processor;
		}


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##См. также 

[Создание эскиза с помощью стандартного кодировщика мультимедиа и платформы .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Обзор кодирования с помощью служб мультимедиа](media-services-encode-asset.md)

<!---HONumber=AcomDC_0921_2016-->