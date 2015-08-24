<properties 
	pageTitle="Как проверить ход выполнения задания с помощью .NET" 
	description="Узнайте, как использовать код обработчика событий для проверки хода выполнения задания и отправки сведений об обновлении состояния. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." 
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
	ms.date="08/11/2015"  
	ms.author="juliako"/>

#Практическое руководство. Проверка хода выполнения задания

> [AZURE.SELECTOR]
- [Portal](media-services-portal-check-job-progress.md)
- [.NET](media-services-check-job-progress.md)
- [REST](media-services-rest-check-job-progress.md)

При выполнении заданий часто требуется способ отслеживания хода выполнения задачи. Вы можете проверить ход выполнения, [определив обработчик событий StateChanged](#statechange_event_handler) или [используя хранилище очередей Azure для наблюдения за уведомлениями о заданиях служб мультимедиа](#check_progress_with_queues). В этом разделе описаны оба способа.

##<a id="statechange_event_handler"></a>Определение обработчика событий StateChanged для отслеживания хода выполнения задания

Следующий пример кода определяет обработчик событий StateChanged. Этот обработчик отслеживает ход выполнения задачи и предоставляет обновленное состояние. Кроме того, код определяет метод LogJobStop. Этот вспомогательный метод заносит в журнал сведения об ошибках.

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
	            LogJobStop(job.Id);
	            break;
	        default:
	            break;
	    }
	}
	
	private static void LogJobStop(string jobId)
	{
	    StringBuilder builder = new StringBuilder();
	    IJob job = GetJob(jobId);
	
	    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	    builder.AppendLine("***************************");
	    builder.AppendLine("Job ID: " + job.Id);
	    builder.AppendLine("Job Name: " + job.Name);
	    builder.AppendLine("Job State: " + job.State.ToString());
	    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	    builder.AppendLine("Media Services account name: " + _accountName);
	    builder.AppendLine("Media Services account location: " + _accountLocation);
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
	    // Write the output to a local file and to the console. The template 
	    // for an error output file is:  JobStop-{JobId}.txt
	    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
	    WriteToFile(outputFile, builder.ToString());
	    Console.Write(builder.ToString());
	}
	
	private static string JobIdAsFileName(string jobID)
	{
	    return jobID.Replace(":", "_");
	}



##<a id="check_progress_with_queues"></a>Использование хранилища очередей Azure для наблюдения за уведомлениями о заданиях служб мультимедиа

