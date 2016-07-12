<properties
	pageTitle="Обнаружение движения с помощью медиа-аналитики Azure"
	description="Обработчик мультимедиа, детектор движения мультимедиа Azure, позволяет эффективно определять представляющие интерес области в обычно продолжительном и бессобытийном видеоматериале."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/22/2016"  
	ms.author="milanga;juliako;"/>
 
# Обнаружение движения с помощью медиа-аналитики Azure

##Обзор

Обработчик мультимедиа (MP) **Azure Media Motion Detector (детектор движения мультимедиа Azure)** позволяет эффективно определять представляющие интерес фрагменты в обычно продолжительном и бессобытийном видеоматериале. Функцию обнаружения движения можно использовать при съемке стационарной камерой для определения тех областей видео, где происходит движение. Она создает JSON-файл, содержащий метаданные с метками времени и ограничивающую область, где возникло событие.

Эта технология предназначена для видеоканалов в системе обеспечения безопасности. Она способна классифицировать движения на нужные события и ложноположительные срабатывания, происходящие, например, при изменении освещения и появлении теней. Это позволяет создавать оповещения системы безопасности при съемке и не обращать внимание на многочисленные неактуальные события, а также извлекать представляющие интерес фрагменты из продолжительных материалов видеонаблюдения.

Сейчас обработчик мультимедиа **Azure Media Motion Detector** доступен в предварительной версии.

В этом разделе приводятся сведения об **Azure Media Motion Detector** и демонстрируется использование этого обработчика с пакетом SDK служб мультимедиа для .NET.

##Входные файлы детектора движения

Видеофайлы. Сейчас поддерживаются следующие форматы: MP4, MOV и WMV.

##Выходные файлы детектора движения

Задание обнаружения движения возвращает в выходной ресурс-контейнер JSON-файл, описывающей оповещения о движении и их категории на видео. Файл будет содержать сведения о времени и длительности движения, обнаруженного на видео.

Сейчас функция обнаружения движения поддерживает только категорию универсального движения, которая обозначается как ***type 2*** в выходных данных.

Координаты X, Y и размеры указываются в виде нормализованных чисел с плавающей запятой от 0,0 до 1,0. Умножив эти показатели на значения разрешения высоты и ширины видео, мы получим прямоугольник, ограничивающий область обнаруженного движения.

Каждые выходные данные разбиваются на фрагменты и делятся на интервалы для определения данных на видео. Длина каждого фрагмента не обязательно должна быть одинаковой. Кроме того, если движение обнаружено не было, фрагмент может быть довольно продолжительным.

Обнаруженные движущиеся объекты на видео с неподвижным фоном (например, в режиме видеонаблюдения) API детектора движения отмечает визуальными индикаторами. Детектор движения способен исключать ложные срабатывания, возникающие, например, при изменении освещения и затенении. В число текущих ограничений для действия алгоритмов входит съемка в режиме ночного видения, полупрозрачные и небольшие объекты.

###<a id="output_elements"></a>Элементы выходного JSON-файла

В следующей таблице описаны элементы выходных данных JSON-файла.

Элемент|Описание
---|---
Version (версия)|Версия API видео.
Шкала времени|Количество тактов в секунду видео.
Offset|Смещение времени для отметки времени в тактах. В API видео версии 1.0 это значение всегда равно 0. В будущих поддерживаемых сценариях это значение может измениться.
Framerate|Количество кадров в секунду видео.
Width, Height|Ширина и высота изображения в пикселях.
Начало|Метка времени начала в тактах.
Длительность|Продолжительность события в тактах.
Интервал|Интервал каждой записи события в тактах.
События|Каждый фрагмент события содержит движение, обнаруженное в течение этого промежутка времени.
Тип|В текущей версии — всегда значение 2 для универсального движения. Эта метка позволяет API видео гибко классифицировать движение в будущих версиях.
RegionID|Как упоминалось выше, в этой версии всегда будет использоваться значение 0. Эта метка позволяет API видео эффективно обнаруживать движение в различных областях в будущих версиях.
регионы|Область в видео, где необходимо отслеживать движение. В текущей версии API видео нельзя указать область. Здесь областью обнаруживаемого движения будет весь видеоматериал полностью<br/> ID представляет область. В этой версии существует только один идентификатор — ID 0. <br/> Прямоугольник представляет фигуру, ограничивающую область, в которой необходимо отслеживать движение. В этой версии всегда используется прямоугольник. <br/> Размеры области выражаются значениями X, Y, ширины и высоты. Координаты X и Y представляют верхние левые координаты X и Y области по нормализованной шкале от 0,0 до 1,0. Ширина и высота представляют размер области по нормализованной шкале от 0,0 до 1,0. В текущей версии значения X, Y, ширины и высоты всегда фиксированы в позициях 0,0 и 1,1.<br/> Fragments. Метаданные разделены на разные сегменты, называемые фрагментами. Каждый фрагмент имеет начало, длительность, номер интервала и события. Фрагмент без событий означает, что с момента начала и в течение продолжительности события движение обнаружено не было.
Квадратные скобки|Каждая скобка представляет один интервал в событии. Пустые скобки для этого интервала означают, что движение не обнаружено.
 

