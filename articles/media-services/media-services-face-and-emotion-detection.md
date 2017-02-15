---
title: "Обнаружение лиц и определение эмоций с помощью медиа-аналитики Azure | Документация Майкрософт"
description: "В этом разделе содержатся сведения об обнаружении лиц и определении эмоций с помощью медиа-аналитики Azure."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/15/2016
ms.author: milanga;juliako;
translationtype: Human Translation
ms.sourcegitcommit: 48a4cdf7d50e765ee42cb44d12d1dafd49c13795
ms.openlocfilehash: 3147eba8bd31d3d05bd990571a986316d6f5093f


---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Обнаружение лиц и определение эмоций с помощью медиа-аналитики Azure
## <a name="overview"></a>Обзор
Обработчик мультимедиа (MP) **Azure Media Face Detector (детектор лиц мультимедиа Azure)** позволяет подсчитывать и отслеживать движения и даже определять заинтересованность и реакции людей с помощью выражений лиц. В этой службе реализованы две функции: 

* **Обнаружение лиц**
  
    Функция обнаружения лиц используется для обнаружения и отслеживания человеческих лиц на видео. Поддерживается одновременное обнаружение множества лиц и их отслеживание по мере перемещения, в результате чего в JSON-файле будут возвращены метаданные времени и расположения. Во время отслеживания и перемещения человека на экране предпринимается попытка получения согласованного ИД для одного и того же лица, даже если это лицо загораживает какой-либо объект или человек ненадолго выходит из кадра.
  
  > [!NOTE]
  > Эта служба не поддерживает распознавание лиц. Человек, который выходит из кадра или остается закрытым каким-либо объектом в течение слишком долгого времени, после возвращения в кадр получит новый идентификатор.
  > 
  > 
* **Определение эмоций**
  
    Определение эмоций представляет собой дополнительный компонент обработчика мультимедиа для обнаружения лиц. Он анализирует несколько выражений эмоций на обнаруженных лицах и определяет реакцию (или чувство) человека, например счастье, печаль, страх, гнев и многое другое. 

Сейчас обработчик мультимедиа **Azure Media Face Detector** доступен в предварительной версии.

В этой статье приводятся сведения об обработчике **Azure Media Face Detector** и демонстрируется его использование с пакетом SDK служб мультимедиа для .NET.

## <a name="face-detector-input-files"></a>Входные файлы детектора лиц
Видеофайлы. Сейчас поддерживаются следующие форматы: MP4, MOV и WMV.

## <a name="face-detector-output-files"></a>Выходные файлы детектора лиц
API обнаружения и отслеживания лиц обеспечивает высокую точность обнаружения и отслеживания лиц с возможностью определения до 64 человеческих лиц на видео. Лица в анфас позволяют получить лучшие результаты, тогда как значения по лицам в профиль или небольшим лицам (не более 24 x 24 пикселей) могут быть неточными.

Обнаруженные и отслеживаемые лица возвращаются с указанием координат (слева, вверху, ширина и высота), которые обозначают расположение лиц на изображении в пикселях, а также с идентификационным номером лица, означающим отслеживание этого человека. Если лицо в анфас теряется или перекрывается в кадре, его идентификационный номер может быть сброшен, в результате чего нескольким людям назначаются несколько идентификаторов.

### <a name="a-idoutputelementsaelements-of-the-output-json-file"></a><a id="output_elements"></a>Элементы выходного JSON-файла
Выходной результат операции обнаружения и отслеживания лиц содержит метаданные по лицам в заданном JSON-файле.

В JSON-файл обнаружения и отслеживания лиц входят следующие атрибуты:

| Элемент | Описание |
| --- | --- |
| Version (версия) |Версия API видео. |
| Шкала времени |Количество тактов в секунду видео. |
| Offset |Смещение времени для отметки времени. В API видео версии 1.0 это значение всегда равно 0. В будущих поддерживаемых сценариях это значение может измениться. |
| Framerate |Количество кадров в секунду видео. |
| Fragments |Метаданные разделены на разные сегменты, называемые фрагментами. Каждый фрагмент имеет начало, длительность, номер интервала и события. |
| Начало |Время начала первого события в тактах. |
| Длительность |Продолжительность фрагмента в тактах. |
| Интервал |Интервал каждой записи события внутри фрагмента в тактах. |
| События |Каждое событие содержит лица, обнаруженные и отслеживаемые в течение этого промежутка времени. Оно представляет собой массив массива событий. Внешний массив представляет один интервал времени. Внутренний массив состоит из нуля или более событий, которые выполнялись в этот момент времени. Пустые скобки означают, что лица не обнаружены. |
| ИД |Идентификатор отслеживаемого лица. Если лицо становится необнаруживаемым, это значение может быть случайным образом изменено. Человек должен иметь один и тот же идентификатор на протяжении всего видео, однако это условие не может быть гарантированным из-за ограничений алгоритма обнаружения (перекрытие и т. д.). |
| X, Y |Верхние левые координаты X и Y прямоугольника, ограничивающего лицо, в нормализованном масштабе от 0,0 до 1,0. <br/> Координаты X и Y всегда указываются в альбомной ориентации, поэтому при наличии портретного видео (или перевернутого — в iOS) потребуется соответствующим образом поменять координаты. |
| Width, Height |Ширина и высота прямоугольника, ограничивающего лицо, в нормализованном масштабе от 0,0 до 1,0. |
| facesDetected |Этот элемент находится в конце результатов JSON и означает количество лиц, обнаруженных алгоритмом на видео. Поскольку идентификаторы могут быть случайно сброшены в случае невозможности обнаружения лица (например, человек пропадает с экрана, отворачивается), это число не всегда соответствует реальному количеству лиц на видео. |

