---
title: "Скрытие лиц с помощью аналитики мультимедиа Azure | Документация Майкрософт"
description: "В этой статье описана функция скрытия лиц с помощью медиа-аналитики Azure."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 5b6d8b8c-5f4d-4fef-b3d6-dc22c6b5a0f5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/01/2017
ms.author: juliako;
translationtype: Human Translation
ms.sourcegitcommit: 5b8d989d950c17d867f30a6fa8a464a8750e2303
ms.openlocfilehash: 98922addf7aa8bfc77033be29bf137b362378661


---
# <a name="redact-faces-with-azure-media-analytics"></a>Скрытие лиц с помощью аналитики мультимедиа Azure
## <a name="overview"></a>Обзор
**Редактор мультимедиа Azure** — это обработчик [медиа-аналитики Azure ](media-services-analytics-overview.md) с возможностью масштабируемого скрытия лиц в облаке. Функция скрытия лиц позволяет изменять видео, размывая изображения лиц выбранных пользователей. Вы можете использовать функцию скрытия лиц в ситуациях, требующих соблюдения общественной безопасности, а также при работе с новостями. Редактирование короткого материала с несколькими лицами вручную может занять несколько часов, тогда как при использовании функции скрытия лиц достаточно выполнить несколько простых действий. Дополнительные сведения см. в [этом блоге](https://azure.microsoft.com/blog/azure-media-redactor/).

В этом разделе описывается **редактор мультимедиа Azure** и способы его использования с пакетом SDK служб мультимедиа для .NET.

Сейчас **редактор мультимедиа Azure Media** доступен в предварительной версии. Он доступен во всех общедоступных регионах Azure, а также в центрах обработки данных для государственных организаций США и для Китая. В настоящее время эта предварительная версия предоставляется бесплатно. 

## <a name="face-redaction-modes"></a>Режимы скрытия лиц
Во время работы этой функции в каждом видеокадре лица определяются и отслеживаются на протяжении всей записи. Таким образом, лицо одного и того же человека можно размыть, даже если оно снято с другого ракурса. Автоматическое скрытие — это очень сложный процесс, который не всегда справляется с поставленной задачей. По этой причине служба медиа-аналитики предусматривает несколько способов изменения полученного результата.

Помимо полностью автоматического режима, также доступен двухэтапный рабочий процесс, в рамках которого вы можете выбирать и отменять выбор обнаруженных лиц с помощью списка идентификаторов. Кроме того, чтобы сделать изменения в каждом кадре произвольными, обработчик мультимедиа использует файл метаданных в формате JSON. Этот рабочий процесс разделен на два режима: **анализ** и **скрытие**. Эти два режима можно объединить, чтобы они выполнялись в рамках одного задания. Такой режим называется **объединенным**.

### <a name="combined-mode"></a>Объединенный режим
В результате вы получите MP4-файл с автоматическим скрытием, который не нужно править вручную.

| Этап | Имя файла | Примечания |
| --- | --- | --- |
| Входной ресурс-контейнер |foo.bar |Видео в формате WMV, MOV или MP4 |
| Входная конфигурация |Конфигурация задания (предустановка) |{'version':'1.0', 'options': {'mode':'combined'}} |
| Выходной ресурс-контейнер |foo_redacted.mp4 |Видео с размытием |

#### <a name="input-example"></a>Пример входных данных
[См. видео](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Пример выходных данных
[См. видео](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Режим анализа
В режиме **анализа** двухэтапный рабочий процесс принимает входное видео и создает JSON-файл с регистрацией расположения лиц, а также изображения всех обнаруженных лиц в формате JPG.

| Этап | Имя файла | Примечания |
| --- | --- | --- |
| Входной ресурс-контейнер |foo.bar |Видео в формате WMV, MPV или MP4 |
| Входная конфигурация |Конфигурация задания (предустановка) |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Выходной ресурс-контейнер |foo_annotations.json |Аннотация с данными о расположении лиц в формате JSON. Вы можете отредактировать эти данные, чтобы изменить границы размытия. См. пример ниже. |
| Выходной ресурс-контейнер |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Обрезанный JPG-файл с изображением каждого обнаруженного лица, где номер соответствует идентификатору метки лица. |

#### <a name="output-example"></a>Пример выходных данных
    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

… (Усечено.)

### <a name="redact-mode"></a>Режим скрытия
Во время второго этапа рабочий процесс принимает большее количество входных данных для объединения в один ресурс-контейнер.

Сюда входит список идентификаторов размываемых лиц, исходное видео и аннотации в формате JSON. Этот режим использует аннотации для применения размытия ко входному видео.

Выходные данные этапа анализа не включают исходное видео. Видео необходимо передать во входной ресурс-контейнер, чтобы задача режима скрытия обработала его, а затем выбрать в качестве первичного файла.

| Этап | Имя файла | Примечания |
| --- | --- | --- |
| Входной ресурс-контейнер |foo.bar |Видео в формате WMV, MPV или MP4. То же видео, что и на этапе 1. |
| Входной ресурс-контейнер |foo_annotations.json |Файл аннотации с метаданными, полученными на этапе&1;, с необязательными изменениями. |
| Входной ресурс-контейнер |foo_IDList.txt (необязательный) |(Необязательно.) Новый список идентификаторов скрываемых лиц со строками-разделителями. Если оставить его пустым, будут размыты все лица. |
| Входная конфигурация |Конфигурация задания (предустановка) |{'version':'1.0', 'options': {'mode':'redact'}} |
| Выходной ресурс-контейнер |foo_redacted.mp4 |Видео с размытием, примененным на основе аннотаций |

#### <a name="example-output"></a>Пример выходных данных
Выходные данные из списка идентификаторов с одним выбранным идентификатором.

[См. видео](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

## <a name="attribute-descriptions"></a>Описания атрибутов
Обработчик мультимедиа с функцией скрытия лиц обеспечивает высокую точность обнаружения и отслеживания лиц с возможностью определения до 64 человеческих лиц в одном видеокадре. Скрытие лиц в анфас выполняется с лучшим результатом по сравнению с лицами в профиль или небольшим лицами (не более 24x24 пикселей), которые сложнее обработать.

Функция регистрирует координаты обнаруженных и отслеживаемых лиц, обозначая их расположение, а также идентификационный номер каждого лица, позволяющий отслеживать этого человека. Если лицо в анфас теряется или перекрывается в кадре, его идентификационный номер может быть сброшен, в результате чего нескольким людям назначаются несколько идентификаторов.

Подробные описания атрибутов см. в статье [Обнаружение лиц и определение эмоций с помощью медиа-аналитики Azure](media-services-face-and-emotion-detection.md).

## <a name="sample-code"></a>Пример кода
В следующей программе показано, как выполнить следующие задачи.

1. Создание ресурса-контейнера и отправка в него файла мультимедиа.
2. Создание задания с задачей скрытия лиц на основе файла конфигурации, содержащего следующую предустановку JSON. 
   
        {'version':'1.0', 'options': {'mode':'combined'}}
3. Загрузка выходных JSON-файлов. 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace FaceRedaction
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
   
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
   
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
   
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
   
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

## <a name="next-step"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Связанные ссылки
[Общие сведения об аналитике служб мультимедиа Azure](media-services-analytics-overview.md)

[Демонстрационные материалы для медиааналитики Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)




<!--HONumber=Feb17_HO1-->