##Конфигурация задачи (предустановка)

При создании задачи с помощью **Azure Media Motion Detector** необходимо указать предустановку конфигурации. Сейчас нельзя задать параметры в предустановке конфигурации Azure Media Motion Detector. Далее приведена необходимая минимальная предустановка конфигурации.

	{"version":"1.0"}

##Примеры видео и выходные данные детектора движения

###Пример фактического движения

[Пример фактического движения](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fd54876c6-89a5-41de-b1f4-f45b6e10a94f%2FGarage.ism%2Fmanifest)

###Выходные данные JSON

	 {
	 "version": "1.0",
	 "timescale": 60000,
	 "offset": 0,
	 "framerate": 30,
	 "width": 1920,
	 "height": 1080,
	 "regions": [
	   {
	     "id": 0,
	     "type": "rectangle",
	     "x": 0,
	     "y": 0,
	     "width": 1,
	     "height": 1
	   }
	 ],
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 68510
	   },
	   {
	     "start": 68510,
	     "duration": 969999,
	     "interval": 969999,
	     "events": [
	       [
	         {
	           "type": 2,
	           "regionId": 0
	         }
	       ]
	     ]
	   },
	   {
	     "start": 1038509,
	     "duration": 41489
	   }
	 ]
	}

###Пример ложноположительных результатов

[Пример ложноположительных результатов (изменение освещения).](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Ffdc6656b-1c10-4f3f-aa7c-07ba073c1615%2FLivingRoomLight.ism%2Fmanifest&tech=flash)

###Выходные данные JSON

	{
	    "version": "1.0",
	    "timescale": 30000,
	    "offset": 0,
	    "framerate": 29.97,
	    "width": 1920,
	    "height": 1080,
	    "regions": [
	    {
	        "id": 0,
	        "type": "rectangle",
	        "x": 0,
	        "y": 0,
	        "width": 1,
	        "height": 1
	    }
	    ],
	    "fragments": [
	    {
	        "start": 0,
	        "duration": 320320
	    }
	    ]
	}


##Ограничения

- Поддерживаемые входные видеоформаты: MP4, MOV и WMV.
- Функция обнаружения движения оптимизирована для видео с неподвижными фонами. Алгоритм разработан для сокращения числа ложных срабатываний, возникающих, например, при изменении освещения и появления теней.
- Из-за ряда технических проблем некоторые виды движений могут не обнаруживаться. Примерами является видео в режиме ночной съемки, полупрозрачные и небольшие объекты.


## Пример кода

В следующей программе показано, как выполнить следующие задачи.

1. Создание ресурса-контейнера и отправка в него файла мультимедиа.
1. Создание задания с задачей обнаружения движения на видео на основе файла конфигурации, содержащего следующую предустановку JSON.
					
		{
		    "version": "1.0"
		}

1. Загрузка выходных JSON-файлов.
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace VideoMotionDetection
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        static void Main(string[] args)
		        {
		
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            // Run the VideoMotionDetection job.
		            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\VideoMotionDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
		        }
		
		        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Video Motion Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Video Motion Detection Job");
		
		            // Get a reference to Azure Media Motion Detector.
		            string MediaProcessorName = "Azure Media Motion Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
		
		            // Use the following event handler to check job progress.  
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
		
		            // Launch the job.
		            job.Submit();
		
		            // Check job execution and wait for job to finish.
		            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
		
		            progressJobTask.Wait();
		
		            // If job state is Error, the event handling
		            // method for job progress should log errors.  Here we check
		            // for error state and exit if needed.
		            if (job.State == JobState.Error)
		            {
		                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
		                Console.WriteLine(string.Format("Error: {0}. {1}",
		                                                error.Code,
		                                                error.Message));
		                return null;
		            }
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
		        {
		            IAsset asset = _context.Assets.Create(assetName, options);
		
		            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
		            assetFile.Upload(filePath);
		
		            return asset;
		        }
		
		        static void DownloadAsset(IAsset asset, string outputDirectory)
		        {
		            foreach (IAssetFile file in asset.AssetFiles)
		            {
		                file.Download(Path.Combine(outputDirectory, file.Name));
		            }
		        }
		
		        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors
		                .Where(p => p.Name == mediaProcessorName)
		                .ToList()
		                .OrderBy(p => new Version(p.Version))
		                .LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor",
		                                                           mediaProcessorName));
		
		            return processor;
		        }
		
		        static private void StateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished.");
		                    Console.WriteLine();
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
		                    // LogJobStop(job.Id);
		                    break;
		                default:
		                    break;
		            }
		        }
		
		    }
        }


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Связанные ссылки

[Общие сведения об аналитике служб мультимедиа Azure](media-services-analytics-overview.md)

[Демонстрационные материалы для медиааналитики Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0629_2016-->