Детектор лиц использует методы фрагментации (с разделением метаданных на временные фрагменты и возможностью загрузки только необходимых частей) и сегментации (с разделением событий в случае, если они становятся слишком продолжительными). Выполнив простые вычисления, вы сможете преобразовать данные. Например, если событие началось в 6 300 (тактов) с шкалой времени 2 997 (тактов в секунду) и частотой кадров 29,97 (кадров в секунду), то:

* начало/шкала времени = 2,1 секунды
* секунды x (частота кадров/шкала времени) = 63 кадра

## <a name="face-detection-input-and-output-example"></a>Пример входных и выходных данных обнаружения лиц
### <a name="input-video"></a>Входные видеоданные
[Входные видеоданные](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Конфигурация задачи (предустановка)
При создании задачи с помощью **Azure Media Face Detector**необходимо указать предустановку конфигурации. Следующая предустановка конфигурации предназначена только для обнаружения лиц.

    {"version":"1.0"}

### <a name="json-output"></a>Выходные данные JSON
Следующий пример выходных данных JSON был сокращен.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

## <a name="emotion-detection-input-and-output-example"></a>Пример входных и выходных данных определения эмоций
### <a name="input-video"></a>Входные видеоданные
[Входные видеоданные](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Конфигурация задачи (предустановка)
При создании задачи с помощью **Azure Media Face Detector**необходимо указать предустановку конфигурации. Следующая предустановка конфигурации используется для создания JSON на основе определения эмоций.

    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


#### <a name="attribute-descriptions"></a>Описания атрибутов
| Имя атрибута | Описание |
| --- | --- |
| Режим |Faces. Только обнаружение лиц <br/>AggregateEmotion. Возвращаются средние значения эмоций для всех лиц в кадре. |
| AggregateEmotionWindowMs |Используется, если выбран режим AggregateEmotion. Указывает длину видео для получения каждого совокупного результата в миллисекундах. |
| AggregateEmotionIntervalMs |Используется, если выбран режим AggregateEmotion. Указывает частоту для получения совокупных результатов. |

#### <a name="aggregate-defaults"></a>Совокупные значения по умолчанию
Ниже приведены рекомендуемые значения для совокупных параметров окна и интервала. Значение AggregateEmotionWindowMs должно быть больше значения AggregateEmotionIntervalMs.

| Значения по умолчанию | Максимальные | Минимальные |
| --- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |
| AggregateEmotionIntervalMs |0,5 |1 |

### <a name="json-output"></a>Выходные данные JSON
Выходные данные JSON для совокупных эмоций (сокращенные).

    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


## <a name="limitations"></a>Ограничения
* Поддерживаемые входные видеоформаты: MP4, MOV и WMV.
* Диапазон размеров обнаруживаемых лиц — от 24 x 24 до 2048 x 2048 пикселей. Лица вне этого диапазона не обнаруживаются.
* Максимальное количество возвращаемых лиц для каждого видео — 64.
* Некоторые лица могут не обнаруживаться из-за технических проблем, например слишком большой лицевой угол (поворот головы) и существенное перекрытие. Лучшие результаты определяются для лиц в анфас или практически в анфас.

## <a name="sample-code"></a>Пример кода
В следующей программе показано, как выполнить следующие задачи.

1. Создание ресурса-контейнера и отправка в него файла мультимедиа.
2. Создание задания с задачей обнаружения лиц на основе файла конфигурации, содержащего следующую предустановку JSON. 
   
        {
            "version": "1.0"
        }
3. Загрузка выходных JSON-файлов. 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace FaceDetection
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
   
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
   
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
   
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
   
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

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Связанные ссылки
[Общие сведения об аналитике служб мультимедиа Azure](media-services-analytics-overview.md)

[Демонстрационные материалы для медиааналитики Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)




<!--HONumber=Nov16_HO3-->


