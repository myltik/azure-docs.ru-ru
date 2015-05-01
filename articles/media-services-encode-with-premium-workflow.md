<properties 
	pageTitle="Дополнительное кодирование с помощью рабочего процесса Premium кодировщика мультимедиа" 
	description="Узнайте, как выполнять дополнительное кодирование с помощью рабочего процесса Premium кодировщика мультимедиа. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="juliako"/>

#Дополнительное кодирование с помощью рабочего процесса Premium кодировщика мультимедиа (общедоступная предварительная версия)

**Примечание** Обработчик мультимедиа рабочего процесса Premium Media Encoder, рассмотренный в данном разделе, недоступен в Китае.

##Обзор

Службы мультимедиа Azure корпорации Майкрософт представляют общедоступную предварительную версию обработчика **рабочего процесса Premium кодировщика мультимедиа**. Данный обработчик предоставляет расширенные возможности кодирования для рабочих процессов уровня premium по требованию. 

В следующих разделах приводятся подробные сведения o **рабочем процессе Premium кодировщика мультимедиа**: 

- [Форматы, поддерживаемые рабочим процессом Premium кодировщика мультимедиа](media-services-premium-workflow-encoder-formats.md) - обсуждаются форматы файлов и кодеков, поддерживаемых **рабочим процессом Premium кодировщика мультимедиа**.

- В разделе [Сравнение кодировщиков](media-services-encode-asset.md#compare_encoders) сравниваются возможности кодирования **рабочего процесса Premium кодировщика мультимедиа** и **кодировщика служб мультимедиа Azure**.

В этом разделе показаны способы кодирования с **рабочим процессом Premium кодировщика мультимедиа** с использованием .NET.

##Кодирование

Задачи кодирования для **рабочего процесса Premium кодировщика мультимедиа** требуют наличия отдельного файла конфигурации, который называется файлом рабочего процесса Workflow). Эти файлы имеют расширение WORKFLOW и создаются с помощью [Конструктора рабочих процессов](media-services-workflow-designer.md).

Также можно получить файлы рабочего процесса с настройками по умолчанию [отсюда](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Папка также содержит описание этих файлов.

Файлы рабочего процесса должны быть отправлены в учетную запись служб мультимедиа в виде ресурса, и этот ресурс нужно передать в задачу кодирования.

В следующем примере показано, как кодировать с помощью **рабочего процесса Premium кодировщика мультимедиа**. 

Выполняются следующие шаги. 
 
1. Создание ресурса и отправка файла рабочего процесса. 
2. Создание ресурса и отправка исходного файла мультимедиа.
3. Получение обработчика мультимедиа "Рабочий процесс Premium кодировщика мультимедиа".
4. Создание задания и задачи.
5. Добавление двух входных ресурсов в задачу.
	
	а. Первый - ресурс рабочего процесса.

	б. Второй - ресурс видео.
	
	**Примечание**. Ресурс рабочего процесса необходимо добавить в задачу перед добавлением ресурса мультимедиа. 
Строка конфигурации для этой задачи должна быть пуста. 

6. Отправка задания кодирования.

Ниже представлен завершенный пример. Сведения о настройке .NET для разработки служб мультимедиа см. в разделе [Разработка для служб мультимедиа с помощью .NET](media-services-dotnet-how-to-use.md).


 	using System; 
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace MediaEncoderPremiumWorkflowSample
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
	
	        private static readonly string _supportFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        private static readonly string _workflowFilePath =
	            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
	        
	        private static readonly string _singleMP4InputFilePath =
	            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
	
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
	            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
	            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
	
	        }
	
	        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
	        {
	            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
	            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	
	            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
	                                                                AccessPermissions.Write | AccessPermissions.List);
	
	            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
	
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading {0}", assetFile.Name);
	
	            locator.Delete();
	            accessPolicy.Delete();
	
	            return asset;
	        }
	
	        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
	            // Получение ссылки на обработчик мультимедиа и передача ему имени 
	            // обработчика для определенной задачи.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
	
	            // Создание задачи с данными кодировки и с использованием стиля строки.
	            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
	                processor,
	                "",
	                TaskOptions.None);
	
	            // Указание входного ресурса для кодирования.
	            task.InputAssets.Add(workflow);
	            task.InputAssets.Add(video); // добавляем один ресурс
	            // Добавление выходного актива для хранения результатов выполнения задания. 
	            // Эти выходные данные указываются как AssetCreationOptions.None, что 
	            // означает, что выходной ресурс не шифруется. 
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Следующий обработчик событий используется для проверки хода выполнения задания.  
	            job.StateChanged += new
	                    EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	            // Запуск задания.
	            job.Submit();
	
	            // Проверка выполнения задания и ожидание его завершения. 
	            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	            progressJobTask.Wait();
	
	            // Если возникла ошибка, метод обработки событий 
	            // для отслеживания хода выполнения задания должен записать ошибки в журнал.  Здесь мы проверяем 
	            // состояние ошибки и при необходимости выходим.
	            if (job.State == JobState.Error)
	            {
	                throw new Exception("\nExiting method due to job error.");
	            }
	
	            return job.OutputMediaAssets[0];
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
	                    LogJobStop(job.Id);
	                    break;
	                default:
	                    break;
	            }
	        }
	
	        static private void LogJobStop(string jobId)
	        {
	            StringBuilder builder = new StringBuilder();
	            IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
	
	            builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	            builder.AppendLine("***************************");
	            builder.AppendLine("Job ID: " + job.Id);
	            builder.AppendLine("Job Name: " + job.Name);
	            builder.AppendLine("Job State: " + job.State.ToString());
	            builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	            builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
	            // Log job errors if they exist.  
	            if (job.State == JobState.Error)
	            {
	                builder.Append("Error Details: \n");
	                foreach (ITask task in job.Tasks)
	                {
	                    foreach (ErrorDetail detail in task.ErrorDetails)
	                    {
	                        builder.AppendLine("  Task Id: " + task.Id);
	                        builder.AppendLine("    Error Code: " + detail.Code);
	                        builder.AppendLine("    Error Message: " + detail.Message + "\n");
	                    }
	                }
	            }
	            builder.AppendLine("***************************\n");
	
	            Console.Write(builder.ToString());
	        }
	
	
	        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	            return processor;
	        }
	    }
	}


##Известные проблемы

Если входное видео не содержит субтитров, выходной ресурс будет по-прежнему содержать пустой TTML-файл.


<!--HONumber=52-->