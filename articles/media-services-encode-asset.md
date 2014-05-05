<properties linkid="develop-media-services-how-to-guides-encode-an-asset" urlDisplayName="Кодировка актива" pageTitle="Кодировка актива для служб мультимедиа — Azure" metaKeywords="" description="Узнайте, как использовать кодировщик мультимедиа Azure для кодирования контента в службах мультимедиа. Примеры кода написаны на C# и используют Media Services SDK для .NET." metaCanonical="" services="media-services" documentationCenter="" title="Кодировка актива" authors="migree" solutions="" manager="" editor="" />


#Кодировка актива
Данная статья является частью серии вводных статей о программировании служб мультимедиа Azure. Предыдущая статья: [Получение мультимедийного процессора](http://go.microsoft.com/fwlink/?LinkID=301732&ampclcid=0x409).

Мультимедийный контент на сервере можно закодировать в разных форматах и кодировках, используя кодировщик мультимедиа Azure. Также можно использовать кодировщик, предоставленный партнером служб мультимедиа, кодировщики сторонних производителей доступны в [Магазине Azure][]. Сведения о задачах кодировки можно указать с помощью строк [предустановок кодировщика][] или файлов конфигурации. 

##Кодировка в формате MP4
Следующий метод отправляет один актив и создает задание для его кодирования в формате MP4 с помощью предустановки "H264 Broadband 720p", которая создает один MP4-файл с кодировкой H264 и разрешением 720p:
<pre><code>
	static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
	{
    	//Create an encrypted asset and upload to storage.
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
			inputMediaFilePath);

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
</code></pre>
<h2>Кодировка в формате Smooth Streaming</h2>
Закодировать видео в формате Smooth Streaming можно двумя способами:
<ul>
<li> напрямую в формате Smooth Streaming; </li>
<li> кодировка в формате MP4 и преобразование в Smooth Streaming.</li>
</ul>

Для кодирования непосредственно в формате Smooth Streaming используйте код, показанный выше, но при этом воспользуйтесь одной из предустановок кодировщика Smooth Streaming. Полный список предустановок кодировщика см. в статье [Строки предустановок задачи для программы Azure Media Encoder](http://msdn.microsoft.com/ru-ru/library/jj129582.aspx). 

Чтобы преобразовать MP4-файл в формат Smooth Streaming, используйте Azure Media Packager. Azure Media Packager не поддерживает строковые предустановки, поэтому необходимо задать параметры конфигурации в формате XML. XML-код, необходимый для преобразования MP4 в Smooth Streaming, можно найти в статье [Предустановка задачи для Azure Media Packager][]. Скопируйте и вставьте XML-код в файл с именем MediaPackager_MP4ToSmooth.xml в вашем проекте. В следующем примере показано, как преобразовать MP4-актив в формат Smooth Streaming. Метод, приведенный ниже, принимает существующий актив и преобразует его. 
<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
	// Declare a new job to contain the tasks
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Set up the first Task to convert from MP4 to Smooth Streaming. 
    // Read in task configuration XML
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Get a media packager reference
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Create a task with the conversion details, using the configuration data
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Specify the input asset to be converted.
    task.InputAssets.Add(assetToConvert);
    // Add an output asset to contain the results of the job.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Use the following event handler to check job progress. 
	// The StateChange method is the same as the one in the previous sample
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    // Launch the job.
    job.Submit();
    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Get a refreshed job reference after waiting on a thread.
    job = GetJob(job.Id);
    // Check for errors
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>

Дополнительные сведения об обработке активов см. в следующих статьях:
<ul>
<li><a href="http://msdn.microsoft.com/ru-ru/library/jj129580.aspx">Обработка активов с помощью пакета Media Services SDK для .NET</a></li>
<li><a href="http://msdn.microsoft.com/ru-ru/library/jj129574.aspx">Обработка активов с помощью API REST служб мультимедиа</a></li>
</ul>

##Дальнейшие действия
Вы узнали, как создать задание для кодирования актива. Теперь вы можете приступить к изучению статьи [Проверка хода выполнения задания с помощью служб мультимедиа](http://go.microsoft.com/fwlink/?LinkID=301737&ampclcid=0x409).

[Магазине Azure]: https://datamarket.azure.com/
[предустановок кодировщика]: http://msdn.microsoft.com/ru-ru/library/hh973610.aspx
[Получение экземпляра процессора мультимедиа]:http://go.microsoft.com/fwlink/?LinkId=301732
[Отправка зашифрованного актива]:http://go.microsoft.com/fwlink/?LinkId=301733
[Доставка актива путем загрузки]:http://go.microsoft.com/fwlink/?LinkId=301734
[Проверка хода выполнения задания]:http://go.microsoft.com/fwlink/?LinkId=301737
[Предустановка задачи для Azure Media Packager]:http://msdn.microsoft.com/ru-ru/library/windowsazure/hh973635.aspx

