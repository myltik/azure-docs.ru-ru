<properties 
	pageTitle="Управление активами служб мультимедиа в нескольких учетных записях хранения" 
	description="В этой статье приведены рекомендации по управлению файлами служб мультимедиа в нескольких учетных записях хранения." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>


#Управление активами служб мультимедиа в нескольких учетных записях хранения

Начиная с версии 2.2 служб мультимедиа Microsoft Azure, можно подключать несколько учетных записей хранения к одной учетной записи служб мультимедиа. Возможность подключить несколько учетных записей хранения к учетной записи служб мультимедиа дает несколько преимуществ: распределение нагрузки ваших файлов между несколькими учетными записями хранения;

масштабирование служб мультимедиа для больших объемов обработки содержимого (сейчас действует ограничение в 500 ТБ на одну учетную запись хранения).

В этом разделе показано, как подключить несколько учетных записей хранения к учетной записи служб мультимедиа с помощью [API REST управления службами Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx). Здесь также описывается, как указывать другие учетные записи хранения при создании файлов с помощью пакета SDK служб мультимедиа.

##Рекомендации

При подключении нескольких учетных записей хранения к своей учетной записи служб мультимедиа обратите внимание на следующее.

- Все учетные записи хранения, подключенные к учетной записи служб мультимедиа, должны находиться в одном центре обработки данных с учетной записью служб мультимедиа.
- После подключения учетной записи хранения к указанной учетной записи служб мультимедиа ее невозможно отключить.
- Основная учетная запись хранения — это запись, указанная во время создания учетной записи служб мультимедиа. Сейчас нельзя изменить учетную запись хранения по умолчанию. 

Дополнительные рекомендации

При создании URL-адресов для потоковой передачи содержимого службы мультимедиа используют значение свойства **IAssetFile.Name** (например, http://{WAMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Поэтому кодирование с помощью знака процента не допускается. Значение свойства Name не может содержать такие [символы, зарезервированные для кодирования с помощью знака процента](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#". Кроме того, может использоваться только один символ "." для расширения имени файла.

##Подключение учетной записи хранения с помощью API REST управления службами Azure

Сейчас единственным способом подключения нескольких учетных записей хранения является использование [API REST управления службами Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx). В примере кода в статье [Как использовать API REST управления службами мультимедиа ](https://msdn.microsoft.com/library/azure/dn167656.aspx) описывается метод **AttachStorageAccountToMediaServiceAccount**, который подключает учетную запись хранения к указанной учетной записи служб мультимедиа. Код в том же разделе определяет метод **ListStorageAccountDetails**, который выводит все учетные записи хранения, подключенные к указанной учетной записи служб мультимедиа.


##Управление файлами служб мультимедиа в нескольких учетных записях хранения

В приведенном ниже коде используется последняя версия пакета SDK служб мультимедиа для выполнения следующих задач.

1. Отображение всех учетных записей хранения, связанных с указанной учетной записью служб мультимедиа.
1. Получение имени учетной записи хранения по умолчанию.
1. Создание нового файла в учетной записи хранения по умолчанию.
1. Создание выходного файла задания кодирования в указанной учетной записи хранения.
	
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace MultipleStorageAccounts
		{
		    class Program
		    {
		        // Location of the media file that you want to encode. 
		        private static readonly string _singleInputFilePath =
		            Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");
		
		        private static readonly string MediaServicesAccountName = 
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string MediaServicesAccountKey = 
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static CloudMediaContext _context;
		        private static MediaServicesCredentials _cachedCredentials = null;
	
		        static void Main(string[] args)
		        {
	
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            MediaServicesAccountName,
		                            MediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
	
		
		            // Display the storage accounts associated with 
		            // the specified Media Services account:
		            foreach (var sa in _context.StorageAccounts)
		                Console.WriteLine(sa.Name);
		
		            // Retrieve the name of the default storage account.
		            var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
		            Console.WriteLine("Name: {0}", defaultStorageName.Name);
		            Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);
		
		            // Retrieve the name of a storage account that is not the default one.
		            var notDefaultStroageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
		            Console.WriteLine("Name: {0}", notDefaultStroageName.Name);
		            Console.WriteLine("IsDefault: {0}", notDefaultStroageName.IsDefault);
		            
		            // Create the original asset in the default storage account.
		            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, 
		                defaultStorageName.Name, _singleInputFilePath);
		            Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);
		            
		            // Create an output asset of the encoding job in the other storage account.
		            IAsset outputAsset = CreateEncodingJob(asset, notDefaultStroageName.Name, _singleInputFilePath);
		            if(outputAsset!=null)
		                Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);
		
		        }
		
		        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
		        {
		            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
		            
		            // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
		            var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);
		
		            var fileName = Path.GetFileName(singleFilePath);
		
		            var assetFile = asset.AssetFiles.Create(fileName);
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            return asset;
		        }
		
		        static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
		        {
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My encoding job");
		            // Get a media processor reference, and pass to it the name of the 
		            // processor to use for the specific task.
		            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My encoding task",
		                processor,
		                "H264 Broadband 720p",
		                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);
		
		            // Specify the input asset to be encoded.
		            task.InputAssets.Add(asset);
		            // Add an output asset to contain the results of the job. 
		            // This output is specified as AssetCreationOptions.None, which 
		            // means the output asset is not encrypted. 
		            task.OutputAssets.AddNew("Output asset", storageName,
		                AssetCreationOptions.None);
		
		            // Use the following event handler to check job progress.  
		            job.StateChanged += new
		                    EventHandler<JobStateChangedEventArgs>(StateChanged);
		
		            // Launch the job.
		            job.Submit();
		
		            // Check job execution and wait for job to finish. 
		            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
		            progressJobTask.Wait();
		
		            // Get an updated job reference.
		            job = GetJob(job.Id);
		
		            // If job state is Error the event handling 
		            // method for job progress should log errors.  Here we check 
		            // for error state and exit if needed.
		            if (job.State == JobState.Error)
		            {
		                Console.WriteLine("\nExiting method due to job error.");
		                return null;
		            }
		
		            // Get a reference to the output asset from the job.
		            IAsset outputAsset = job.OutputMediaAssets[0];
		
		            return outputAsset;
		        }
		
		
		        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		            return processor;
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
		                    Console.WriteLine("********************");
		                    Console.WriteLine("Job is finished.");
		                    Console.WriteLine("Please wait while local tasks or downloads complete...");
		                    Console.WriteLine("********************");
		                    Console.WriteLine();
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
		                    Console.WriteLine("An error occurred in {0}", job.Id);
		                    break;
		                default:
		                    break;
		            }
		        }
		
		        static IJob GetJob(string jobId)
		        {
		            // Use a Linq select query to get an updated 
		            // reference by Id. 
		            var jobInstance =
		                from j in _context.Jobs
		                where j.Id == jobId
		                select j;
		            // Return the job reference as an Ijob. 
		            IJob job = jobInstance.FirstOrDefault();
		
		            return job;
		        }
		    }
		}
 

##Схемы обучения работе со службами мультимедиа

Схемы обучения AMS можно просмотреть здесь:

- [Рабочий процесс для потоковой передачи в реальном времени в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Рабочий процесс для потоковой передачи по запросу в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Sept15_HO2-->