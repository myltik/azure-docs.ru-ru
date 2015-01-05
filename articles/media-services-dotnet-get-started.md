<properties urlDisplayName="Get Started with Media Services" pageTitle="Приступая к работе со службами мультимедиа - Azure" metaKeywords="Azure media services" description="An introduction to using Media Services with Azure." metaCanonical="" services="media-services" documentationCenter="" title="Get started with Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





# <a name="getting-started"></a>Приступая к работе со службами мультимедиа

В этом учебнике показано, как начать разработку с помощью служб мультимедиа Azure. Здесь описываются базовый процесс работы со службами мультимедиа и наиболее распространенные объекты и задачи программирования, необходимые для разработки с использованием служб мультимедиа. По завершении работы с учебником вы сможете воспроизвести образец мультимедийного файла, который вы отправили, закодировали и загрузили. Вы также сможете перейти к закодированному активу и воспроизвести его на сервере. 

Проект Visual Studio на языке C#, который содержит код для этого примера, доступен здесь: [скачать](http://go.microsoft.com/fwlink/?linkid=253275).

В этом учебнике рассматриваются следующие основные действия:

* [Настройка проекта](#Step1)
* [Получение контекста сервера служб мультимедиа](#Step2)
* [Создание ресурса и отправка файлов, связанных с ресурсом, в службы мультимедиа](#Step3)
* [Кодировка ресурса и скачивание выходного ресурса](#Step4)

## Предварительные требования
Следующие компоненты необходимы для изучения пошагового руководства, а также для разработки с использованием пакета SDK служб мультимедиа Azure.

- Учетная запись служб мультимедиа в новой или существующей подписке Azure. Дополнительную информацию см. в разделе [Создание учетной записи служб мультимедиа](http://go.microsoft.com/fwlink/?LinkId=256662).
- Операционные системы: Windows 7, Windows 2008 R2, Windows 8 или более поздняя версия.
- .NET Framework 4.5 или .NET Framework 4.
- Visual Studio 2012, Visual Studio 2010 SP1 (Professional, Premium, Ultimate или Express) или более поздняя версия.
- Установите **пакет Azure SDK для .NET**, **пакет служб мультимедиа Azure SDK для .NET** и **службы данных WCF 5.0 для библиотек OData V3**, а также добавьте ссылки на проект с помощью пакета [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) . Ниже показано, как установить компоненты и добавить эти ссылки.

>[WACOM.NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительную информацию см. на сайте <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Бесплатное пробное использование Azure</a>.

<h2><a id="Step1"></a>Настройка проекта</h2>

1. Создайте консольное приложение C# в Visual Studio 2012 или Visual Studio 2010 SP1. Введите значения в полях **Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку "ОК". 

2. Добавьте ссылку на сборку System.Configuration.

	Чтобы добавить ссылку в диалоговом окне **Управление ссылками**, выполните следующее. В **обозревателе решений** щелкните правой кнопкой мыши 	узел **Ссылки** и выберите пункт **Добавить ссылку**. В диалоговом окне **Управление ссылками** выберите соответствующие сборки (в данном случае это System.Configuration.)

3. Если вы этого еще не сделали, добавьте ссылки на **пакет Azure SDK для .NET** (Microsoft.WindowsAzure.StorageClient.dll), **пакет SDK служб мультимедиа Azure для .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) и **службы данных WCF 5.0 для библиотек OData V3** (Microsoft.Data.OData.dll) с использованием пакета <a href="http://nuget.org/packages/windowsazure.mediaservices">windowsazure.mediaservices Nuget</a> . 

	Чтобы добавить ссылки, с помощью Nuget, выполните следующие действия. В главном меню Visual Studio выберите "Сервис -> Диспетчер пакетов библиотеки -> Консоль диспетчера пакетов". Затем в окне консоли введите <i>Install-Package [имя пакета]</i> и нажмите клавишу ВВОД (в этом случае используйте следующую команду: <i>Install-Package windowsazure.mediaservices</i>).

4. Добавьте в файл **app.config** раздел appSettings и задайте значения имени и ключа учетной записи служб мультимедиа Azure. Имя учетной записи служб мультимедиа и ключ учетной записи вы получили во время установки. Добавьте эти значения для каждого атрибута всех параметров в файле app.config в проекте Visual Studio.

	> [WACOM.NOTE]
	> В Visual Studio 2012 файл App.config добавляется по умолчанию. В Visual Studio 2010 вам необходимо вручную добавить файл конфигурации приложения.

	<pre><code>
	<configuration>
  	. . . 
  	<appSettings>
    	<add key="accountName" value="Add-Media-Services-Account-Name" />
    	<add key="accountKey" value="Add-Media-Services-Account-Key" />
  	</appSettings>
	</configuration>
	</code></pre>

5. Создайте новую папку на локальном компьютере с именем supportFiles (в данном примере папка supportFiles находится в каталоге проекта MediaServicesGettingStarted). Пример: <a href="http://go.microsoft.com/fwlink/?linkid=253275">используемый</a> в этом руководстве, содержит каталог supportFiles. Вы можете скопировать содержимое этого каталога в вашу папку supportFiles.

6. Замените существующие инструкции using в начале файла Program.cs на следующий код.

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


7. Добавьте следующие переменные пути уровня класса. Путь **_supportFiles** должен указывать на папку, созданную на предыдущем шаге. 

		// Base support files path.  Update this field to point to the base path  
		// for the local support files folder that you create. 
		private static readonly string _supportFiles =
		            Path.GetFullPath(@"../..\supportFiles");
		
		// Paths to support files (within the above base path). You can use 
		// the provided sample media files from the "supportFiles" folder, or 
		// provide paths to your own media files below to run these samples.
		private static readonly string _singleInputFilePath =
		    Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
		private static readonly string _outputFilesFolder =
		    Path.GetFullPath(_supportFiles + @"\outputfiles");
		
8. Добавьте следующие переменные уровня класса для получения параметров подключения и проверки подлинности.  Эти параметры будут извлечены из файла App.Config и требуются для подключения к службам мультимедиа, проверки подлинности и получения маркера для доступа к контексту сервера. Код в проекте ссылается на эти переменные для создания экземпляра контекста сервера.
	
		private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
		private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9. Добавьте следующую переменную уровня класса, которая используется как статическая ссылка на контекст сервера.

		// Field for service context.
		private static CloudMediaContext _context = null;
		
<h2><a id="Step2"></a>Получение контекста служб мультимедиа</h2>

Объект контекста служб мультимедиа содержит все основные объекты и коллекции для программирования служб мультимедиа. Этот контекст включает ссылки на важные коллекции, в том числе задания, ресурсы, файлы, политики доступа, локаторы и другие объекты. Для большинства задач программирования служб мультимедиа требуется получить контекст сервера.

В файле Program.cs добавьте следующий код в качестве первого элемента метода **Main**. Этот код использует имя учетной записи служб мультимедиа, имя и ключ учетной записи из файла app.config для создания экземпляра контекста сервера. Экземпляр назначается переменной **_context**, созданной на уровне класса.

	// Get the service context.
	_context = new CloudMediaContext(_accountName, _accountKey);
	
<h2><a id="Step3"></a>Создание актива и передача файла</h2>

Код в этом разделе выполняет следующие задачи. 

1. Создает пустой актив<br/>
При создании актива можно указать три различных параметра их шифрования. 

	- **AssetCreationOptions.None**: без шифрования. Если вы хотите создать незашифрованный актив, задайте этот параметр.
	- **AssetCreationOptions.CommonEncryptionProtected**: для файлов CENC. Примером служит набор файлов, уже зашифрованных PlayReady. 
	- **AssetCreationOptions.StorageEncrypted**: шифрование хранилища. Шифрование входного файла до его загрузки в хранилище Azure.

		<div class="dev-callout"> 
	<strong>Примечание.</strong> 
	<p>Службы мультимедиа обеспечивают шифрование на диске, а не по сети, как технология управления цифровыми правами (DRM).</p> 
	</div>

2. Создает экземпляр AssetFile, который нужно связать с активом.
3. Создает экземпляр AccessPolicy, определяющий разрешения и длительность доступа к активам.
4. Создает экземпляр локатора, который предоставляет доступ к активам.
5. Передает один файл мультимедиа в службы мультимедиа. Процесс создания и передачи также называется поглощением активов.

Добавьте в класс следующие методы.

<pre><code>
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(3),
                                                        AccessPermissions.Write | AccessPermissions.List);

    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    locator.Delete();
    accessPolicy.Delete();

    return asset;
}

</code></pre>

Добавьте вызов метода после строки **\_context = new CloudMediaContext(_accountName, _accountKey);** в методе Main. 

	IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

<h2><a id="Step4"></a>Кодирование ресурса на сервере и скачивание выходного ресурса</h2>

Службы мультимедиа могут создавать задания, обрабатывающие мультимедиа контент несколькими способами, такими как кодирование, шифрование, преобразование форматов и т. д. Задание служб мультимедиа всегда содержит одну или несколько задач, определяющих детали обработки. В этом разделе вы создадите базовую задачу кодировки и затем запустите задание, которое выполняет эту задачу с помощью кодировщика службы мультимедиа Windows Azure. Задача использует предварительно заданную строку для указания нужного типа кодировки. Доступные предустановленные значения кодировки см. в разделе [Строки предустановок задачи для кодировщика мультимедиа Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj129582.aspx) . Службы мультимедиа поддерживают такие же входные и выходные форматы файлов мультимедиа, как и Microsoft Expression Encoder. Список поддерживаемых форматов см. в разделе [Поддерживаемые типы файлов для служб мультимедиа](http://msdn.microsoft.com/ru-ru/library/windowsazure/hh973634.aspx).

<ol>
<li>
Добавьте в класс следующее определение метода <strong>CreateEncodingJob</strong>. Этот метод демонстрирует, как выполнить несколько задач для задания кодирования:
<ul>
<li>
Declare a new job.
</li>
<li>
Объявите мультимедийный процессор для обработки задания. Обработчик мультимедиа - это компонент, обрабатывающий кодирование, шифрование, преобразование форматов и другие связанные задания обработки. Существует несколько типов обработчиков мультимедиа (можно выполнить итерацию по всем из них, используя _context.MediaProcessors). Метод GetLatestMediaProcessorByName, показанный далее в этом руководстве, возвращает обработчик кодировщика мультимедиа Azure.
</li>
<li>
Объявите новое задание. Каждое задание содержит одну или несколько задач. Обратите внимание, что задаче передается понятное имя, экземпляр процессора мультимедиа, строка конфигурации задачи и параметры создания задачи. Строка конфигурации определяет параметры кодировки. В этом примере используется параметр <strong>H264 Broadband 720p</strong>. Эта предустановка позволяет создать один MP4-файл. Дополнительную информацию об этой и других предустановках см. в статье <a href="http://msdn.microsoft.com/library/windowsazure/jj129582.aspx">Строки предустановок задачи для кодировщика мультимедиа Azure</a>.
</li>
<li>
Добавьте входной актив в задачу. В этом примере входным активом является актив, созданный в предыдущем разделе.
</li>
<li>
Добавьте выходной актив в задачу. Укажите для него понятное имя, логическое значение, указывающее, следует ли сохранить выходные данные на сервере после выполнения задания, и <strong>AssetCreationOptions.None,</strong> указывающее, что выходные данные не шифруются для хранения и передачи. 
</li>
<li>
Отправка задания.<br/>
Отправка задания - это последний шаг, необходимый для выполнения задания кодирования.
</li>
</ul>
Этот метод также демонстрирует, как выполнить другие полезные (но необязательные) задачи, например отслеживание хода выполнения задачи и доступ к активу, создаваемому заданием кодировки.
<pre><code>
static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
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
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    // Use the following event handler to check job progress.  
    job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

    // Launch the job.
    job.Submit();

    // Optionally log job details. This displays basic job details
    // to the console and saves them to a JobDetails-{JobId}.txt file 
    // in your output folder.
    LogJobDetails(job.Id);

    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();

    // **********
    // Optional code.  Code after this point is not required for 
    // an encoding job, but shows how to access the assets that 
    // are the output of a job, either by creating URLs to the 
    // asset on the server, or by downloading. 
    // **********

    // Get an updated job reference.
    job = GetJob(job.Id);

    // If job state is Error the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
        return job;
    }

    // Get a reference to the output asset from the job.
    IAsset outputAsset = job.OutputMediaAssets[0];
    IAccessPolicy policy = null;
    ILocator locator = null;

    // Declare an access policy for permissions on the asset. 
    // You can call an async or sync create method. 
    policy =
        _context.AccessPolicies.Create("My 30 days readonly policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

    // Create a SAS locator to enable direct access to the asset 
    // in blob storage. You can call a sync or async create method.  
    // You can set the optional startTime param as 5 minutes 
    // earlier than Now to compensate for differences in time  
    // between the client and server clocks. 

    locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
        policy,
        DateTime.UtcNow.AddMinutes(-5));

    // Build a list of SAS URLs to each file in the asset. 
    List<String> sasUrlList = GetAssetSasUrlList(outputAsset, locator);

    // Write the URL list to a local file. You can use the saved 
    // SAS URLs to browse directly to the files in the asset.
    if (sasUrlList != null)
    {
        string outFilePath = Path.GetFullPath(outputFolder + @"\" + "FileSasUrlList.txt");
        StringBuilder fileList = new StringBuilder();
        foreach (string url in sasUrlList)
        {
            fileList.AppendLine(url);
            fileList.AppendLine();
        }
        WriteToFile(outFilePath, fileList.ToString());

        // Optionally download the output to the local machine.
        DownloadAssetToLocal(job.Id, outputFolder);
    }

    
    return job;
}

</code></pre>
</li>
<li>
Добавьте вызов метода <strong>CreateEncodingJob</strong> в метод <strong>Main</strong> после строк, которые вы добавили ранее.
<pre><code>
CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);
</code></pre>
</li>
<li>
Добавьте в класс следующие вспомогательные методы. Они необходимы для поддержки метода <strong>CreateEncodingJob</strong> . Ниже приведено описание этих вспомогательных методов.
<ul>
<li>
Пример: <strong>GetLatestMediaProcessorByName</strong> возвращает соответствующий обработчик мультимедиа для обработки кодировки, шифрования и других связанных задач обработки. Мультимедийный процессор можно создать, используя строковое имя процессора. В параметре mediaProcessor методу можно передать следующие строки: <strong>Azure Media Encoder</strong>, <strong>Microsoft Azure Media Packager</strong>, <strong>Microsoft Azure Media Encryptor</strong>, <strong>Расшифровка хранилища</strong>.
<pre><code>
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    // The possible strings that can be passed into the 
    // method for the mediaProcessor parameter:
    //   Azure Media Encoder
    //   Windows Azure Media Packager
    //   Windows Azure Media Encryptor
    //   Storage Decryption

    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

</code></pre>
</li>
<li>
При выполнении заданий часто требуется способ отслеживания хода выполнения задачи. Следующий пример кода определяет обработчик событий StateChanged. Этот обработчик отслеживает ход выполнения задачи и предоставляет обновленное состояние. Кроме того, код определяет метод LogJobStop. Этот вспомогательный метод заносит в журнал сведения об ошибках.

<pre><code>
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

private static void LogJobDetails(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nJob ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);

    // Write the output to a local file and to the console. The template 
    // for an error output file is:  JobDetails-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}
        
private static string JobIdAsFileName(string jobID)
{
    return jobID.Replace(":", "_");
}
</code></pre>
</li>
<li>
Метод WriteToFile записывает файл в указанную выходную папку.
<pre><code>
static void WriteToFile(string outFilePath, string fileContent)
{
    StreamWriter sr = File.CreateText(outFilePath);
    sr.Write(fileContent);
    sr.Close();
}

</code></pre>
</li>
<li>
После шифрования активов в службах мультимедиа вы можете получить доступ к активам, полученным в результате выполнения задания кодирования. В этом пошаговом руководстве показаны два способа доступа к результатам задания кодирования:
<ul>
<li>
Создание URL-адреса SAS для актива на сервере. 
</li>
<li>
Загрузка выходного актива с сервера.
</li>
</ul>
Метод GetAssetSasUrlList создает список URL-адресов SAS для всех файлов в активе. 
<pre><code>
static List<String> GetAssetSasUrlList(IAsset asset, ILocator locator)
{
    // Declare a list to contain all the SAS URLs.
    List<String> fileSasUrlList = new List<String>();

    // If the asset has files, build a list of URLs to 
    // each file in the asset and return. 
    foreach (IAssetFile file in asset.AssetFiles)
    {
        string sasUrl = BuildFileSasUrl(file, locator);
        fileSasUrlList.Add(sasUrl);
    }

    // Return the list of SAS URLs.
    return fileSasUrlList;
}

// Create and return a SAS URL to a single file in an asset. 
static string BuildFileSasUrl(IAssetFile file, ILocator locator)
{
    // Take the locator path, add the file name, and build 
    // a full SAS URL to access this file. This is the only 
    // code required to build the full URL.
    var uriBuilder = new UriBuilder(locator.Path);
    uriBuilder.Path += "/" + file.Name;

    // Optional:  print the locator.Path to the asset, and 
    // the full SAS URL to the file
    Console.WriteLine("Locator path: ");
    Console.WriteLine(locator.Path);
    Console.WriteLine();
    Console.WriteLine("Full URL to file: ");
    Console.WriteLine(uriBuilder.Uri.AbsoluteUri);
    Console.WriteLine();


    //Return the SAS URL.
    return uriBuilder.Uri.AbsoluteUri;
}

</code></pre>
</li>
<li>
Пример: <strong>DownloadAssetToLocal</strong> скачивает каждый файл в ресурсе в локальную папку. Поскольку в этом примере актив был создан с одним входным файлом мультимедиа, коллекция файлов выходных активов содержит два файла: кодированный файл мультимедиа (с расширением MP4) и XML-файл с метаданными об активе. Этот метод загружает оба файла.
<pre><code>
static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
{
    // This method illustrates how to download a single asset. 
    // However, you can iterate through the OutputAssets
    // collection, and download all assets if there are many. 

    // Get a reference to the job. 
    IJob job = GetJob(jobId);
    // Get a reference to the first output asset. If there were multiple 
    // output media assets you could iterate and handle each one.
    IAsset outputAsset = job.OutputMediaAssets[0];

    IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);
    BlobTransferClient blobTransfer = new BlobTransferClient
    {
        NumberOfConcurrentTransfers = 10,
        ParallelTransferThreadCount = 10
    };

    var downloadTasks = new List<Task>();
    foreach (IAssetFile outputFile in outputAsset.AssetFiles)
    {
        // Use the following event handler to check download progress.
        outputFile.DownloadProgressChanged += DownloadProgress;

        string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

        Console.WriteLine("File download path:  " + localDownloadPath);

        downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

        outputFile.DownloadProgressChanged -= DownloadProgress;
    }

    Task.WaitAll(downloadTasks.ToArray());

    return outputAsset;
}

static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
{
    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
}
</code></pre>
</li>
<li>
Вспомогательные методы GetJob и GetAsset запрашивают и возвращают ссылку на объект задания и объект актива с заданным идентификатором. Аналогичный тип запроса LINQ можно использовать для получения ссылок на другие объекты служб мультимедиа на сервере.
<pre><code>
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
</code></pre>
</li>
</ul>
</li>
</ol>

## Тестирование кода
Запустите программу (нажмите клавишу F5). Вывод консоли будет выглядеть примерно следующим образом:

<pre><code>
Asset name: UploadSingleFile_11/14/2012 10:09:11 PM
Time created: 11/14/2012 12:00:00 AM
Created assetFile interview2.wmv
Upload interview2.wmv
Done uploading of interview2.wmv using Upload()

Job ID: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
Job Name: My encoding job
Job submitted (client UTC time): 11/14/2012 10:09:39 PM
Media Services account name: Add-Media-Services-Account-Name
Media Services account location: Add-Media-Services-account-location-name

Job(My encoding job) state: Queued.
Please wait...

Job(My encoding job) state: Processing.
Please wait...

********************
Job(My encoding job) is finished.
Please wait while local tasks or downloads complete...
********************

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3
A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2F
BxERnav8Jb6hL7fxylq3oESc%3D

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&se=2012-1
1-14T23%3A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8F
od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

Downloads are in progress, please wait.

File download path:  C:\supportFiles\outputfiles\interview2.mp4
1.70952185308162 % download progress.
3.68508804454907 % download progress.
6.48870388360293 % download progress.
6.83808741232649 % download progress.
. . . 
99.0763740574049 % download progress.
99.1522674787341 % download progress.
100 % download progress.
File download path:  C:\supportFiles\outputfiles\interview2_metadata.xml
100 % download progress.

</code></pre>

1. В результате выполнения этого приложения происходит следующее:

2. WMV-файл передается в службы мультимедиа. 

3. Затем файл кодируется с использованием предустановки **H264 Broadband 720p** **кодировщика мультимедиа Azure**.

4. Файл FileSasUrlList.txt создается в папке \supportFiles\outputFiles. Этот файл содержит URL-адрес закодированного актива. 

	Чтобы воспроизвести файл мультимедиа, скопируйте URL-адрес актива из текстового файла и вставьте его в браузере. 

5. MP4-файл и файл _metadata.xml загружаются в папку outputFiles.

>[WACOM.NOTE]
> В объектной модели служб мультимедиа актив является объектом коллекции контента служб мультимедиа, который представляет отношение "один ко многим файлам". Путь локатора предоставляет URL-адрес BLOB-объекта Azure, т. е. базовый путь к этому активу в хранилище Windows Azure. Для доступа к конкретным файлам в активе добавьте имя файла в путь базового локатора.

<h2>Дальнейшие действия</h2>
В данном пошаговом руководстве была показана последовательность задач программирования для создания простого приложения служб мультимедиа. Вы изучили основные задачи программирования служб мультимедиа, включая получение контекста сервера, создание активов, кодирование активов, загрузка и получение активов на сервере. Дальнейшие действия и более сложные задачи разработки описываются в следующих статьях:

- <a href="http://azure.microsoft.com/ru-ru/develop/media-services/resources/">Использование служб мультимедиа</a>
- <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/hh973618.aspx">Создание приложений с помощью API REST служб мультимедиа</a>

<h2>Дополнительные ресурсы</h2>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Get your video online now! (Службы мультимедиа Azure 101. Передача видео в Интернет)</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Dynamic Packaging and Mobile Devices (Службы мультимедиа Azure 102. Динамическая упаковка и мобильные устройства)</a>

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps


<!--HONumber=35.1-->
