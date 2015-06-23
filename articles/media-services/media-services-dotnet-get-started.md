<properties 
	pageTitle="Начало работы с пакетом SDK служб мультимедиа .NET - Azure" 
	description="В этом учебнике показаны шаги по реализации приложения доставки видео по запросу с помощью служб мультимедиа Azure с использованием .NET." 
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
	ms.date="02/24/2015" 
	ms.author="juliako"/>


# Краткое руководство: Доставка видео по запросу с помощью пакета SDK служб мультимедиа для .NET 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Бесплатная пробная версия Azure</a>.

В этом учебнике содержатся шаги по реализации приложения доставки видео по запросу с помощью пакета SDK служб мультимедиа Azure для .NET. 


В учебнике описывается базовый рабочий процесс служба мультимедиа и наиболее распространенные объекты и задачи программирования, необходимые для разработки с использованием служб мультимедиа. По завершении работы с учебником вы сможете выполнить потоковую передачу и поэтапно скачать пример файла мультимедиа, который вы отправили, закодировали и скачали.  

## Предварительные требования
Для начала разработки с помощью пакета SDK служб мультимедиа для .NET необходимо выполнить следующие предварительные требования.

- Операционные системы: Windows 7, Windows 2008 R2, Windows 8 или более поздняя версия.
- .NET Framework 4.5 или .NET Framework 4.0
- Visual Studio 2013, Visual Studio 2012, Visual Studio 2010 с пакетом обновления 1 (SP1) (Professional, Premium, Ultimate или Express).


В этом кратком руководстве представлены следующие задачи.

1.  Создание учетной записи служб мультимедиа с помощью портала
2.  Настройка конечной точки потоковой передачи с помощью портала
3.  Создание и настройка проекта Visual Studio
5.  Подключение к учетной записи служб мультимедиа
1.  Создание нового актива и отправка видеофайла
1.  Кодирование исходного файла в набор MP4-файлов с адаптивным битрейтом
1.  Настройка политики доставки для закодированного актива
1.  Публикация актива и получение URL-адресов потоковой передачи и поэтапного скачивания  
1.  Воспроизведение содержимого 


##Создание учетной записи служб мультимедиа с помощью портала

