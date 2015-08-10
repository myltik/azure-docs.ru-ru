<properties 
	pageTitle="Индексирование файлов мультимедиа с помощью индексатора мультимедийных данных Azure" 
	description="Индексатор мультимедийных данных Azure позволяет искать содержимое файлов мультимедиа и создавать полнотекстовую транскрипцию для скрытых субтитров и ключевых слов. В этом разделе показано, как использовать индексатор мультимедийных данных." 
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


# Индексирование файлов мультимедиа с помощью индексатора мультимедийных данных Azure

> [AZURE.SELECTOR]
- [Portal](media-services-manage-content.md#index)
- [.NET](media-services-index-content.md)


Индексатор мультимедийных данных Azure позволяет искать содержимое файлов мультимедиа и создавать полнотекстовую транскрипцию для скрытых субтитров и ключевых слов. Вы можете обработать один файл мультимедиа или несколько файлов мультимедиа в пакете.

>[AZURE.NOTE]При индексировании содержимого обязательно используйте файлы мультимедиа, содержащие отчетливую речь (без фоновой музыки, шума, эффектов или шипения микрофона). Примерами подходящего содержимого могут служить записи собраний, лекций или презентаций. Для индексирования, как правило, не подходит такое содержимое, как фильмы, телепередачи, любые материалы со смешанными аудио- и звуковыми эффектами, записи плохого качества с фоновым шумом (шипением).


Задание индексирования создает четыре потока выходных данных для каждого файла индексирования:

- Файл субтитров в формате SAMI.
- Файл субтитров в формате Timed Text Markup Language (TTML).

	Файлы SAMI и TTML содержат тег Recognizability, который используется для оценки задания индексирования в зависимости от степени разборчивости речи в исходном видео. Значение Recognizability можно использовать для отбора пригодных для использования выходных файлов. Низкая оценка означает, что результаты индексирования не точны из-за качества звука.
- Файл с ключевыми словами (XML).
- Файл большого двоичного объекта аудиоиндексации (AIB) для использования с SQL Server.
	
	Дополнительные сведения см. в разделе [Использование файлов AIB с индексатором мультимедийных данных Azure и SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


В этом разделе показано, как создавать задания для **индексирования актива** и **индексирования нескольких файлов**.

Последние новости об индексаторе мультимедийных данных см. в [блогах служб мультимедиа](http://azure.microsoft.com/blog/topics/media-services/).

##Использование файлов конфигурации и манифеста для задач индексирования

С помощью конфигурации задачи можно задать дополнительные параметры задач индексирования. Например, можно указать, какие метаданные будут использоваться для файла мультимедиа. Эти метаданные используются для пополнения словаря обработчика языка и существенно повышают точность распознавания речи.

С помощью файла манифеста можно также обработать несколько файлов мультимедиа одновременно.

Дополнительные сведения см. в разделе [Предустановка задачи для индексатора мультимедийных данных Azure](https://msdn.microsoft.com/library/azure/dn783454.aspx).

##Индексирование ресурса

Следующий метод передает файл мультимедиа как ресурс и создает задание для индексирования ресурса.

Обратите внимание, что если файл конфигурации не указан, файл мультимедиа будет индексирован со всеми параметрами по умолчанию.
	
	static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
	{
	    // Create an asset and upload the input media file to storage.
	    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	        "My Indexing Input Asset",
	        AssetCreationOptions.None);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer",
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration from file if specified.
	    string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job. 
	    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
	
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
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
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
	
###<a id="output_files"></a>Выходные файлы

Задание индексирования создает следующие выходные файлы. Файлы будут храниться в первом выходном ресурсе.


<table border="1">
<tr><th>Имя файла</th><th>Описание</th></tr>
<tr><td>InputFileName.aib </td>
<td>Файл большого двоичного объекта для индексирования аудиозаписей.<br/><br/>
Файл большого двоичного объекта для индексирования аудиозаписей (AIB) — это двоичный файл, который можно найти на сервере Microsoft SQL Server с помощью полнотекстового поиска. Файл AIB эффективнее, чем простой файл субтитров, так как он содержит альтернативные варианты для каждого слова, открывая гораздо более широкие возможности для поиска.
<br/>
<br/>
Для его использования необходимо установить надстройку Indexer SQL на компьютере, где выполняется сервер Microsoft SQL Server 2008 или более поздней версии. Поиск по AIB с помощью полнотекстового поиска Microsoft SQL Server обеспечивает более точные результаты, чем поиск по файлам скрытых субтитров, созданных WAMI. Это связано с тем, что AIB содержит альтернативные варианты слов с аналогичным звучанием, а файлы скрытых субтитров содержат наиболее достоверное слово для каждого сегмента звукового файла. Если поиск в речи критически важен, рекомендуется использовать AIB в сочетании с Microsoft SQL Server.
<br/><br/>
Чтобы загрузить надстройку, щелкните ссылку <a href="http://aka.ms/indexersql">Надстройка SQL «Индексатор мультимедийных данных Azure»</a>.
<br/><br/>
Кроме того, можно использовать другие поисковые системы, например Apache Lucene/Solr, чтобы просто проиндексировать видео на основе скрытых субтитров и XML-файлов ключевых слов, но в этом случае результаты поиска будут менее точными.</td></tr>
<tr><td>InputFileName.smi<br/>InputFileName.ttml</td>
<td>Файлы скрытых субтитров в форматах SAMI и TTML.
<br/><br/>
С их помощью аудио- и видеофайлы можно сделать доступным для людей с нарушениями слуха.
<br/><br/>
Файлы SAMI и TTML содержат тег <b>Recognizability</b>, который используется для оценки задания индексирования в зависимости от степени разборчивости речи в исходном видео. Значение <b>Recognizability</b> можно использовать для отбора пригодных для использования выходных файлов. Низкая оценка означает, что результаты индексирования не точны из-за качества звука.</td></tr>
<tr><td>InputFileName.kw.xml</td>
<td>Файл ключевых слов.
<br/><br/>
Файл ключевых слов — это XML-файл, содержащий ключевые слова, извлеченные из речевого содержимого вместе с информацией о частоте и смещении.
<br/><br/>
Этот файл можно использовать для различных целей, например для анализа речи, улучшения обнаружения файлов мультимедиа такими поисковыми системами, как Bing, Google или Microsoft SharePoint, или повышения релевантности рекламы.</td></tr>
</table>

Если не все входные файлы мультимедиа проиндексированы успешно, задание индексирования завершится ошибкой с кодом 4000. Дополнительные сведения см. в разделе [Коды ошибок](#error_codes).

##Индексация нескольких файлов

Следующий метод передает несколько файлов мультимедиа в виде ресурса и создает задание для индексации этих файлов в пакете.

При этом создается файл манифеста с расширением LST, который затем отправляется в ресурс. Файл манифеста содержит список всех файлов ресурсов. Дополнительные сведения см. в разделе [Предустановка задачи для индексатора мультимедийных данных Azure](https://msdn.microsoft.com/library/azure/dn783454.aspx).
	
	static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
	{
	    // Create an asset and upload to storage.
	    IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
	        "My Indexing Input Asset - Batch Mode",
	        AssetCreationOptions.None);
	
	    // Create a manifest file that contains all the asset file names and upload to storage.
	    string manifestFile = "input.lst";            
	    File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
	    assetFile.Upload(manifestFile);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer";
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("batch.config");
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
	
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
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    foreach (string filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        assetFile.Upload(filePath);
	    }
	
	    return asset;
	}


###Выходные файлы

При наличии нескольких входных файлов мультимедиа WAMI создаст файл манифеста для выходных данных задания с именем JobResult.txt. Полученные для каждого входного файла мультимедиа файлы AIB, SAMI, TTML и файлы ключевых слов последовательно нумеруются, как показано ниже.

Описание выходных файлов см. в разделе [Выходные файлы](#output_files).


<table border="1">
<tr><th>Имя файла</th><th>Описание</th></tr>
<tr><td>JobResult.txt</td>
<td>Выходной манифест
<br/><br/>Ниже приведен формат выходного файла манифеста (JobResult.txt).
<br/><br/>

<table border="1">
<tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Ошибка</th></tr>
<tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr>
<tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr>
<tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr>
</table><br/>
Каждая строка соответствует одному входному файлу мультимедиа:
<br/><br/>
InputFile — имя файла ресурса или URL-адрес входного файла мультимедиа.
<br/><br/>
Alias — имя соответствующего выходного файла.
<br/><br/>
MediaLength — продолжительность входного файла мультимедиа в секундах. Может иметь значение 0, если для этого входного файла произошла ошибка.
<br/><br/>
Error — указывает, успешно ли проиндексирован данный файл мультимедиа. 0 — успешно, в противном случае — сбой. Конкретные ошибки см. в разделе <a href="#error_codes">Коды ошибок</a>.
</td></tr>
<tr><td>Media_1.aib </td>
<td>Файл #0 - файл большого двоичного объекта для индексирования аудиозаписей.</td></tr>
<tr><td>Media_1.smi<br/>Media_1.ttml</td>
<td>Файл #0 — файлы скрытых субтитров в форматах SAMI и TTML.</td></tr>
<tr><td>Media_1.kw.xml</td>
<td>Файл #0 — файл ключевых слов.</td></tr>
<tr><td>Media_2.aib </td>
<td>Файл #1&#160;— файл большого двоичного объекта для индексирования аудиозаписей.</td></tr>
</table>

Если не все входные файлы мультимедиа проиндексированы успешно, задание индексирования завершится ошибкой с кодом 4000. Дополнительные сведения см. в разделе [Коды ошибок](#error_codes).

###Частично успешно выполненное задание

Если не все входные файлы мультимедиа проиндексированы успешно, задание индексирования завершится ошибкой с кодом 4000. Дополнительные сведения см. в разделе [Коды ошибок](#error_codes).


При этом создаются те же выходные данные (как и в случае успешно выполненного задания). Чтобы узнать, для каких входных файлов произошел сбой, можно просмотреть выходной файл манифеста (значения в столбце Error). Если для входных файлов происходит сбой, результирующие файлы AIB, SAMI, TTML и файлы ключевых слов не создаются.


### <a id="error_codes"></a>Коды ошибок


<table border="1">
<tr><th>Код</th><th>Имя</th><th>Возможные причины</th></tr>
<tr><td>2000</td><td>Недопустимая конфигурация</td><td>Недопустимая конфигурация</td></tr>
<tr><td>2001</td><td>Недопустимые входные ресурсы</td><td>Отсутствие входных ресурсов или пустой ресурс.</td></tr>
<tr><td>2002</td><td>Недопустимый манифест</td><td>Манифест пуст или содержит недопустимые элементы.</td></tr>
<tr><td>2003</td><td>Не удалось загрузить файл мультимедиа</td><td>Недопустимый URL-адрес в файле манифеста.</td></tr>
<tr><td>2004</td><td>Неподдерживаемый протокол</td><td>Протокол URL-адреса мультимедиа не поддерживается.</td></tr>
<tr><td>2005</td><td>Неподдерживаемый тип файла</td><td>Тип файла входного мультимедиа не поддерживается.</td></tr>
<tr><td>2006</td><td>Слишком много входных файлов</td><td>Во входном манифесте указано более 10 файлов. </td></tr>
<tr><td>3000</td><td>Не удалось расшифровать файл мультимедиа</td>
<td>Кодек мультимедиа не поддерживается.
<br/>или<br/>
Файл мультимедиа поврежден.
<br/>или<br/>
Входной файл мультимедиа не содержит аудиопотока.</td></tr>
<tr><td>4000</td><td>Индексирование пакета выполнено частично</td><td>Некоторые файлы входного мультимедиа не удалось проиндексировать. Дополнительные сведения см. в разделе <a href="output_files">Выходные файлы</a>.</td></tr>
<tr><td>Прочие</td><td>Внутренние ошибки</td><td>Обратитесь в службу поддержки.</td></tr>
</table>


##<a id="supported_languages"></a>Поддерживаемые языки

В настоящее время поддерживаются английский и испанский языки. Дополнительные сведения см. в разделе [Индексатор мультимедийных данных (на испанском языке)](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

##Связанные ссылки

[Использование файлов AIB с индексатором мультимедийных данных Azure и SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=July15_HO5-->