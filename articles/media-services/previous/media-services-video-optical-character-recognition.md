---
title: Оцифровывание текста с помощью распознавания текста медиа-аналитики Azure | Документация Майкрософт
description: Функция OCR (оптическое распознавание символов) медиа-аналитики Azure позволяет преобразовывать текстовое содержимое в видеофайлах в редактируемый и доступный для поиска цифровой текст.  Это позволяет автоматически извлекать значимые метаданные из видеосигнала файлов мультимедиа.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 4a7a31b4e0069d2c94a4f109248d7b02c0b03faa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785903"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Преобразование текстового содержимого в видеофайлах в цифровой текст с помощью медиа-аналитики Azure
## <a name="overview"></a>Обзор
Если требуется извлечь текстовое содержимое из файлов видео и создать редактируемый и доступный для поиска цифровой текст, воспользуйтесь функцией OCR (оптическое распознавание символов) медиа-аналитики Azure. Этот обработчик мультимедиа Azure обнаруживает текстовое содержимое в видеофайлах и создает текстовые файлы, готовые к использованию. Функция OCR позволяет автоматически извлекать значимые метаданные из видеосигнала файлов мультимедиа.

При использовании в сочетании с поисковой системой можно с легкостью индексировать медиафайлы по тексту, тем самым повышая возможности обнаружения своего содержимого. Это очень полезно, если видео содержит много текста, например, если это видеозапись или снимки экрана какой-либо презентации в режиме слайд-шоу. Обработчик мультимедиа OCR Azure оптимизирован для цифрового текста.

В настоящее время доступна предварительная версия обработчика мультимедиа **Azure Media OCR** .

В этой статье приводятся сведения об **Azure Media OCR** и демонстрируется использование этого обработчика с пакетом SDK служб мультимедиа для .NET. Дополнительные сведения и примеры см. в [этом блоге](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Входные файлы OCR
Видеофайлы. Сейчас поддерживаются следующие форматы: MP4, MOV и WMV.

## <a name="task-configuration"></a>Конфигурация задачи
Конфигурация задачи (предустановка). При создании задачи с помощью **Azure Media OCR** необходимо указать предустановку конфигурации, используя JSON- или XML-файл. 

>[!NOTE]
>Подсистема распознавания текста принимает в качестве допустимых входных данных только область изображения от 40 до 32 000 пикселей в высоту и ширину.
>

### <a name="attribute-descriptions"></a>Описания атрибутов
| Имя атрибута | ОПИСАНИЕ |
| --- | --- |
|AdvancedOutput| Если для атрибута AdvancedOutput задано значение true, выходные данные JSON будут содержать данные о положении для каждого отдельного слова (помимо фраз и регионов). Если вы не хотите видеть эти сведения, установите значение false. По умолчанию для этого параметра используется значение false. Дополнительную информацию см. в [этом блоге](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Язык |(необязательно) Описывает язык искомого текста. Возможные значения: AutoDetect (по умолчанию), Arabic, ChineseSimplified, ChineseTraditional, Czech Danish, Dutch, English, Finnish, French, German, Greek, Hungarian, Italian, Japanese, Korean, Norwegian, Polish, Portuguese, Romanian, Russian, SerbianCyrillic, SerbianLatin, Slovak, Spanish, Swedish, Turkish. |
| TextOrientation |(необязательно) Описывает ориентацию искомого текста.  "Left" означает, что верхняя часть всех букв направлена влево.  По умолчанию текст (как, например, в книге) имеет ориентацию "Up", то есть буквы направлены вверх.  Возможные значения: AutoDetect (по умолчанию), Up, Right, Down, Left. |
| TimeInterval |(необязательно) Описывает частоту выборки.  Значение по умолчанию — каждые полсекунды.<br/>Формат JSON — ЧЧ:мм:сс.ССС (по умолчанию — 00:00:00.500)<br/>Формат XML: минимальная длительность W3C XSD (по умолчанию — PT0.5). |
| DetectRegions |(необязательно) Массив объектов DetectRegion, указывающих области внутри видеокадра, в котором выполняется распознавание текста.<br/>Объект DetectRegion состоит из следующих четырех значений, которые являются целыми числами.<br/>Left — отступ в пикселях от левого края.<br/>Top — отступ в пикселях от верхнего края.<br/>Width — ширина области в пикселях.<br/>Height: высота области в пикселях. |

#### <a name="json-preset-example"></a>Пример предустановки JSON

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>Пример предустановки XML

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>Выходные файлы OCR
Выходными данными обработчика мультимедиа OCR является JSON-файл.

### <a name="elements-of-the-output-json-file"></a>Элементы выходного JSON-файла
При обработке видео функция OCR выводит сегментированные по времени данные, состоящие из знаков, найденных в видео.  Чтобы сосредоточиться именно на тех словах, которые важно проанализировать, можно использовать атрибуты, такие как язык или ориентация. 

Выходные данные содержат следующие атрибуты:

| Элемент | ОПИСАНИЕ |
| --- | --- |
| Шкала времени |Количество тактов в секунду видео |
| Offset |Смещение времени для отметки времени. В API видео версии 1.0 это значение всегда равно 0. |
| Framerate |Количество кадров в секунду видео |
| width |Ширина видео в пикселях |
| height |Высота видео в пикселях |
| Fragments |Массив блоков видео, сегментированных на основе времени, в которые фрагментируются метаданные |
| start |Время начала фрагмента в тактах |
| длительность |Продолжительность фрагмента в тактах |
| interval |Интервал каждого события в пределах данного фрагмента |
| events |Массив, содержащий области |
| region |Объект, представляющий обнаруженные слова или фразы |
| Язык |Язык текста, обнаруженного в пределах области |
| orientation |Ориентация текста, обнаруженного в пределах области |
| lines |Массив строк текста, обнаруженного в пределах области |
| текст |Непосредственно текст |

### <a name="json-output-example"></a>Пример выходных данных JSON
В следующем примере выходных данных содержатся общие сведения о видео и несколько фрагментов видео. В каждом фрагменте видео содержится область, которая обнаружена обработчиком мультимедиа OCR; указывается язык и ориентация текста. Элемент region также содержит каждую строку в данной области, указывается текст строки, положение строки и сведения о каждом слове (содержимое слова, положение и достоверность) в этой строке. Ниже приводится пример с некоторыми сопутствующими комментариями.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>Пример кода .NET

В следующей программе показано, как выполнить следующие задачи.

1. Создать ресурс-контейнера и отправить в него файл мультимедиа.
2. Создание задания с помощью файла конфигурации или файла предустановки OCR.
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

namespace OCR
{
    class Program
    {
        // Read values from the App.config file.
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