Службы мультимедиа Microsoft Azure позволяют доставлять уведомления в [хранилище очередей Azure](../storage-dotnet-how-to-use-queues.md#what-is) при обработке заданий мультимедиа. В этом разделе показано, как получить эти уведомления из хранилища очередей.

Доступ к сообщениям, доставленным в хранилище очередей, можно получить в любой точке мира. Служба очередей Azure предоставляет надежную и масштабируемую архитектуру обмена сообщениями. Рекомендуется опрашивать хранилище очередей, а не использовать другие методы.

Вот один из распространенных сценариев прослушивания уведомлений служб мультимедиа: вы разрабатываете систему управления содержимым, которая должна выполнить ряд дополнительных задач после завершения кодирования (например, инициировать следующий шаг в рабочем процессе или опубликовать содержимое).

###Рекомендации

При разработке приложений для служб мультимедиа, использующих очередь хранилища Azure, учитывайте следующее.

- Служба очередей не гарантирует доставку "первым пришел, первым вышел" (FIFO). Дополнительную информацию см. в разделе [Очереди Azure и очереди служебной шины Azure — сравнение и противопоставление](https://msdn.microsoft.com/library/azure/hh767287.aspx).
- Служба очередей хранилища Azure — это не служба push-уведомлений, вам необходимо опрашивать очередь. 
- Можно использовать любое количество очередей. Дополнительную информацию см. в разделе [REST API службы очередей](https://msdn.microsoft.com/library/azure/dd179363.aspx).
- С очередями хранилища Azure связан ряд ограничений и особенностей, которые описаны в следующей статье: [Очереди Azure и очереди служебной шины Azure — сравнение и противопоставление](https://msdn.microsoft.com/library/azure/hh767287.aspx).

###Пример кода

Пример кода в этом разделе выполняет следующие задачи:

1. определяет класс **EncodingJobMessage**, который сопоставляется с форматом уведомления; десериализует сообщения, полученные из очереди, в объекты типа **EncodingJobMessage**;
1. загружает данные учетной записи служб мультимедиа и хранилища из файла app.config; использует эти сведения для создания объектов **CloudMediaContext** и **CloudQueue**;
1. создает очередь, которая будет получать уведомления о задании кодирования;
1. создает конечную точку уведомлений, сопоставленную с очередью;
1. присоединяет конечную точку уведомлений к заданию и отправляет задание кодирования. К заданию может быть прикреплено несколько конечных точек уведомлений.
1. В этом примере нас интересуют только конечные состояния обработки задания, поэтому мы передаем **NotificationJobState.FinalStatesOnly** в метод **AddNew**. 
		
		job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. Если передать параметр NotificationJobState.All, вы получите все уведомления об изменении состояния: Queued -> Scheduled -> Processing -> Finished. Тем не менее, как было отмечено ранее, служба очередей хранилища Azure не гарантирует упорядоченной доставки. Можно использовать свойство Timestamp (определено в типе EncodingJobMessage в примере ниже), чтобы упорядочить сообщения. Возможно, вы получите повторяющиеся уведомления. Используйте свойство ETag (определенное в типе EncodingJobMessage) для проверки наличия повторений. Также возможно, что некоторые уведомления об изменении состояния будут пропущены. 
1. Ожидает, пока задание не перейдет в состояние завершения, проверяя очередь каждые 10 секунд. Удаляет сообщения после их обработки.
1. Удаляет очередь и конечную точку уведомлений.

>[AZURE.NOTE]Рекомендуемый способ наблюдения за состоянием задания — прослушивание уведомлений, как показано в следующем примере.
>
>Кроме того, можно проверить состояние задания с помощью свойства **IJob.State**. Обратите внимание, что уведомление о завершении задания может поступить до того, как свойство State в **IJob** станет равно **Finished**. Свойство **IJob.State** отражает точное состояние с небольшой задержкой.

	
	using System;
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Web;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;
	using System.Runtime.Serialization.Json;
	
	namespace JobNotification
	{
	    public class EncodingJobMessage
	    {
	        // MessageVersion is used for version control. 
	        public String MessageVersion { get; set; }
	    
	        // Type of the event. Valid values are 
	        // JobStateChange and NotificationEndpointRegistration.
	        public String EventType { get; set; }
	    
	        // ETag is used to help the customer detect if 
	        // the message is a duplicate of another message previously sent.
	        public String ETag { get; set; }
	    
	        // Time of occurrence of the event.
	        public String TimeStamp { get; set; }
	
	        // Collection of values specific to the event.
	
	        // For the JobStateChange event the values are:
	        //     JobId - Id of the Job that triggered the notification.
	        //     NewState- The new state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	        //     OldState- The old state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	
	        // For the NotificationEndpointRegistration event the values are:
	        //     NotificationEndpointId- Id of the NotificationEndpoint 
	        //          that triggered the notification.
	        //     State- The state of the Endpoint. 
	        //          Valid values are: Registered and Unregistered.
	
	        public IDictionary<string, object> Properties { get; set; }
	    }
	
	    class Program
	    {
	        private static CloudMediaContext _context = null;
	        private static CloudQueue _queue = null;
	        private static INotificationEndPoint _notificationEndPoint = null;
	
	        private static readonly string _singleInputMp4Path =
	            Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");
	
	        static void Main(string[] args)
	        {
	            // Get the values from app.config file.
	            string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	            string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	            string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];
	
	
	            string endPointAddress = Guid.NewGuid().ToString();
	
	            // Create the context. 
	            _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);
	
	            // Create the queue that will be receiving the notification messages.
	            _queue = CreateQueue(storageConnectionString, endPointAddress);
	
	            // Create the notification point that is mapped to the queue.
	            _notificationEndPoint = 
	                    _context.NotificationEndPoints.Create(
	                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);
	
	
	            if (_notificationEndPoint != null)
	            {
	                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
	                WaitForJobToReachedFinishedState(job.Id);
	            }
	
	            // Clean up.
	            _queue.Delete();      
	            _notificationEndPoint.Delete();
	       }
	
	
	        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
	        {
	            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);
	
	            // Create the queue client
	            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
	
	            // Retrieve a reference to a queue
	            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);
	
	            // Create the queue if it doesn't already exist
	            queue.CreateIfNotExists();
	
	            return queue;
	        }
	 
	
	        // Upload a video file, and encode to Smooth Streaming format
	        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
	
	            //Create an encrypted asset and upload the mp4. 
	            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
	                inputMediaFilePath);
	
	            // Get a media processor reference, and pass to it the name of the 
	            // processor to use for the specific task.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	
	            // Create a task with the conversion details, using a configuration file. 
	            ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
	                processor,
	                "H264 Smooth Streaming 720p",
	                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job.
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Add a notification point to the job. You can add multiple notification points.  
	            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, 
	                _notificationEndPoint);
	
	            job.Submit();
	
	            return job;
	        }
	
	        public static void WaitForJobToReachedFinishedState(string jobId)
	        {
	            int expectedState = (int)JobState.Finished;
	            int timeOutInSeconds = 600;
	
	            bool jobReachedExpectedState = false;
	            DateTime startTime = DateTime.Now;
	            int jobState = -1;
	
	            while (!jobReachedExpectedState)
	            {
	                // Specify how often you want to get messages from the queue.
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	
	                foreach (var message in _queue.GetMessages(10))
	                {
	                    using (Stream stream = new MemoryStream(message.AsBytes))
	                    {
	                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
	                        settings.UseSimpleDictionaryFormat = true;
	                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
	                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);
	
	                        Console.WriteLine();
	
	                        // Display the message information.
	                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
	                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
	                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
	                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
	                        foreach (var property in encodingJobMsg.Properties)
	                        {
	                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
	                        }
	
	                        // We are only interested in messages 
	                        // where EventType is "JobStateChange".
	                        if (encodingJobMsg.EventType == "JobStateChange")
	                        {
	                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
	                            if (JobId == jobId)
	                            {
	                                string oldJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
	                                string newJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;
	
	                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
	                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);
	
	                                if (newJobState == (JobState)expectedState)
	                                {
	                                    Console.WriteLine("job with Id: {0} reached expected state: {1}", 
	                                        jobId, newJobState);
	                                    jobReachedExpectedState = true;
	                                    break;
	                                }
	                            }
	                        }
	                    }
	                    // Delete the message after we've read it.
	                    _queue.DeleteMessage(message);
	                }
	
	                // Wait until timeout
	                TimeSpan timeDiff = DateTime.Now - startTime;
	                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
	                if (timedOut)
	                {
	                    Console.WriteLine(@"Timeout for checking job notification messages, 
	                                        latest found state ='{0}', wait time = {1} secs",
	                        jobState,
	                        timeDiff.TotalSeconds);
	
	                    throw new TimeoutException();
	                }
	            }
	        }
	   
	        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
	        {
	            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(), 
	                assetCreationOptions);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading of {0}", assetFile.Name);
	
	            return asset;
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

Приведенный выше пример формирует следующие выходные данные. Фактические значения могут отличаться.
	
	Created assetFile BigBuckBunny.mp4
	Upload BigBuckBunny.mp4
	Done uploading of BigBuckBunny.mp4
	
	EventType: NotificationEndPointRegistration
	MessageVersion: 1.0
	ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
	TimeStamp: 2013-05-14T20:22:37
	    NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
	    State: Registered
	    Name: dde957b2-006e-41f2-9869-a978870ac620
	    Created: 2013-05-14T20:22:35
	
	EventType: JobStateChange
	MessageVersion: 1.0
	ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
	TimeStamp: 2013-05-14T20:24:40
	    JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
	    JobName: My MP4 to Smooth Streaming encoding job
	    NewState: Finished
	    OldState: Processing
	    AccountName: westeuropewamsaccount
	job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected 
	State: Finished
	
 

<!---HONumber=August15_HO7-->