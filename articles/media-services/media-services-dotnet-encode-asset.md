<properties 
	pageTitle="Как кодировать ресурс с помощью обработчика мультимедиа Azure" 
	description="Узнайте, как использовать кодировщик мультимедиа Azure для кодировки мультимедийного контента в службах мультимедиа. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="juliako"/>


#Как кодировать ресурс с помощью обработчика мультимедиа Azure


> [AZURE.SELECTOR]
- [REST](media-services-rest-encode-asset.md)
- [.NET](media-services-dotnet-encode-asset.md)
- [Portal](media-services-manage-content.md#encode)

##Обзор

Для поставки цифрового видео по Интернету необходимо сжать мультимедиа. Цифровые видеофайлы довольно объемные. Это затрудняет их доставку через Интернет и влияет на качество их отображения на устройствах клиентов. Кодирование — это процесс сжатия аудио- и видеофайлов, чтобы их могли просматривать клиенты.

Задания кодирования — одни из самых распространенных операций обработки в службах мультимедиа. Они создаются для преобразования файлов мультимедиа из одного формата кодирования в другой. При кодировании можно использовать встроенный кодировщик служб мультимедиа. Также можно использовать кодировщик, предоставленный партнером служб мультимедиа, кодировщики сторонних производителей доступны в Магазине Azure. Информацию о задачах кодировки можно указать с помощью строк предустановок, заданных для кодировщика, или файлов конфигурации. См. типы доступных предустановок в разделе [Предустановки задач для служб мультимедиа Azure](https://msdn.microsoft.com/library/azure/dn619392.aspx). Если вы использовали сторонний кодировщик, вам следует [проверить свои файлы](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Мы советуем всегда кодировать мезонинные файлы в набор MP4-файлов с адаптивной скоростью, а затем преобразовывать его в нужный формат с помощью [динамической упаковки](https://msdn.microsoft.com/library/azure/jj889436.aspx). Для использования динамической упаковки вам потребуется получить по крайней мере одну единицу потоковой передачи по запросу для конечной точки потоковой передачи, из которой планируется передавать содержимое. Дополнительные сведения см. в статье [Масштабирование служб мультимедиа](media-services-manage-origins.md#scale_streaming_endpoints).

Если выходящий ресурс зашифрован в хранилище, необходимо настроить политику доставки ресурсов. Дополнительную информацию см. в разделе [Настройка политики доставки ресурсов](media-services-dotnet-configure-asset-delivery-policy.md).

##Создание задания с одной задачей кодирования 

При кодировании с помощью кодировщика служб мультимедиа Azure вы можете использовать предустановки конфигурации задач, указанные [здесь](https://msdn.microsoft.com/library/azure/dn619389.aspx).

###Использование пакета SDK служб мультимедиа для .NET  

Следующий метод **EncodeToAdaptiveBitrateMP4Set** создает задание кодирования и добавляет в задание одну задачу кодирования. Для выполнения задачи используется кодировщик мультимедиа Microsoft Azure, выполняющий кодирование в формат "H264 с адаптивной скоростью MP4 Set 720p".

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
    {
        var encodingPreset = "H264 Adaptive Bitrate MP4 Set 720p";

        IJob job = _context.Jobs.Create(String.Format("Encoding {0} into to {1}",
                                inputAsset.Name,
                                encodingPreset));

        var mediaProcessors = GetLatestMediaProcessorByName("Azure Media Encoder");

        ITask encodeTask = job.Tasks.AddNew("Encoding", mediaProcessors, encodingPreset, TaskOptions.None);
        
        encodeTask.InputAssets.Add(inputAsset);

        // Specify the storage-encrypted output asset.
        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 
            AssetCreationOptions.StorageEncrypted);


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

###Использование расширений пакета SDK служб мультимедиа для .NET

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
    {
        // 1. Prepare a job with a single task to transcode the specified mezzanine asset
        //    into a multi-bitrate asset.
        IJob job = _context.Jobs.CreateWithSingleTask(
            MediaProcessorNames.AzureMediaEncoder,
            MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
            asset,
            "Adaptive Bitrate MP4",
            AssetCreationOptions.None);

        Console.WriteLine("Submitting transcoding job...");

        // 2. Submit the job and wait until it is completed.
        job.Submit();
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    } 

##Создание задания с цепными задачами 

Во многих сценариях приложений разработчикам необходимо создать серию задач обработки. В службах мультимедиа вы можете создавать серии цепных задач. Каждая задача выполняет разные шаги обработки. Эти задачи также могут использовать разные обработчики мультимедиа. Цепные задачи могут передать ресурсы из одной задачи в другую, следуя линейной последовательности задач ресурса. Тем не менее, в последовательность не должны быть включены задачи, выполняемые в задании. При создании цепной задачи цепные объекты **ITask** создаются в одном объекте **IJob**.

>[AZURE.NOTE]Сейчас количество задач ограничено 30 задачами на задание. Если вам необходимо создать цепь из более чем 30 задач, создайте для них несколько заданий.

Следующий метод **CreateChainedTaskEncodingJob** создает задание, содержащее две цепные задачи. Как следствие метод возвращает задание, которое содержит два выходных ресурса.

	
    public static IJob CreateChainedTaskEncodingJob(IAsset asset)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("My task-chained encoding job");

        // Set up the first task to encode the input file.

        // Get a media processor reference
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
           "H264 Adaptive Bitrate MP4 Set 720p",
            TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Specify the storage-encrypted output asset.
        task.OutputAssets.AddNew("My storage-encrypted output asset",
            AssetCreationOptions.StorageEncrypted);

        // Set up the second task to decrypt the encoded output file from 
        // the first task.

        // Get another media processor instance
        IMediaProcessor decryptProcessor = GetLatestMediaProcessorByName("Storage Decryption");

        // Declare the decryption task. 
        ITask decryptTask = job.Tasks.AddNew("My decryption task",
            decryptProcessor,
            string.Empty,
            TaskOptions.None);

        // Specify the input asset to be decrypted. This is the output 
        // asset from the first task. 
        decryptTask.InputAssets.Add(task.OutputAssets[0]);

        // Specify an output asset to contain the results of the job. 
        // This should have AssetCreationOptions.None. 
        decryptTask.OutputAssets.AddNew("My decrypted output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(JobStateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        //return job that contains two output assets.
        return job;
    }


##Дальнейшие действия

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=July15_HO5-->