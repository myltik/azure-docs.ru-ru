
<properties 
	pageTitle="Управление активами и связанными сущностями с помощью пакета SDK служб мультимедиа для .NET" 
	description="Описание способов управления активами и связанными сущностями с помощью пакета SDK служб мультимедиа для .NET." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="05/03/2016"
	ms.author="juliako"/>


#Управление активами и связанными сущностями с помощью пакета SDK служб мультимедиа для .NET


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [REST](media-services-rest-manage-entities.md)


В этом разделе показано, как выполнять следующие задачи управления службами мультимедиа:

- Получение ссылки на актив 
- Получение ссылки на задание 
- Перечисление всех активов 
- Перечисление заданий и активов 
- Перечисление всех политик доступа 
- Перечисление всех указателей
- Перечисление больших коллекций сущностей
- Удаление актива 
- Удаление задания 
- Удаление политики доступа 

##Предварительные требования 

См. раздел [Настройка среды](media-services-set-up-computer.md).

##Получение ссылки на актив

Распространенной задачей является получение ссылки на существующий актив в службах мультимедиа. В следующем примере кода показано, как можно получить ссылку на актив (Assets) из коллекции активов в объекте контекста сервера, указав идентификатор (Id) актива. В следующем примере кода используется запрос Linq для получения ссылки на существующий объект IAsset.

	static IAsset GetAsset(string assetId)
	{
	    // Use a LINQ Select query to get an asset.
	    var assetInstance =
	        from a in _context.Assets
	        where a.Id == assetId
	        select a;
	    // Reference the asset as an IAsset.
	    IAsset asset = assetInstance.FirstOrDefault();
	
	    return asset;
	}

##Получение ссылки на задание

При работе с задачами обработки в коде служб мультимедиа часто требуется получить ссылку на существующее задание по идентификатору. В следующем примере кода показано, как получить ссылку на объект IJob из коллекции заданий (Jobs). Предупреждение. Получение ссылки на задание может потребоваться при запуске длительного задания кодирования, когда необходимо проверять состояние задания в программном потоке. В таких случаях при возвращении метода из потока требуется получить обновленную ссылку на задание.

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

##Перечисление всех активов

