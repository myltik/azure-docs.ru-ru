<properties urlDisplayName="How to Encode an Asset" pageTitle="Кодировка актива для служб мультимедиа &mdash; Azure" metaKeywords="" description="Узнайте, как использовать кодировщик мультимедиа Azure для кодировки мультимедийного контента в службах мультимедиа. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." metaCanonical="" services="media-services" documentationCenter="" title="Практическое руководство: кодировка активов" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Практическое руководство: кодировка активов

Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущий раздел [Практическое руководство: Получение процессора мультимедиа][Практическое руководство: Получение процессора мультимедиа].

Мультимедийный контент на сервере можно закодировать в разных форматах и кодировках, используя кодировщик мультимедиа Azure. Также можно использовать кодировщик, предоставленный партнером служб мультимедиа, кодировщики сторонних производителей доступны в [Магазине Azure][Магазине Azure]. Сведения о задачах кодировки можно указать с помощью строк [предустановок кодировщика][предустановок кодировщика] или файлов конфигурации.

## Кодировка в формате MP4

Следующий метод отправляет один актив и создает задание для его кодирования в формате MP4 с помощью предустановки "H264 Broadband 720p", которая создает один MP4-файл с кодировкой H264 и разрешением 720p:

     static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder) { //Create an encrypted asset and upload to storage. IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);
        // Declare a new job.

        IJob job = _context.Jobs.Create("My encoding job");

        // Get a reference to the Azure Media Encoder
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
            "H264 Broadband 720p",
            _protectedConfig);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is in the clear (unencrypted). 
        task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-JobId.txt file 
        // in your output folder.
        LogJobDetails(job.Id);

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("\nExiting method due to job error.");
            return job;
        }

        // Perform other tasks. For example, access the assets that are the output of a job, 
        // either by creating URLs to the asset on the server, or by downloading. 
        return job;
    }

    private static void StateChanged(object sender, JobStateChangedEventArgs e)
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
                LogJobStop(job.Id);
                break;
            default:
                break;
        }
    }

<p>
</code>

</pre>

## Кодировка в формате Smooth Streaming

Закодировать видео в формате Smooth Streaming можно двумя способами:

-   напрямую в формате Smooth Streaming;
-   кодировка в формате MP4 и преобразование в Smooth Streaming.

</p>
Для кодирования непосредственно в формате Smooth Streaming используйте код, показанный выше, но при этом воспользуйтесь одной из предустановок кодировщика Smooth Streaming. Полный список предустановок кодировщика см. в статье [Строки предустановок задачи для программы Windows Azure Media Encoder][Строки предустановок задачи для программы Windows Azure Media Encoder].

Чтобы преобразовать MP4-файл в формат Smooth Streaming, используйте Azure Media Packager. Azure Media Packager не поддерживает строковые предустановки, поэтому необходимо задать параметры конфигурации в формате XML. XML-код, необходимый для преобразования MP4 в Smooth Streaming, можно найти в статье [Предустановка задачи для Azure Media Packager][Предустановка задачи для Azure Media Packager]. Скопируйте и вставьте XML в файл с именем MediaPackager\_MP4ToSmooth.xml в вашем проекте. В следующем примере показано, как преобразовать MP4-актив в формат Smooth Streaming. Метод, приведенный ниже, принимает существующий актив и преобразует его.

    private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath) { // Declare a new job to contain the tasks IJob job = _context.Jobs.Create("Convert to Smooth Streaming job"); // Set up the first Task to convert from MP4 to Smooth Streaming. // Read in task configuration XML string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml")); // Get a media packager reference IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager"); // Create a task with the conversion details, using the configuration data ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task", processor, configMp4ToSmooth, TaskOptions.None); // Specify the input asset to be converted. task.InputAssets.Add(assetToConvert); // Add an output asset to contain the results of the job. task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None); // Use the following event handler to check job progress. // The StateChange method is the same as the one in the previous sample job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged); // Launch the job. job.Submit(); // Check job execution and wait for job to finish. Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None); progressJobTask.Wait(); // Get a refreshed job reference after waiting on a thread. job = GetJob(job.Id); // Check for errors if (job.State == JobState.Error) { Console.WriteLine("\nExiting method due to job error."); } return job;}

</p>
Дополнительные сведения об обработке активов см. в следующих статьях:

-   [Обработка активов с помощью пакета Media Services SDK для .NET][Обработка активов с помощью пакета Media Services SDK для .NET]
-   [Обработка активов с помощью API REST служб мультимедиа][Обработка активов с помощью API REST служб мультимедиа]

</p>
## Дальнейшие действия

Вы узнали, как создать задание для кодирования актива. Теперь вы можете приступить к изучению статьи [Проверка хода выполнения задания с помощью служб мультимедиа][Проверка хода выполнения задания с помощью служб мультимедиа].

  [Практическое руководство: Получение процессора мультимедиа]: ../media-services-get-media-processor/
  [Магазине Azure]: https://datamarket.azure.com/
  [предустановок кодировщика]: http://msdn.microsoft.com/ru-ru/library/hh973610.aspx
  [Строки предустановок задачи для программы Windows Azure Media Encoder]: http://msdn.microsoft.com/ru-ru/library/jj129582.aspx
  [Предустановка задачи для Azure Media Packager]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh973635.aspx
  [Обработка активов с помощью пакета Media Services SDK для .NET]: http://msdn.microsoft.com/ru-ru/library/jj129580.aspx
  [Обработка активов с помощью API REST служб мультимедиа]: http://msdn.microsoft.com/ru-ru/library/jj129574.aspx
  [Проверка хода выполнения задания с помощью служб мультимедиа]: ../media-services-check-job-progress/
