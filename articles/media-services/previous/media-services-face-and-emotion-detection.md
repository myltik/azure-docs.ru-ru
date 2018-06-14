---
title: Обнаружение лиц и определение эмоций с помощью медиа-аналитики Azure | Документация Майкрософт
description: В этом разделе содержатся сведения об обнаружении лиц и определении эмоций с помощью медиа-аналитики Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 859e75819f96edd527fceb143faf8357738ce80e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33784463"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Обнаружение лиц и определение эмоций с помощью медиа-аналитики Azure
## <a name="overview"></a>Обзор
Обработчик мультимедиа (MP) **Azure Media Face Detector (детектор лиц мультимедиа Azure)** позволяет подсчитывать и отслеживать движения и даже определять заинтересованность и реакции людей с помощью выражений лиц. В этой службе реализованы две функции: 

* **Обнаружение лиц**
  
    Функция обнаружения лиц используется для обнаружения и отслеживания человеческих лиц на видео. Поддерживается одновременное обнаружение множества лиц и их отслеживание по мере перемещения, в результате чего в JSON-файле будут возвращены метаданные времени и расположения. Во время отслеживания и перемещения человека на экране предпринимается попытка получения согласованного идентификатора для одного и того же лица, даже если это лицо загораживает какой-либо объект или человек ненадолго выходит из кадра.
  
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

## <a id="output_elements"></a>Элементы выходного JSON-файла

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Детектор лиц использует методы фрагментации (с разделением метаданных на временные фрагменты и возможностью загрузки только необходимых частей) и сегментации (с разделением событий в случае, если они становятся слишком продолжительными). Выполнив простые вычисления, вы сможете преобразовать данные. Например, если событие началось в 6 300 (тактов) с шкалой времени 2 997 (тактов в секунду) и частотой кадров 29,97 (кадров в секунду), то:

* начало/шкала времени = 2,1 секунды
* Время (с) x частота кадров = 63 кадра

## <a name="face-detection-input-and-output-example"></a>Пример входных и выходных данных обнаружения лиц
### <a name="input-video"></a>Входные видеоданные
[Входные видеоданные](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Конфигурация задачи (предустановка)
При создании задачи с помощью **Azure Media Face Detector**необходимо указать предустановку конфигурации. Следующая предустановка конфигурации предназначена только для обнаружения лиц.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Описания атрибутов
| Имя атрибута | ОПИСАНИЕ |
| --- | --- |
| Mode |Fast: быстрая скорость обработки, но с меньшей точностью (по умолчанию).|

### <a name="json-output"></a>Выходные данные JSON
Следующий пример выходных данных JSON был сокращен.

```json
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
```


## <a name="emotion-detection-input-and-output-example"></a>Пример входных и выходных данных определения эмоций
### <a name="input-video"></a>Входные видеоданные
[Входные видеоданные](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Конфигурация задачи (предустановка)
При создании задачи с помощью **Azure Media Face Detector**необходимо указать предустановку конфигурации. Следующая предустановка конфигурации используется для создания JSON на основе определения эмоций.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Описания атрибутов
| Имя атрибута | ОПИСАНИЕ |
| --- | --- |
| Mode |Faces: только обнаружение лиц.<br/>PerFaceEmotion: эмоции возвращаются отдельно для каждого обнаружения лиц.<br/>AggregateEmotion. Возвращаются средние значения эмоций для всех лиц в кадре. |
| AggregateEmotionWindowMs |Используется, если выбран режим AggregateEmotion. Указывает длину видео для получения каждого совокупного результата в миллисекундах. |
| AggregateEmotionIntervalMs |Используется, если выбран режим AggregateEmotion. Указывает частоту для получения совокупных результатов. |

#### <a name="aggregate-defaults"></a>Совокупные значения по умолчанию
Ниже приведены рекомендуемые значения для совокупных параметров окна и интервала. Значение AggregateEmotionWindowMs должно быть больше значения AggregateEmotionIntervalMs.

|| Значения по умолчанию | Максимальные | Минимальные |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0,25|
| AggregateEmotionIntervalMs |0,5 |1 |0,25|

### <a name="json-output"></a>Выходные данные JSON
Выходные данные JSON для совокупных эмоций (сокращенные).

```json
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
```

## <a name="limitations"></a>Ограничения
* Поддерживаемые входные видеоформаты: MP4, MOV и WMV.
* Диапазон размеров обнаруживаемых лиц — от 24 x 24 до 2048 x 2048 пикселей. Лица вне этого диапазона не обнаруживаются.
* Максимальное количество возвращаемых лиц для каждого видео — 64.
* Некоторые лица могут не обнаруживаться из-за технических проблем, таких как слишком большой поворот головы и существенное перекрытие. Лучшие результаты определяются для лиц в анфас или практически в анфас.

## <a name="net-sample-code"></a>Пример кода .NET

В следующей программе показано, как выполнить следующие задачи.

1. Создать ресурс-контейнера и отправить в него файл мультимедиа.
2. Создать задание с задачей обнаружения лиц на основе файла конфигурации, содержащего следующую предустановку JSON: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Загрузка выходных JSON-файлов. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Создание и настройка проекта Visual Studio

Настройте среду разработки и укажите в файле app.config сведения о подключении, как описано в статье [Разработка служб мультимедиа с помощью .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Пример

```csharp
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
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

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
```

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Связанные ссылки
[Общие сведения об аналитике служб мультимедиа Azure](media-services-analytics-overview.md)

[Демонстрационные материалы для медиааналитики Azure](http://amslabs.azurewebsites.net/demos/Analytics.html)