По мере роста числа активов в хранилище полезно получать список всех активов. В следующем примере кода показано, как обойти всю коллекцию активов (Assets) в объекте контекста сервера. С каждым активом пример также записывает некоторые значения его свойств в консоль. Например, каждый актив может содержать множество файлов мультимедиа. Пример кода записывает все файлы, связанные с каждым активом.



	static void ListAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IAsset asset in _context.Assets)
	    {
	        // Display the collection of assets.
	        builder.AppendLine("");
	        builder.AppendLine("******ASSET******");
	        builder.AppendLine("Asset ID: " + asset.Id);
	        builder.AppendLine("Name: " + asset.Name);
	        builder.AppendLine("==============");
	        builder.AppendLine("******ASSET FILES******");
	
	        // Display the files associated with each asset. 
	        foreach (IAssetFile fileItem in asset.AssetFiles)
	        {
	            builder.AppendLine("Name: " + fileItem.Name);
	            builder.AppendLine("Size: " + fileItem.ContentFileSize);
	            builder.AppendLine("==============");
	        }
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Перечисление заданий и активов

Важная связанная задача — перечисление активов с заданиями, с которыми они связаны, в службах мультимедиа. В следующем примере кода показано, как перечислить каждый объект IJob, а затем для каждого задания отображаются свойства задания, все связанные задачи, все входные активы и все выходные активы. Данный пример кода может быть полезен для множества других задач. Например, если требуется перечислить выходные активы из одного или нескольких заданий кодирования, которые выполнялись ранее, данный код показывает, как получить доступ к этим выходным активам. При наличии ссылки на выходной актив можно затем доставлять содержимое другим пользователям или приложениям путем загрузки или передачи URL-адресов.

Дополнительные сведения о вариантах доставки ресурсов-контейнеров см. в статье [Доставка ресурсов-контейнеров с помощью пакета SDK служб мультимедиа для .NET](media-services-deliver-streaming-content.md).

	// List all jobs on the server, and for each job, also list 
	// all tasks, all input assets, all output assets.

	static void ListJobsAndAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IJob job in _context.Jobs)
	    {
	        // Display the collection of jobs on the server.
	        builder.AppendLine("");
	        builder.AppendLine("******JOB*******");
	        builder.AppendLine("Job ID: " + job.Id);
	        builder.AppendLine("Name: " + job.Name);
	        builder.AppendLine("State: " + job.State);
	        builder.AppendLine("Order: " + job.Priority);
	        builder.AppendLine("==============");
	
	
	        // For each job, display the associated tasks (a job  
	        // has one or more tasks). 
	        builder.AppendLine("******TASKS*******");
	        foreach (ITask task in job.Tasks)
	        {
	            builder.AppendLine("Task Id: " + task.Id);
	            builder.AppendLine("Name: " + task.Name);
	            builder.AppendLine("Progress: " + task.Progress);
	            builder.AppendLine("Configuration: " + task.Configuration);
	            if (task.ErrorDetails != null)
	            {
	                builder.AppendLine("Error: " + task.ErrorDetails);
	            }
	            builder.AppendLine("==============");
	        }
	
	        // For each job, display the list of input media assets.
	        builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
	        foreach (IAsset inputAsset in job.InputMediaAssets)
	        {
	
	            if (inputAsset != null)
	            {
	                builder.AppendLine("Input Asset Id: " + inputAsset.Id);
	                builder.AppendLine("Name: " + inputAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	        // For each job, display the list of output media assets.
	        builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
	        foreach (IAsset theAsset in job.OutputMediaAssets)
	        {
	            if (theAsset != null)
	            {
	                builder.AppendLine("Output Asset Id: " + theAsset.Id);
	                builder.AppendLine("Name: " + theAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Перечисление всех политик доступа

В службах мультимедиа можно определить политику доступа для актива или его файлов. Политика доступа определяет разрешения для файла или актива (тип доступа и продолжительность). В коде служб мультимедиа политика доступа обычно определяется путем создания объекта IAccessPolicy и последующей его привязки к существующему активу. Затем создается объект ILocator, который позволяет предоставлять прямой доступ к активам в службах мультимедиа. Проект Visual Studio, прилагаемый к этой документации, содержит несколько примеров кода, которые показывают, как создавать и назначать политики доступа и указатели на активы.

В следующем примере кода показано, как перечислить все политики доступа на сервере, а также показаны связанные с каждой из них типы разрешений. Другой полезный способ просмотра политик доступа — получить список всех объектов ILocator на сервере, а затем для каждого указателя можно перечислить связанную политику доступа с помощью его свойства AccessPolicy.

	static void ListAllPolicies()
	{
	    foreach (IAccessPolicy policy in _context.AccessPolicies)
	    {
	        Console.WriteLine("");
	        Console.WriteLine("Name:  " + policy.Name);
	        Console.WriteLine("ID:  " + policy.Id);
	        Console.WriteLine("Permissions: " + policy.Permissions);
	        Console.WriteLine("==============");
	
	    }
	}

##Перечисление всех указателей

Указатель — это URL-адрес, предоставляющий прямой путь для доступа к активу, а также разрешения для этого актива, определенные связанной с указателем политикой доступа. Каждый актив может содержать коллекцию объектов ILocator, связанную с ним через его свойство Locators. Контекст сервера также содержит коллекцию Locators, содержащую все указатели.

В следующем примере кода перечисляются все указатели на сервере. Для каждого указателя отображается идентификатор (Id) связанного актива и политики доступа. Также отображаются тип разрешений, дата окончания срока действия и полный путь к активу.

Обратите внимание, что путь указателя к активу представляет собой только базовый URL-адрес актива. Чтобы создать прямой путь к отдельным файлам, которые может просматривать пользователь или приложения, в коде необходимо добавить к пути указателя конкретный файловый путь. Сведения о том, как это сделать, см. в статье [Доставка ресурсов-контейнеров с помощью пакета SDK служб мультимедиа для .NET](media-services-deliver-streaming-content.md).

	static void ListAllLocators()
	{
	    foreach (ILocator locator in _context.Locators)
	    {
	        Console.WriteLine("***********");
	        Console.WriteLine("Locator Id: " + locator.Id);
	        Console.WriteLine("Locator asset Id: " + locator.AssetId);
	        Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
	        Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
	        Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
	        // The locator path is the base or parent path (with included permissions) to access  
	        // the media content of an asset. To create a full URL to a specific media file, take 
	        // the locator path and then append a file name and info as needed.  
	        Console.WriteLine("Locator base path: " + locator.Path);
	        Console.WriteLine("");
	    }
	}

## Перечисление больших коллекций сущностей

При запросе сущностей существует ограничение в 1000 сущностей, возвращаемых за один раз, так как в открытой версии 2 REST количество результатов запросов ограничено 1000. При перечислении больших коллекций сущностей необходимо использовать предложения "Пропустить" и "Принять".
	
Следующая функция перебирает все задания для предоставленной учетной записи служб мультимедиа. Службы мультимедиа возвращают 1000 заданий в коллекции заданий. Эта функция использует предложения "Пропустить" и "Принять", чтобы убедиться в том, что все задания перечислены (при наличии более 1000 заданий в учетной записи).
	
	static void ProcessJobs()
	{
	    try
	    {
	
	        int skipSize = 0;
	        int batchSize = 1000;
	        int currentBatch = 0;
	
	        while (true)
	        {
	            // Loop through all Jobs (1000 at a time) in the Media Services account
	            IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
	            foreach (IJob job in _jobsCollectionQuery)
	            {
	                currentBatch++;
	                Console.WriteLine("Processing Job Id:" + job.Id);
	            }
	
	            if (currentBatch == batchSize)
	            {
	                skipSize += batchSize;
	                currentBatch = 0;
	            }
	            else
	            {
	                break;
	            }
	        }
	    }
	    catch (Exception ex)
	    {
	        Console.WriteLine(ex.Message);
	    }
	}

##Удаление актива

В следующем примере удаляется актив.

	static void DeleteAsset( IAsset asset)
	{
	    // delete the asset
	    asset.Delete();
	
	    // Verify asset deletion
	    if (GetAsset(asset.Id) == null)
	        Console.WriteLine("Deleted the Asset");
	
	}

##Удаление задания

Чтобы удалить задание, необходимо проверить состояние задания, как указано в свойстве State. Можно удалить задания, которые завершены или отменены, в то время как задания, которые находятся в некоторых других состояниях (помещенные в очередь, запланированные или обрабатываемые), необходимо сначала отменить, а затем их можно будет удалить. В следующем примере кода показан метод удаления задания, проверяющий состояния заданий и удаляющий их при завершенном или отмененном состоянии. Этот код зависит от содержащегося выше раздела данной статьи "Получение ссылки на задание".

	static void DeleteJob(string jobId)
	{
	    bool jobDeleted = false;
	
	    while (!jobDeleted)
	    {
	        // Get an updated job reference.  
	        IJob job = GetJob(jobId);
	
	        // Check and handle various possible job states. You can 
	        // only delete a job whose state is Finished, Error, or Canceled.   
	        // You can cancel jobs that are Queued, Scheduled, or Processing,  
	        // and then delete after they are canceled.
	        switch (job.State)
	        {
	            case JobState.Finished:
	            case JobState.Canceled:
	            case JobState.Error:
	                // Job errors should already be logged by polling or event 
	                // handling methods such as CheckJobProgress or StateChanged.
	                // You can also call job.DeleteAsync to do async deletes.
	                job.Delete();
	                Console.WriteLine("Job has been deleted.");
	                jobDeleted = true;
	                break;
	            case JobState.Canceling:
	                Console.WriteLine("Job is cancelling and will be deleted "
	                    + "when finished.");
	                Console.WriteLine("Wait while job finishes canceling...");
	                Thread.Sleep(5000);
	                break;
	            case JobState.Queued:
	            case JobState.Scheduled:
	            case JobState.Processing:
	                job.Cancel();
	                Console.WriteLine("Job is scheduled or processing and will "
	                    + "be deleted.");
	                break;
	            default:
	                break;
	        }
	
	    }
	}


##Удаление политики доступа

В следующем примере кода показано, как получить ссылку на политику доступа на основе идентификатора (Id) политики, а затем удалить эту политику.

	static void DeleteAccessPolicy(string existingPolicyId)
	{
	    // To delete a specific access policy, get a reference to the policy.  
	    // based on the policy Id passed to the method.
	    var policyInstance =
	            from p in _context.AccessPolicies
	            where p.Id == existingPolicyId
	            select p;
	    IAccessPolicy policy = policyInstance.FirstOrDefault();
	
	    policy.Delete();
	
	}
	


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0504_2016-->