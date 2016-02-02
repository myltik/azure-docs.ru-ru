<properties
	pageTitle="Приступая к работе с доставкой содержимого по запросу с помощью пакета SDK для .NET"
	description="В этом учебнике показаны шаги по реализации приложения доставки содержимого по запросу с помощью служб мультимедиа Azure и .NET."
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
	ms.topic="hero-article"
	ms.date="01/18/2016"
	ms.author="juliako"/>


# Приступая к работе с доставкой содержимого по запросу с помощью пакета SDK для .NET

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
 
##Обзор 

В этом учебнике содержатся шаги по реализации приложения доставки видео по запросу с помощью пакета SDK служб мультимедиа Azure для .NET.


В учебнике описывается базовый рабочий процесс служба мультимедиа и наиболее распространенные объекты и задачи программирования, необходимые для разработки с использованием служб мультимедиа. По завершении работы с учебником вы сможете выполнить потоковую передачу и поэтапно скачать пример файла мультимедиа, который вы отправили, закодировали и скачали.

## Что вы узнаете

В этом руководстве показано, как выполнять следующие задачи:

1.  Создание учетной записи служб мультимедиа (с помощью классического портала Azure).
2.  Настройка конечной точки потоковой передачи (с помощью портала).
3.  Создание и настройка проекта Visual Studio.
5.  Подключение к учетной записи служб мультимедиа.
6.  Создание нового актива и отправка видеофайла.
7.  Кодирование исходного файла в набор MP4-файлов с адаптивным битрейтом.
8.  Публикация ресурса-контейнера и получение URL-адресов для потоковой передачи и поэтапного скачивания.
9.  Проверка посредством воспроизведения содержимого.

## Предварительные требования

Ниже перечислены необходимые условия для выполнения действий, описанных в этом учебнике.

- Для работы с этим учебником требуется учетная запись Azure. 
	
	Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Вы получаете кредиты, которые можно использовать, чтобы попробовать платные службы Azure. Даже после того как кредиты использованы, учетная запись остается за вами и вы можете использовать бесплатные службы и функции Azure, например компонент веб-приложений в службы приложений Azure.
- Операционные системы: Windows 8 или более поздней версии, Windows 2008 R2, Windows 7.
- .NET Framework 4.0 или более поздняя версия.
- Visual Studio 2010 с пакетом обновления 1 (SP1) (Professional, Premium, Ultimate или Express) или более поздняя версия.


##Скачивание образца

