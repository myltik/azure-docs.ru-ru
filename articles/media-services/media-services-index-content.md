<properties
	pageTitle="Индексирование файлов мультимедиа с помощью индексатора мультимедийных данных Azure"
	description="Индексатор мультимедийных данных Azure позволяет искать содержимое файлов мультимедиа и создавать полнотекстовую транскрипцию для скрытых субтитров и ключевых слов. В этом разделе показано, как использовать индексатор мультимедийных данных."
	services="media-services"
	documentationCenter=""
	authors="Juliako,Asolanki,johndeu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/15/2015"   
	ms.author="juliako"/>


# Индексирование файлов мультимедиа с помощью индексатора мультимедийных данных Azure

> [AZURE.SELECTOR]
- [Portal](media-services-manage-content.md#index)
- [.NET](media-services-index-content.md)


Индексатор мультимедийных данных Azure позволяет искать содержимое файлов мультимедиа и создавать полнотекстовую транскрипцию для скрытых субтитров и ключевых слов. Вы можете обработать один файл мультимедиа или несколько файлов мультимедиа в пакете.

>[AZURE.IMPORTANT]При индексировании содержимого обязательно используйте файлы мультимедиа, содержащие отчетливую речь (без фоновой музыки, шума, эффектов или шипения микрофона). Примерами подходящего содержимого могут служить записи собраний, лекций или презентаций. Для индексирования, как правило, не подходит такое содержимое, как фильмы, телепередачи, любые материалы со смешанными аудио- и звуковыми эффектами, записи плохого качества с фоновым шумом (шипением).


Задание индексирования может создавать следующие выходные данные:

- Файлы скрытых субтитров в следующих форматах: **SAMI**, **TTML** и **WebVTT**.

	Файлы скрытых субтитров содержат тег Recognizability, который используется для оценки задания индексирования в зависимости от степени разборчивости речи в исходном видео. Значение Recognizability можно использовать для отбора пригодных для использования выходных файлов. Низкая оценка означает, что результаты индексирования не точны из-за качества звука.
- Файл с ключевыми словами (XML).
- Файл большого двоичного объекта аудиоиндексации (AIB) для использования с SQL Server.

	Дополнительные сведения см. в разделе [Использование файлов AIB с индексатором мультимедийных данных Azure и SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


В этом разделе показано, как создать задания для **индексирования актива** и **индексирования нескольких файлов**.

Последние новости об индексаторе мультимедийных данных см. в [блогах служб мультимедиа](#preset).

## Использование файлов конфигурации и манифеста для задач индексирования

С помощью конфигурации задачи можно задать дополнительные параметры задач индексирования. Например, можно указать, какие метаданные будут использоваться для файла мультимедиа. Эти метаданные используются для пополнения словаря обработчика языка и существенно повышают точность распознавания речи. Вы также можете указать нужные выходные файлы.

С помощью файла манифеста можно также обработать несколько файлов мультимедиа одновременно.

Дополнительные сведения см. в разделе [Предустановка задачи для индексатора мультимедийных данных Azure](#).

## Индексирование ресурса

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
<!-- __ -->
### <a id="output_files"></a>Выходные файлы

По умолчанию задание индексирования создает следующие выходные файлы. Файлы будут храниться в первом выходном ресурсе.

При наличии нескольких входных файлов мультимедиа индексатор создаст файл манифеста для выходных данных задания с именем JobResult.txt. Для каждого входного файла будут созданы мультимедийные файлы AIB-, SAMI-, TTML- или WebVTT и файлы ключевых слов, которые будут последовательно пронумерованы и получат названия в соответствии с параметром «Alias».

Имя файла | Описание
----------|------------
__InputFileName.aib__ | Файл большого двоичного объекта для индексирования аудиозаписей. <br /><br />Файл большого двоичного объекта для индексирования аудиозаписей (AIB) — это двоичный файл, по которому можно выполнять полнотекстовый поиск на сервере Microsoft SQL Server. Файл AIB эффективнее, чем простой файл субтитров, так как он содержит альтернативные варианты для каждого слова, открывая гораздо более широкие возможности для поиска. <br/><br/>Для его использования необходимо установить надстройку Indexer SQL на компьютере, где работает сервер Microsoft SQL Server 2008 или более поздней версии. Поиск по AIB с помощью полнотекстового поиска Microsoft SQL Server обеспечивает более точные результаты, чем поиск по файлам скрытых субтитров, созданных WAMI. Это связано с тем, что AIB содержит альтернативные варианты слов с аналогичным звучанием, а файлы скрытых субтитров содержат наиболее достоверное слово для каждого сегмента звукового файла. Если поиск в речи критически важен, рекомендуется использовать AIB в сочетании с Microsoft SQL Server.<br/><br/> Чтобы загрузить надстройку, щелкните ссылку <a href="http://aka.ms/indexersql">Надстройка SQL «Индексатор мультимедийных данных Azure»</a>. <br/><br/>Кроме того, можно просто проиндексировать видео на основе скрытых субтитров и XML-файлов ключевых слов с помощью других поисковых систем, например Apache Lucene/Solr, но результаты поиска в этом случае будут менее точными.
__InputFileName.smi__<br />\_\_InputFileName.ttml\_\_<br />\_\_InputFileName.vtt\_\_ |Файлы скрытых субтитров (CC) в форматах SAMI, TTML и WebVTT.<br/><br/>Они позволяют сделать аудио и видеофайлы доступными для людей с нарушениями слуха.<br/><br/>Файлы скрытых субтитров имеют тег <b>Recognizability</b>, в который заносится оценка индексирования по качеству распознавания речи в исходном видео. Значение <b>Recognizability</b> позволяет оценить пригодность выходных файлов к использованию. Низкая оценка означает, что результаты индексирования не точны из-за качества звука.
__InputFileName.kw.xml<br />InputFileName.info__ |Файлы ключевых слов и сведений. <br/><br/>Файл ключевых слов — это XML-файл, который содержит ключевые слова, извлеченные из распознанной речи, и информацию об их частоте и расположении в файле. <br/><br/>Файл сведений — это обычный текстовый файл, который содержит подробные сведения о каждом распознанном термине. Первая строка является особой и содержит коэффициент узнаваемости. Каждая следующая строка представляет собой следующие данные с разделителями табуляцией: начальное время, конечное время, слово или фраза, достоверность. Значения времени указаны в секундах, а значение достоверности принадлежит диапазону от 0 до 1. <br/><br/>Пример строки файла: «1.20 1.45 слово 0.67»<br/><br/>Этот файл можно использовать для различных целей, например для анализа речи, улучшения обнаружения файлов мультимедиа поисковыми системами Bing, Google, Microsoft SharePoint и др. или для отображения более релевантной рекламы.
__JobResult.txt__ |Выходной манифест, который создается только при индексировании нескольких файлов. Он содержит следующую информацию:<br/><br/><table border="1"><tr><th>Входной файл</th><th>Псевдоним</th><th>Длинафайла мультимедиа</th><th>Ошибка</th></tr><tr><td>a.mp4</td><td>Media\_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media\_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media\_3</td><td>600</td><td>0</td></tr></table><br/>



Если не все входные файлы мультимедиа проиндексированы успешно, задание индексирования завершится ошибкой с кодом 4000. Дополнительные сведения см. в разделе [Коды ошибок](#error_codes).

## Индексация нескольких файлов

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

### Частично успешно выполненное задание

Если не все входные файлы мультимедиа проиндексированы успешно, задание индексирования завершится ошибкой с кодом 4000. Дополнительные сведения см. в разделе [Коды ошибок](#error_codes).


При этом создаются те же выходные данные (как и в случае успешно выполненного задания). Чтобы узнать, для каких входных файлов произошел сбой, можно просмотреть выходной файл манифеста (значения в столбце Error). Если для входных файлов происходит сбой, результирующие AIB-, SAMI-, TTML-, WebVTT-файлы и файлы ключевых слов НЕ создаются.

### <a id="preset"></a> Предустановка задачи для индексатора мультимедийных данных Azure

Работу индексатора мультимедийных данных Azure можно настроить под свои нужды, приложив к заданию необязательный файл предустановки задачи. Далее описан формат этого конфигурационного XML-файла.

Имя | Обязательный параметр | Описание
----|----|---
__input__ | нет | Файлы ресурса, которые необходимо проиндексировать.</p><p>Индексатор мультимедийных данных Azure поддерживает следующие форматы файлов мультимедиа: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Имена файлов можно указать в атрибуте **name** или **list** элемента **input** (как показано ниже). Если не указать файл ресурса для индексации, выбирается первичный файл. Если не задан первичный файла ресурса, индексируется первый файл входного ресурса.</p><p>Чтобы явно указать имя файла ресурса, используйте запись следующего формата.<br />`<input name="TestFile.wmv">`<br /><br />Индексатор может обрабатывать несколько файлов ресурса сразу (до 10). Для этого выполните следующие действия.<br /><br /><ol class="ordered"><li><p>Создайте текстовый файл (файл манифеста) с расширением .lst. </p></li><li><p>Внесите в этот файл манифеста список всех файлов входного ресурса. </p></li><li><p>Добавьте (передайте) файл манифеста в ресурс. </p></li><li><p>Укажите имя файла манифеста в атрибуте list элемента input.<br />`<input list="input.lst">`</li></ol><br /><br />Примечание: если в файле манифеста будет указано более 10 файлов, задание индексирования завершится ошибкой с кодом 2006.
__metadata__ | нет | Метаданные для указанного файла ресурса, используемые для адаптации словаря. Это удобный способ подготовить индексатор к распознаванию слов, не входящих в стандартные словари, например имен собственных.<br />`<metadata key="..." value="..."/>` <br /><br />Вы можете указать значения __(value)__ для поддерживаемых ключей __(key)__. В настоящее время поддерживаются следующие ключи.<br /><br />title и description — позволяют адаптировать словарь к особенностям вашего задания и повысить точность распознавания речи. По значениям ключей выполняется поиск в интернете текстовых документов, соответствующих контексту. На время выполнения задания индексирования эти документы дополняют внутренний словарь индексатора.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__features__ <br /><br /> Добавлено в версии 1.2. В настоящее время поддерживается только одна функция: распознавание речи (ASR).| нет | Распознавание речи имеет следующие ключи настройки.<table><tr><th><p>Ключ</p></th> <th><p>Описание</p></th><th><p>Пример значения</p></th></tr><tr><td><p>Language</p></td><td><p>Естественный язык, который нужно распознавать в файле мультимедиа.</p></td><td><p>English, Spanish</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>Список предпочтительных форматов вывода (если таковые имеются), разделенных точкой с запятой</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Логический флаг. Если он установлен, создается файл AIB (для использования с SQL Server и клиентским Indexer IFilter). Дополнительные сведения собраны в статье <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Использование файлов AIB с индексатором мультимедийных данных Azure и сервером SQL Server</a>.</p></td><td><p>True (да); False (нет)</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Логический флаг. Если он установлен, создается XML-файл ключевых слов.</p></td><td><p>True (да); False (нет) </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Логический флаг. Если он установлен, всегда создаются полные подписи (независимо от уровня достоверности). </p><p>По умолчанию имеет значение false, в этом случае слова и фразы с уровнем достоверности меньше 50 % в создаваемых подписях заменяются на многоточие («…»). Многоточие позволяет контролировать качество текста.</p></td><td><p>True (да); False (нет) </p></td></tr></table>

### <a id="error_codes"></a>Коды ошибок

В случае ошибки индексатор мультимедийных данных Azure возвращает один из следующих кодов ошибки.

Код | Имя | Возможные причины
-----|------|------------------
2000 | Недопустимая конфигурация | Недопустимая конфигурация
2001 | Недопустимые входные ресурсы | Отсутствие входных ресурсов или пустой ресурс.
2002 | Недопустимый манифест | Манифест пуст или содержит недопустимые элементы.
2003 | Не удалось загрузить файл мультимедиа | Недопустимый URL-адрес в файле манифеста.
2004 | Неподдерживаемый протокол | Протокол URL-адреса мультимедиа не поддерживается.
2005 | Неподдерживаемый тип файла | Тип файла входного мультимедиа не поддерживается.
2006 | Слишком много входных файлов | Во входном манифесте указано более 10 файлов.
3000 | Не удалось расшифровать файл мультимедиа | Кодек мультимедиа не поддерживается <br/>или<br/> поврежден файл мультимедиа <br/>или<br/> нет аудиопотока во входном файле мультимедиа.
4000 | Индексирование пакета выполнено частично | Некоторые файлы входного мультимедиа не удалось проиндексировать. Дополнительные сведения см. в статье <a href="output_files">Выходные файлы</a>.
Прочие | Внутренние ошибки | Обратитесь в службу поддержки. indexer@microsoft.com


## <a id="supported_languages"></a>Поддерживаемые языки

В настоящее время поддерживаются английский и испанский языки. Подробнее этот вопрос раскрыт в [записи блога о выходе версии 1.2](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


## Схемы обучения работе со службами мультимедиа

Схемы обучения AMS можно просмотреть здесь:

- [Рабочий процесс для потоковой передачи в реальном времени в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Рабочий процесс для потоковой передачи по запросу в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


## Связанные ссылки

[Использование файлов AIB с индексатором мультимедийных данных Azure и SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=Nov15_HO2-->