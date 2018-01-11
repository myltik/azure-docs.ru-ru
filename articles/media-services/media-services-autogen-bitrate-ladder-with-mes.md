---
title: "Использование Azure Media Encoder Standard для автоматического создания таблицы скоростей и разрешений | Документация Майкрософт"
description: "В этом разделе показано, как использовать Media Encoder Standard (MES) для автоматического создания таблицы скоростей и разрешений на основе разрешения и скорости входных данных. Разрешение и скорость входных данных никогда не превышаются. Например, если указаны входные данные с разрешением 720p и скоростью 3 Мбит/с, то выходные данные будут иметь разрешение не выше 720p, а их скорости начнутся со значений ниже 3 Мбит/с."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2017
ms.author: juliako
ms.openlocfilehash: 4ffced8e11f05d214995f9fc8506dd7c6c7deaa5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Использование стандартной версии кодировщика мультимедиа Azure для автоматического создания схемы скоростей

## <a name="overview"></a>Обзор

В этой статье показано, как использовать Media Encoder стандартных (MES) для автоматического создания лестницу скоростью (разрешение bitrate пары), на основе входных разрешение и битрейт. Созданная автоматически предустановка никогда не превышает разрешение и скорость входных данных. Например если входное значение 720p скоростью 3 Мбит/с, выходные данные в лучшем остается 720p и начнется с частотой меньше 3 Мбит/с.

### <a name="encoding-for-streaming-only"></a>Кодирование только для потоковой передачи

Если планируется для кодирования источника видео только для потоковой передачи, следует использовать, «адаптивной потоковой передачи» стиль при создании задачи кодирования. При использовании предустановки **Adaptive Streaming** кодировщик MES интеллектуально ограничит таблицу скоростей и разрешений. Однако вы не сможете контролировать затраты на кодирование, так как служба определяет, сколько уровней использовать и с каким разрешением. Можно просмотреть примеры слоев выходные данные, созданные MES в результате кодирования с **адаптивной потоковой передачи** предустановленный набор в конце этой статьи. Выходные данные средства содержит MP4-файлов, тогда аудио и видео не чередуются.

### <a name="encoding-for-streaming-and-progressive-download"></a>Кодирование для потоковой передачи и прогрессивного скачивания

Если планируется для кодирования источника видео для потоковой передачи а также для создания для прогрессивного скачивания MP4-файлов, следует использовать «содержимого адаптивной несколько Bitrate MP4» стиль при создании задачи кодирования. При использовании **содержимого адаптивной несколько Bitrate MP4** заданы, MES кодировщик применяет ту же логику кодирования, как описано выше, но теперь выходного актива будет содержать MP4-файлов, где аудио и чередуются видео. Такой MP4-файл (например, версию с самым высоким битрейтом) можно использовать для прогрессивного скачивания.

## <a id="encoding_with_dotnet"></a>Кодирование с помощью пакета SDK служб мультимедиа для .NET

В следующем примере кода пакет SDK служб мультимедиа используется для выполнения следующих задач.

- Создание задания кодирования.
- Получение ссылки на стандартный кодировщик мультимедиа.
- Добавьте задачу кодирования в задание и укажите предустановку **Adaptive Streaming**. 
- Создание выходного ресурса-контейнера с закодированным ресурсом.
- Добавление обработчика событий для проверки хода выполнения задания.
- Отправка задания.

#### <a name="create-and-configure-a-visual-studio-project"></a>Создание и настройка проекта Visual Studio

Настройте среду разработки и укажите в файле app.config сведения о подключении, как описано в статье [Разработка служб мультимедиа с помощью .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Пример

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
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
    }
}
```

## <a id="output"></a>Выходные данные

Этот раздел содержит три примера выходных данных слоев, созданных MES в результате кодирования с предустановкой **Adaptive Streaming**. 

### <a name="example-1"></a>Пример 1
При исходной высоте 1080 и частоте кадров 29,970 создается 6 слоев видео.

|Слой|Высота:|Ширина|Скорость (кбит/с)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4.|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Пример 2
При исходной высоте 720 и частоте кадров 23,970 создается 5 слоев видео.

|Слой|Высота:|Ширина|Скорость (кбит/с)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4.|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Пример 3
При исходной высоте 360 и частоте кадров 29,970 создается 3 слоя видео.

|Слой|Высота:|Ширина|Скорость (кбит/с)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>См. также
[Обзор кодирования с помощью служб мультимедиа](media-services-encode-asset.md)