Скачать и запустить пример можно [здесь](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##Создание учетной записи служб мультимедиа с помощью портала

1. На классическом портале Azure последовательно выберите **Создать**, **Служба мультимедиа**, **Быстрое создание**.

![Быстрое создание служб мультимедиа](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. В поле **ИМЯ** введите имя новой учетной записи. Имя учетной записи служб мультимедиа может состоять из букв или цифр в нижнем регистре без пробелов и имеет длину 3 – 24 знака.

3. В поле **РЕГИОН** выберите географический регион, который будет использоваться для хранения записей метаданных вашей учетной записи служб мультимедиа. В раскрывающемся списке отображаются только доступные области служб мультимедиа.

4. В поле **УЧЕТНАЯ ЗАПИСЬ ХРАНЕНИЯ** выберите учетную запись хранения, которая обеспечит хранилище BLOB-объектов для мультимедийного контента из учетной записи служб мультимедиа. Можно выбрать существующую учетную запись хранения в географическом регионе, где находится учетная запись служб носителей, или создать новую учетную запись хранения. Новая учетная запись хранения будет создана в том же регионе.

5. Если была создана новая учетная запись хранения, в поле **ИМЯ НОВОЙ УЧЕТНОЙ ЗАПИСИ ХРАНЕНИЯ** введите имя для учетной записи хранения. В отношении учетных записей хранения действуют те же правила, что и для учетных записей служб носителей.

6. Щелкните **Быстрое создание** в нижней части формы.

Состояние процесса можно контролировать с помощью области сообщений в нижней части окна.

После успешного создания учетной записи состояние изменится на **Активно**.

В нижней части страницы появится кнопка **УПРАВЛЕНИЕ КЛЮЧАМИ**. При нажатии этой кнопки отобразится диалоговое окно с именем учетной записи служб мультимедиа, а также первичным и вторичным ключами. Для программного доступа к учетной записи служб мультимедиа потребуется имя учетной записи и сведения о первичном ключе.

![Страница служб мультимедиа](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

Если дважды щелкнуть имя учетной записи, по умолчанию откроется страница **Быстрый старт**. Эта страница позволяет выполнять некоторые задачи управления, которые также доступны на других страницах портала. Например, видеофайл можно загрузить как с этой страницы, так и со страницы содержимого.

##Настройка конечной точки потоковой передачи с помощью портала

При работе со службами мультимедиа Azure один из самых частых сценариев — доставка клиентам потоковой передачи с адаптивной скоростью. С помощью потоковой передачи с адаптивным битрейтом клиент может переключаться на потоковую передачу с более высоким или более низким битрейтом, так как видео отображается на основе текущей пропускной способности сети, загрузки ЦП и других факторов. Службы мультимедиа поддерживают следующие технологии потоковой передачи с адаптивной скоростью: потоковая трансляция HTTP (HLS), Smooth Streaming, MPEG DASH и HDS (только для владельцев лицензий Adobe PrimeTime/Access).

Службы мультимедиа обеспечивают динамическую упаковку, которая позволяет доставлять закодированное содержимое MP4-файлов с адаптивным битрейтом или Smooth Streaming в форматах потоковой передачи с поддержкой служб мультимедиа (MPEG DASH, HLS, Smooth Streaming, HDS) без необходимости повторной упаковки в эти форматы потоковой передачи.

Чтобы воспользоваться преимуществом динамической упаковки, необходимо выполнить следующие действия:

- закодировать или перекодировать мезонинный (исходный) файл в набор файлов MP4 или Smooth Streaming с переменной скоростью (шаги кодирования показаны далее в этом руководстве);
- получить по крайней мере одну единицу потоковой передачи для **конечной точки потоковой передачи**, из которой вы планируете доставлять содержимое.

Благодаря динамической упаковке вы оплачиваете хранение файлов только в одном формате, так как службы мультимедиа автоматически подготавливают и передают содержимое в нужном формате, исходя из полученных от клиента запросов.

Чтобы изменить число зарезервированных единиц потоковой передачи, выполните следующие действия:

1. На [портале](https://manage.windowsazure.com/) щелкните **Службы мультимедиа**. Затем щелкните имя службы мультимедиа.

2. Откройте страницу КОНЕЧНЫЕ ТОЧКИ ПОТОКОВОЙ ПЕРЕДАЧИ. Затем щелкните конечную точку, которую требуется изменить.

3. Чтобы указать число единиц потоковой передачи, перейдите на вкладку «Масштаб» и переместите ползунок **Зарезервированная емкость**.

	![Страница "Масштаб"](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.

Выделение новых единиц потоковой передачи занимает около 20 минут.

>[AZURE.NOTE] В настоящее время переход от любого положительного значения единиц потоковой передачи к нулевому может привести к отключению потоковой передачи на период до одного часа.
>
> Для расчета затрат используется наибольшее число единиц, указанных для 24-часового периода. Дополнительные сведения о ценах см. в разделе [Сведения о ценах на службы мультимедиа](http://go.microsoft.com/fwlink/?LinkId=275107).



##Создание и настройка проекта Visual Studio

1. Создайте новое консольное приложение C# в Visual Studio 2013, Visual Studio 2012 или Visual Studio 2010 с пакетом обновления 1 (SP1). Введите значения в поля **Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку **ОК**.

2. Используйте пакет Nuget [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions), чтобы установить **расширения пакета SDK служб мультимедиа Azure для .NET**. Расширения пакета SDK .NET служб мультимедиа — это набор методов расширения и вспомогательных функций, которые упростят код и разработку с помощью служб мультимедиа. При установке этого пакета также устанавливается **пакет SDK служб мультимедиа для .NET** и добавляются все остальные необходимые зависимости.

3. Добавьте ссылку на сборку System.Configuration. Эта сборка содержит класс **System.Configuration.ConfigurationManager**, используемый для доступа к файлам конфигурации (например, App.config).

4. Откройте файл App.config (добавьте файл в проект, если он не был добавлен по умолчанию) и добавьте в него раздел *appSettings*. Установите значения для имени и ключа учетной записи служб мультимедиа Azure, как показано в следующем примере. Чтобы получить имя учетной записи и сведения о ее ключе, откройте классический портал Azure, выберите учетную запись служб мультимедиа и нажмите кнопку **Управление ключами**.

<configuration>
		...
		  <appSettings>
		    <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
		    <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
		  </appSettings>
		  
		</configuration>

5. Замените существующие инструкции **using** в начале файла Program.cs на следующий код.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

6. Создайте новую папку в каталоге проектов и скопируйте MP4- или WMV-файл, для которого нужно выполнить кодирование, потоковую передачу или поэтапное скачивание. В этом примере используется путь C:\\VideoFiles.

##Подключение к учетной записи служб мультимедиа

Если вы используете службы мультимедиа с помощью .NET, то для большинства задач программирования этих служб следует использовать класс **CloudMediaContext**. Это могут быть такие задачи: подключение к учетной записи служб мультимедиа, создание, обновление и удаление различных объектов и получение доступа к этим объектам (ресурсам-контейнерам, файлам ресурсов-контейнеров, заданиям, политикам доступа, указателям и т. д.).

Перезапишите класс "Program" с помощью следующего кода. В коде показано, как считывать значения подключения из файла App.config и как создать объект **CloudMediaContext** для подключения к службам мультимедиа. Дополнительные сведения о подключении к службам мультимедиа см. в статье [Подключение к службам мультимедиа с помощью пакета SDK служб мультимедиа для .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Функция **Main** вызывает методы, которые будут определены далее в этом разделе.

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

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##Создание нового актива и отправка видеофайла

В службах мультимедиа цифровые файлы отправляются (или принимаются) в актив. Сущность **Asset** может содержать видео, аудио, изображения, коллекции эскизов, текстовые каналы и файлы скрытых субтитров (а также метаданные этих файлов). После отправки этих файлов содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи. Файлы в ресурсе называются **файлами ресурса**.

Метод **UploadFile**, определенный ниже, вызывает **CreateFromFile** (этот метод определен в расширениях пакета SDK для .NET). **CreateFromFile** создает новый ресурс-контейнер, в который отправляется указанный исходный файл.

Метод **CreateFromFile** получает параметры **AssetCreationOptions**, которые позволяют указать один из таких параметров создания ресурса-контейнера.

- **None** — шифрование не используется. Это значение по умолчанию. Обратите внимание, что при использовании этого параметра содержимое не защищено при передаче и хранении. Используйте этот параметр, если MP4-файл планируется доставить с помощью поэтапного скачивания.
- **StorageEncrypted**. Используйте этот параметр, чтобы локально зашифровать содержимое с помощью 256-битового шифрования AES, а затем отправить его в хранилище Azure для хранения в зашифрованном виде. Активы, защищенные с помощью шифрования хранилища, автоматически расшифровываются и помещаются в систему зашифрованных файлов до кодирования и при необходимости повторно кодируются до отправки в виде нового выходного актива. Шифрование хранилища чаще всего используется, если нужно защитить входные файлы мультимедиа высокого качества с помощью стойкого шифрования при хранении на диске.
- **CommonEncryptionProtected**. Используйте этот параметр при отправке содержимого, которое уже зашифровано и защищено с помощью стандартного шифрования или PlayReady DRM (например, Smooth Streaming с защитой PlayReady DRM).
- **EnvelopeEncryptionProtected**. Используйте этот параметр при отправке HLS с шифрованием AES. Обратите внимание, что файлы должны быть закодированы и зашифрованы с помощью Transform Manager.

Метод **CreateFromFile**, кроме того, позволяет указывать метод обратного вызова, чтобы сообщать о ходе отправки файла.

В следующем примере в качестве параметров ресурса-контейнера используется **None**.

Добавьте следующий метод в класс Program.

	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });

	    Console.WriteLine("Asset {0} created.", inputAsset.Id);

	    return inputAsset;
	}


##Кодирование исходного файла в набор MP4-файлов с адаптивным битрейтом

После приема ресурсов-контейнеров в службы мультимедиа файлы мультимедиа можно кодировать, менять их формат, добавлять к ним водяные знаки и т. д. до их доставки клиентам. Эти действия запланированы и выполняются в нескольких экземплярах фоновых ролей для обеспечения высокой производительности и доступности. Эти действия называются заданиями, и каждое задание состоит из атомарных задач, которые выполняют фактическую работу в файле ресурса-контейнера.

Как упоминалось ранее, один из наиболее распространенных сценариев при работе со службами мультимедиа Azure — доставка клиентам потоковой передачи с переменной скоростью. Службы мультимедиа могут динамически упаковывать набор MP4-файлов с адаптивным битрейтом в один из следующих форматов: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH и HDS (только для обладателей лицензии Adobe PrimeTime/Access).

Чтобы воспользоваться преимуществом динамической упаковки, необходимо выполнить следующие действия:

- закодировать или перекодировать мезонинный (исходный) файл в набор файлов MP4 или Smooth Streaming с переменной скоростью;  
- получить по крайней мере одну единицу потоковой передачи для конечной точки потоковой передачи, из которой вы планируете доставлять содержимое.

В следующем коде показано, как отправить задание кодирования. Задание содержит одну задачу, которая определяет перекодировку мезонинного файла в набор MP4-файлов с переменной скоростью с помощью **кодировщика мультимедиа Azure**. Код отправляет задание и ждет его выполнения.

После выполнения заданий вы сможете выполнить потоковую передачу актива и поэтапное скачивание MP4-файлов, созданных в результате перекодирования. Обратите внимание, что вам не нужно получать единицы потоковой передачи, чтобы поэтапно скачать MP4-файлы.

Добавьте следующий метод в класс Program.

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
	
	    // Prepare a job with a single task to transcode the specified asset
	    // into a multi-bitrate asset.
	
	    IJob job = _context.Jobs.CreateWithSingleTask(
	        "Media Encoder Standard",
	        "H264 Multiple Bitrate 720p",
	        asset,
	        "Adaptive Bitrate MP4",
	        options);
	
	    Console.WriteLine("Submitting transcoding job...");
	
	
	    // Submit the job and wait until it is completed.
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

##Публикация ресурса-контейнера и получение URL-адресов для потоковой передачи и поэтапного скачивания

Чтобы выполнить потоковую передачу ресурса-контейнера или скачать его, сначала необходимо "опубликовать" его, создав указатель. Указатели предоставляют доступ к файлам, содержащимся в активе. Службы мультимедиа поддерживают два типа указателей: указатели на OnDemandOrigin, использующиеся для потоковой передачи служб мультимедиа (например, MPEG DASH, HLS или Smooth Streaming), и указатели на подписанный URL-адрес, использующиеся для скачивания файлов мультимедиа.

После создания указателей можно создать URL-адреса, которые используются для потоковой передачи или скачивания файлов.


URL-адрес потоковой передачи для Smooth Streaming имеет следующий формат:

	 {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

URL-адрес потоковой передачи для HLS имеет следующий формат:

	 {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

URL-адрес потоковой передачи для MPEG DASH имеет следующий формат:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

URL-адрес SAS, используемый для скачивания файлов, имеет следующий формат:

	{blob container name}/{asset name}/{file name}/{SAS signature}

Расширения пакета SDK .NET служб мультимедиа обеспечивают удобные вспомогательные методы, которые возвращают отформатированные URL-адреса для опубликованного актива.

Следующий код использует расширения SDK .NET при создании указателей и получении URL-адресов для потоковой передачи и поэтапного скачивания. Код также показывает, как скачать файлы в локальную папку.

Добавьте следующий метод в класс Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
		// of encoding.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##Проверка посредством воспроизведения содержимого  

После запуска программы, определенной в предыдущем разделе, в окне консоли отобразятся URL-адреса, аналогичные следующим.

URL-адреса адаптивной потоковой передачи:

Smooth Streaming

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URL-адреса поэтапного скачивания (аудио и видео).

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Чтобы воспроизвести видео, используйте [Проигрыватель служб мультимедиа Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Чтобы протестировать поэтапное скачивание, вставьте URL-адрес в браузер (например, Internet Explorer, Chrome или Safari).


##Дальнейшие действия: схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### Не нашли ответа?

Если содержание этого раздела не соответствует вашим ожиданиям или не содержит ответов на ваши вопросы, оставьте отзыв в обсуждении Disqus ниже.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/

<!---HONumber=AcomDC_0128_2016-->