1. На [портале управления][] нажмите кнопку **Создать**, выберите пункт **Служба мультимедиа** и щелкните **Быстрое создание**.
   
	![Службы мультимедиа - быстрое создание](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. В поле **ИМЯ** введите имя новой учетной записи. Имя учетной записи служб мультимедиа может состоять из букв или цифр в нижнем регистре без пробелов и иметь длину 3-24 знака. 

3. В поле **РЕГИОН** выберите географический регион, который будет использоваться для хранения записей метаданных для вашей учетной записи служб мультимедиа. В раскрывающемся списке отображаются только доступные области служб носителей. 

4. В поле **УЧЕТНАЯ ЗАПИСЬ ХРАНЕНИЯ** выберите учетную запись хранения, которая обеспечит хранилище больших двоичных объектов для содержимого мультимедиа из учетной записи служб мультимедиа. Можно выбрать существующую учетную запись хранения в географической области, где находится учетная запись служб носителей, или создать новую учетную запись хранения. Новая учетная запись хранения будет создана в той же области. 

5. Если была создана новая учетная запись хранения, в поле **ИМЯ НОВОЙ УЧЕТНОЙ ЗАПИСИ ХРАНЕНИЯ** введите имя для учетной записи хранения. В отношении учетных записей хранения действуют те же правила, что и для учетных записей служб носителей.

6. Щелкните **Быстрое создание** в нижней части формы.

	Состояние процесса можно контролировать с помощью области сообщений в нижней части окна.

	После успешного создания учетной записи состояние изменится на "Активно". 
	
	В нижней части страницы появится кнопка **УПРАВЛЕНИЕ КЛЮЧАМИ**. При нажатии этой кнопки отобразится диалоговое окно с именем учетной записи служб мультимедиа и первичный и вторичный ключи. Для программного доступа к учетной записи служб мультимедиа потребуется имя учетной записи и сведения о первичном ключе. 

	
	![Страница служб мультимедиа](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

	Если дважды щелкнуть имя учетной записи, по умолчанию откроется страница "Быстрый запуск". Эта страница позволяет выполнять некоторые задачи управления, которые также доступны на других страницах портала. Например, видеофайл можно загрузить как с этой страницы, так и со страницы КОНТЕНТ.

	 
##Настройка конечной точки потоковой передачи с помощью портала

При работе со службами мультимедиа Azure один из самых частых сценариев - доставка потоковой передачи с адаптивным битрейтом клиентам. С помощью потоковой передачи с адаптивным битрейтом клиент может переключаться на потоковую передачу с более высоким или более низким битрейтом, так как видео отображается на основе текущей пропускной способности сети, загрузки ЦП и других факторов. Службы мультимедиа поддерживают следующие технологии потоковой передачи с адаптивным битрейтом: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH и HDS (только для обладателей лицензии Adobe PrimeTime/Access). 

Службы мультимедиа обеспечивают динамическую упаковку, которая позволяет доставлять закодированное содержимое MP4-файлов с адаптивным битрейтом или Smooth Streaming в форматах потоковой передачи с поддержкой служб мультимедиа (MPEG DASH, HLS, Smooth Streaming, HDS) без необходимости повторной упаковки в эти форматы потоковой передачи. 

Чтобы воспользоваться преимуществом динамической упаковки, необходимо выполнить следующие действия:

- закодировать или перекодировать мезонинный(исходный) файл в набор MP4-файлов с адаптивным битрейтом или в набор файлов Smooth Streaming (шаги кодирования показаны далее в этом учебнике),  
- получить по крайней мере одну единицу потоковой передачи для конечной точки **потоковой передачи**, из которой планируется доставлять содержимое.

С динамической упаковкой потребуется хранить и оплачивать файлы только в одном формате хранения, а службы мультимедиа выполнят сборку и будут обслуживать соответствующий ответ на основе запросов клиента. 

Чтобы изменить число зарезервированных единиц потоковой передачи, выполните следующие действия.

1. На [портале управления](https://manage.windowsazure.com/) щелкните **Службы мультимедиа**. Затем щелкните имя службы мультимедиа.

2. Откройте страницу КОНЕЧНЫЕ ТОЧКИ ПОТОКОВОЙ ПЕРЕДАЧИ. Затем щелкните точку, которую требуется изменить.

3. Чтобы указать число единиц потоковой передачи, перейдите на вкладку "МАСШТАБ" и переместите ползунок **зарезервированной емкости**.

	![Страница "Масштаб"](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. Нажмите кнопку СОХРАНИТЬ, чтобы сохранить изменения.

	Выделение новых единиц занимает около 20 минут. 

	 
	>[AZURE.NOTE] Сейчас переход от любого положительного значения единиц потоковой передачи к нулевому может привести к отключению потоковой передачи на период до одного часа.
	>
	> Для расчета затрат используется наибольшее число единиц, указанных для 24-часового периода. Сведения о ценах см. в разделе [Сведения о ценах на службы мультимедиа](http://go.microsoft.com/fwlink/?LinkId=275107).



##Создание и настройка проекта Visual Studio

1. Создайте новое консольное приложение C# в Visual Studio 2013, Visual Studio 2012 или Visual Studio 2010 с пакетом обновления 1 (SP1). Введите значения в полях **Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку "ОК". 

2. Используйте пакет Nuget [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) для установки **расширений пакета SDK .NET служб мультимедиа Azure**.  Расширения пакета SDK .NET служб мультимедиа - это набор методов расширения и вспомогательных функций, которые упростят код и разработку с помощью служб мультимедиа. При установке этого пакета также устанавливается **пакет SDK .NET служб мультимедиа** и добавляются все остальные необходимые зависимости.
 
3. Добавьте ссылку на сборку System.Configuration. Эта сборка содержит класс System.Configuration.ConfigurationManager, используемый для доступа к файлам конфигурации (например, файлу App.config). 

4. Откройте файл App.config (добавьте файл в проект, если он не был добавлен по умолчанию) и добавьте в него раздел *appSettings*. Установите значения для имени и ключа учетной записи служб мультимедиа Azure, как показано в следующем примере. Чтобы получить имя и сведения о ключе учетной записи, откройте портал управления Azure, выберите учетную запись служб мультимедиа и нажмите кнопку **УПРАВЛЕНИЕ КЛЮЧАМИ**.


	<pre><code>
	&lt;configuration&gt;
        &lt;appSettings&gt;
    	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" /&gt;
        	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /&gt;
  	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>


5. Замените существующие инструкции using в начале файла Program.cs на следующий код.

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

6. Создайте новую папку в каталоге проектов и скопируйте MP4- или WMV-файл, для которого нужно выполнить кодирование, потоковую передачу или поэтапное скачивание. В этом примере используется путь "C:\VideoFiles". 

##Подключение к учетной записи служб мультимедиа

При использовании служб мультимедиа с .NET для большинства задач программирования служб мультимедиа необходимо использовать класс **CloudMediaContext**: подключение к учетной записи служб мультимедиа; создание, обновление, удаление и доступ к следующим объектам: активы, файлы активов, задания, политики доступа, указатели и т. д. 
 
Перезапишите класс "Program" с помощью следующего кода. Код показывает чтение значений подключения из файла App.config и создание объекта CloudMediaContext для подключения к службам мультимедиа. Дополнительные сведения о подключении к службам мультимедиа см. в разделе [Подключение к службам мультимедиа с помощью пакета SDK служб мультимедиа для .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

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
                // Создание и кэширование учетных данных служб мультимедиа в переменной статического класса.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Для создания используются кэшированные учетные данные CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Добавление вызовов в методы, определенные в этом разделе.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                ConfigureClearAssetDeliveryPolicy(encodedAsset);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Синтаксический анализ XML-сообщения об ошибке в ответе служб мультимедиа и создание нового 
                // исключения с содержимым.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##Создание нового актива и отправка видеофайла

В службах мультимедиа цифровые файлы отправляются (или принимаются) в актив. Сущность **Актив** может содержать видео, аудио, изображения, коллекции отпечатков, текстовые каналы и файлы скрытых субтитров(и метаданные этих файлов).  После отправки этих файлов содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи. Файлы в активе называются **файлы актива**.

Метод **UploadFile**, определенный ниже, вызывает **CreateFromFile** (определен в расширениях пакета SDK .NET). **CreateFromFile** создает новый актив, в который отправляется указанный исходный файл. 

Метод **CreateFromFile** получает параметры **AssetCreationOptions**, которые позволяют указать один из следующих параметров создания актива:
 
- **None** - шифрование не используется. Это значение по умолчанию. Обратите внимание, что при использовании этого параметра содержимое не защищено при передаче или в хранилище.
Используйте этот параметр, если MP4-файл планируется доставить с помощью поэтапного скачивания. 
- **StorageEncrypted** - шифрует незашифрованное содержимое локально с помощью AES-256-разрядного шифрования, а затем отправляет его в хранилище Azure, где оно хранится в зашифрованном виде. Активы, защищенные с помощью шифрования хранилища, автоматически расшифровываются и помещаются в систему зашифрованных файлов до кодирования и при необходимости повторно кодируются до отправки в виде нового выходного актива. Основная причина использования шифрования хранилища - если нужно защитить входные файлы мультимедиа высокого качества с помощью стойкого шифрования при хранении на диске.
- **CommonEncryption** - используйте этот параметр при отправке содержимого, которое уже зашифровано и защищено с помощью стандартного шифрования или PlayReady DRM (например, Smooth Streaming с защитой PlayReady DRM).
- **EnvelopeEncrypted** - используйте этот параметр при отправке HLS с шифрованием AES. Обратите внимание, что файлы должны быть закодированы и зашифрованы с помощью Transform Manager.

Метод **CreateFromFile** также позволяет указать метод обратного вызова, чтобы сообщать о ходе отправки файла.

В следующем примере в качестве параметров актива используется **None**.

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

После приема активов в службы мультимедиа файлы мультимедиа можно кодировать, менять их формат, добавлять водяные знаки и т. д. до их доставки клиентам. Эти действия запланированы и выполняются в нескольких экземплярах фоновых ролей для обеспечения высокой производительности и доступности. Эти действия называются заданиями, и каждое задание состоит из элементарных задач, которые выполняют фактическую работу в файле ресурса. 

Как было упомянуто ранее, при работе со службами мультимедиа Azure один из наиболее распространенных сценариев - доставка потоковой передачи с адаптивным битрейтом клиентам. Службы мультимедиа могут динамически упаковывать набор MP4-файлов с адаптивным битрейтом в один из следующих форматов: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH и HDS (только для обладателей лицензии Adobe PrimeTime/Access). 

Чтобы воспользоваться преимуществом динамической упаковки, необходимо выполнить следующие действия:

- закодировать или перекодировать мезонинный(исходный) файл в набор MP4-файлов с адаптивным битрейтом или в набор файлов Smooth Streaming,  
- получить по крайней мере одну единицу потоковой передачи для конечной точки потоковой передачи, из которой планируется доставлять содержимое. 

В следующем коде показано, как отправить задание кодирования. Задание содержит одну задачу, которая указывает необходимость перекодирования мезонинного файла в набор MP4-файлов с адаптивным битрейтом с помощью **Azure Media Encoder**. Код отправляет задание и ждет его выполнения. 

После выполнения заданий вы сможете выполнить потоковую передачу актива и поэтапное скачивание MP4-файлов, созданных в результате перекодирования.
Обратите внимание, что вам не нужно получать единицы потоковой передачи, чтобы поэтапно скачать MP4-файлы. 


Добавьте следующий метод в класс Program.

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
		// Подготовка задания с одной задачей для перекодирования указанного ресурса
        // into a multi-bitrate asset.

	    IJob job = _context.Jobs.CreateWithSingleTask(
	        MediaProcessorNames.AzureMediaEncoder,
	        MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
	        asset,
	        "Adaptive Bitrate MP4",
	        options);
	
		Console.WriteLine("Submitting transcoding job...");
	

	    // Отправка задания и ожидание его завершения.
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

##Настройка политики доставки для закодированного актива

Один из шагов в рабочем процессе доставки содержимого служб мультимедиа - настройка политик доставки активов. В настройку политики доставки активов входит следующее: протоколы, которые можно использовать для доставки актива (например, MPEG DASH, HLS, HDS, Smooth Streaming или все), следует ли использовать динамическое шифрование и как (конверт или общее шифрование). 

Следующий метод **ConfigureClearAssetDeliveryPolicy** указывает, что не следует применять динамическое шифрование и доставлять потоковую передачу в любом из следующих протоколов:  MPEG DASH, HLS и Smooth Streaming. 
  
Добавьте следующий метод в класс Program.

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }


##Публикация актива и получение URL-адресов потоковой передачи и поэтапного скачивания

Чтобы выполнить потоковую передачу или скачать актив, сначала нужно "опубликовать" его, создав указатель. Указатели предоставляют доступ к файлам, содержащимся в активе. Службы мультимедиа поддерживают два типа указателей: указатели на OnDemandOrigin, использующиеся для потоковой передачи служб мультимедиа (например, MPEG DASH, HLS или Smooth Streaming), и указатели на подписанный URL-адрес, использующиеся для скачивания файлов мультимедиа.

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
 
Следующий код использует расширения SDK .NET для создания указателей, получения URL-адресов потоковой передачи и поэтапного скачивания. Код также показывает, как скачать файлы в локальную папку.

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

        // Получение URL-адреса последовательной загрузки для каждого созданного MP4-файла
		// в результате кодирования.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Отображение URL-адресов потоковой передачи.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Отображение URL-адресов последовательной загрузки.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Загрузка выходного ресурса в локальную папку.
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

##Воспроизведение содержимого  

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


Для тестирования Smooth Streaming используйте [http://amsplayer.azurewebsites.net/](http://amsplayer.azurewebsites.net/) или [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor).

Для тестирования MPEG DASH используйте [http://dashif.org](http://dashif.org/reference/players/javascript/).

Для тестирования HLS используйте устройства iOS или Safari или [3ivx-hls-player](http://apps.microsoft.com/windows/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614). 


Для тестирования поэтапного скачивания вставьте URL-адрес в браузер (например, Internet Explorer, Chrome, Safari).


###Дополнительные ресурсы
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Get your video online now! (Службы мультимедиа Azure 101. Передача видео в Интернет)</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Dynamic Packaging and Mobile Devices (Службы мультимедиа Azure 102. Динамическая упаковка и мобильные устройства)</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Установщик веб-платформы]: http://go.microsoft.com/fwlink/?linkid=255386
  [портале управления]: http://manage.windowsazure.com/



<!--HONumber=52--> 