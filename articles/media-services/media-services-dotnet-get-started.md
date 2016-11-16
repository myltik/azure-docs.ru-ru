---
title: "Приступая к работе с доставкой содержимого по запросу с помощью пакета SDK для .NET | Документация Майкрософт"
description: "В этом учебнике показаны шаги по реализации приложения доставки содержимого по запросу с помощью служб мультимедиа Azure и .NET."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/17/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 80606d9fd08a4d5b5845af8ed43fdcef050e47e9


---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Приступая к работе с доставкой содержимого по запросу с помощью пакета SDK для .NET
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
> 
> 

## <a name="overview"></a>Обзор
В этом учебнике содержатся шаги по реализации приложения доставки видео по запросу с помощью пакета SDK служб мультимедиа Azure для .NET.

В учебнике описывается базовый рабочий процесс служба мультимедиа и наиболее распространенные объекты и задачи программирования, необходимые для разработки с использованием служб мультимедиа. По завершении работы с учебником вы сможете выполнить потоковую передачу и поэтапно скачать пример файла мультимедиа, который вы отправили, закодировали и скачали.

## <a name="what-youll-learn"></a>Что вы узнаете
В этом руководстве показано, как выполнять следующие задачи.

1. Создание учетной записи служб мультимедиа (с помощью портала Azure).
2. Настройка конечной точки потоковой передачи с помощью портала Azure.
3. Создание и настройка проекта Visual Studio.
4. Подключение к учетной записи служб мультимедиа.
5. Создание нового актива и отправка видеофайла.
6. Кодирование исходного файла в набор MP4-файлов с адаптивным битрейтом.
7. Публикация ресурса-контейнера и получение URL-адресов для потоковой передачи и поэтапного скачивания.
8. Проверка посредством воспроизведения содержимого.

## <a name="prerequisites"></a>Предварительные требования
Ниже перечислены необходимые условия для выполнения действий, описанных в этом учебнике.

* Для работы с этим учебником требуется учетная запись Azure. 
  
    Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Вы получаете кредиты, которые можно использовать, чтобы попробовать платные службы Azure. Даже после того как кредиты использованы, учетная запись остается за вами и вы можете использовать бесплатные службы и функции Azure, например компонент веб-приложений в службы приложений Azure.
* Операционные системы: Windows 8 или более поздней версии, Windows 2008 R2, Windows 7.
* .NET Framework 4.0 или более поздняя версия.
* Visual Studio 2010 с пакетом обновления 1 (SP1) (Professional, Premium, Ultimate или Express) или более поздняя версия.

## <a name="download-sample"></a>Скачивание образца
Скачать и запустить пример вы можете [здесь](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Создание учетной записи служб мультимедиа Azure с помощью портала Azure
В этом разделе показано, как создать учетную запись AMS.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Щелкните **+Создать** > **Мультимедиа+CDN** > **Службы мультимедиа**.
   
    ![Создание служб мультимедиа](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. В окне **создания учетной записи служб мультимедиа** введите необходимые значения.
   
    ![Создание служб мультимедиа](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. Укажите **имя учетной записи**AMS. Имя учетной записи служб мультимедиа может состоять из букв или цифр в нижнем регистре без пробелов и должно иметь длину от 3 до 24 символов.
   2. В поле "Подписка" выберите одну из доступных подписок Azure.
   3. Выберите новую или существующую **группу ресурсов**.  Группа ресурсов — это коллекция ресурсов с одинаковым жизненным циклом, разрешениями и политиками. Дополнительные сведения см. [здесь](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. Укажите **расположение**(географический регион), в котором будут храниться записи мультимедиа и метаданных для вашей учетной записи служб мультимедиа. Этот регион будет использоваться для обработки и потоковой передачи мультимедиа. В раскрывающемся списке отображаются только доступные регионы служб мультимедиа. 
   5. Выберите **учетную запись хранения**, чтобы определить хранилище BLOB-объектов для мультимедийного содержимого из учетной записи служб мультимедиа. Можно выбрать существующую учетную запись хранения в географическом регионе, где находится учетная запись служб мультимедиа, или создать учетную запись хранения. Новая учетная запись хранения будет создана в том же регионе. В отношении учетных записей хранения действуют те же правила, что и для учетных записей служб носителей.
      
       Дополнительные сведения о хранилище см. [здесь](../storage/storage-introduction.md).
   6. Установите флажок **Закрепить на панели мониторинга** , чтобы отслеживать развертывание учетной записи.
4. Нажмите кнопку **Создать** в нижней части формы.
   
    Когда учетная запись будет создана, состояние изменится на **Выполняется**. 
   
    ![Параметры служб мультимедиа](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Управлять учетной записью AMS (например, передавать видео, кодировать ресурсы, отслеживать выполнение задания) можно в окне **Параметры** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Настройка конечных точек потоковой передачи с помощью портала Azure
При работе со службами мультимедиа Azure один из самых частых сценариев — потоковая передача видео с переменной скоростью для клиентов. Службы мультимедиа поддерживают следующие технологии потоковой передачи с адаптивной скоростью: потоковая трансляция HTTP (HLS), Smooth Streaming, MPEG DASH и HDS (только для владельцев лицензий Adobe PrimeTime/Access).

Службы мультимедиа обеспечивают динамическую упаковку, которая позволяет своевременно доставлять закодированное содержимое MP4-файлов с переменной скоростью в форматах потоковой передачи, которые поддерживаются службами мультимедиа (MPEG DASH, HLS, Smooth Streaming, HDS), без необходимости хранения предварительно упакованных версий каждого из этих форматов потоковой передачи.

Чтобы воспользоваться преимуществом динамической упаковки, необходимо выполнить следующие действия:

* закодировать мезонинный (исходный) файл в набор MP4-файлов с переменной скоростью (шаги кодирования показаны далее в этом руководстве);  
* создать по крайней мере одну единицу потоковой передачи для *конечной точки потоковой передачи* , из которой вы планируете доставлять содержимое. (шаги по изменению числа единиц потоковой передачи приведены далее).

Благодаря динамической упаковке вы оплачиваете хранение файлов только в одном формате, так как службы мультимедиа автоматически подготавливают и передают содержимое в нужном формате, исходя из полученных от клиента запросов.

Чтобы создать зарезервированные единицы потоковой передачи и изменить их число, сделайте следующее:

1. В окне **Параметры** щелкните элемент **Потоковые конечные точки**. 
2. Щелкните конечную точку потоковой передачи по умолчанию. 
   
    Появится окно **сведений о конечной точке потоковой передачи по умолчанию** .
3. Чтобы указать число единиц потоковой передачи, передвиньте ползунок **Единицы потоковой передачи** .
   
    ![Единицы потоковой передачи](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Нажмите кнопку **Сохранить** , чтобы сохранить изменения.
   
   > [!NOTE]
   > Выделение новых единиц потоковой передачи может занять до 20 минут.
   > 
   > 

## <a name="create-and-configure-a-visual-studio-project"></a>Создание и настройка проекта Visual Studio
1. Создайте новое консольное приложение C# в Visual Studio 2013, Visual Studio 2012 или Visual Studio 2010 с пакетом обновления 1 (SP1). Введите значения в поля **Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку **ОК**.
2. Используйте пакет Nuget [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions), чтобы установить **расширения пакета SDK служб мультимедиа Azure для .NET**.  Расширения пакета SDK .NET служб мультимедиа — это набор методов расширения и вспомогательных функций, которые упростят код и разработку с помощью служб мультимедиа. При установке этого пакета также устанавливается **пакет SDK служб мультимедиа для .NET** и добавляются все остальные необходимые зависимости.
3. Добавьте ссылку на сборку System.Configuration. Эта сборка содержит класс **System.Configuration.ConfigurationManager** , используемый для доступа к файлам конфигурации (например, App.config).
4. Откройте файл App.config (добавьте файл в проект, если он не был добавлен по умолчанию) и добавьте в него раздел *appSettings* . Установите значения для имени и ключа учетной записи служб мультимедиа Azure, как показано в следующем примере. Чтобы получить имя учетной записи и сведения о ключе, перейдите на [портал Azure](https://portal.azure.com/) и выберите свою учетную запись AMS. Затем выберите **Параметры** > **Ключи**. В окне "Управление ключами" отображается имя учетной записи, а также первичный и вторичный ключи.
   
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
6. Создайте новую папку в каталоге проектов и скопируйте MP4- или WMV-файл, для которого нужно выполнить кодирование, потоковую передачу или поэтапное скачивание. В этом примере используется путь C:\VideoFiles.

## <a name="connect-to-the-media-services-account"></a>Подключение к учетной записи служб мультимедиа
Если вы используете службы мультимедиа с помощью .NET, то для большинства задач программирования этих служб следует использовать класс **CloudMediaContext**. Это могут быть такие задачи, как подключение к учетной записи служб мультимедиа, создание, обновление и удаление различных объектов и получение доступа к этим объектам: ресурсам-контейнерам, файлам ресурсов-контейнеров, заданиям, политикам доступа, указателям и т. д.

Перезапишите класс "Program" с помощью следующего кода. В коде показано, как считывать значения подключения из файла App.config и как создать объект **CloudMediaContext** для подключения к службам мультимедиа. Дополнительные сведения о подключении к службам мультимедиа см. в статье [Подключение к учетной записи служб мультимедиа с помощью пакета SDK служб мультимедиа для .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

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

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Создание нового актива и отправка видеофайла
В службах мультимедиа цифровые файлы отправляются (или принимаются) в актив. Сущность **Ресурс-контейнер** может содержать видео, аудио, изображения, коллекции эскизов, текстовые каналы и файлы скрытых субтитров (а также метаданные этих файлов).  После отправки этих файлов содержимое сохраняется в безопасном расположении в облаке для дальнейшей обработки и потоковой передачи. Файлы в ресурсе называются **файлами ресурса**.

Метод **UploadFile**, определенный ниже, вызывает **CreateFromFile** (этот метод определен в расширениях пакета SDK для .NET). **CreateFromFile** создает новый ресурс-контейнер, в который отправляется указанный исходный файл.

Метод **CreateFromFile** получает параметры **AssetCreationOptions**, которые позволяют указать один из таких параметров создания ресурса-контейнера.

* **None** — шифрование не используется. Это значение по умолчанию. Обратите внимание, что при использовании этого параметра содержимое не защищено при передаче и хранении.
  Используйте этот параметр, если MP4-файл планируется доставить с помощью поэтапного скачивания.
* **StorageEncrypted**. Используйте этот параметр, чтобы локально зашифровать содержимое с помощью 256-битового шифрования AES, а затем отправить его в хранилище Azure для хранения в зашифрованном виде. Активы, защищенные с помощью шифрования хранилища, автоматически расшифровываются и помещаются в систему зашифрованных файлов до кодирования и при необходимости повторно кодируются до отправки в виде нового выходного актива. Шифрование хранилища чаще всего используется, если нужно защитить входные файлы мультимедиа высокого качества с помощью стойкого шифрования при хранении на диске.
* **CommonEncryptionProtected**. Используйте этот параметр при отправке содержимого, которое уже зашифровано и защищено с помощью стандартного шифрования или PlayReady DRM (например, Smooth Streaming с защитой PlayReady DRM).
* **EnvelopeEncryptionProtected**. Используйте этот параметр при отправке HLS с шифрованием AES. Обратите внимание, что файлы должны быть закодированы и зашифрованы с помощью Transform Manager.

Метод **CreateFromFile** , кроме того, позволяет указывать метод обратного вызова, чтобы сообщать о ходе отправки файла.

В следующем примере в качестве параметров ресурса-контейнера используется **None** .

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


## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Кодирование исходного файла в набор MP4-файлов с адаптивным битрейтом
После приема ресурсов-контейнеров в службы мультимедиа файлы мультимедиа можно кодировать, менять их формат, добавлять к ним водяные знаки и т. д. до их доставки клиентам. Эти действия запланированы и выполняются в нескольких экземплярах фоновых ролей для обеспечения высокой производительности и доступности. Эти действия называются заданиями, и каждое задание состоит из атомарных задач, которые выполняют фактическую работу с файлом ресурса-контейнера.

Как упоминалось ранее, один из наиболее распространенных сценариев при работе со службами мультимедиа Azure — доставка клиентам потоковой передачи с переменной скоростью. Службы мультимедиа могут динамически упаковывать набор MP4-файлов с адаптивным битрейтом в один из следующих форматов: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH и HDS (только для обладателей лицензии Adobe PrimeTime/Access).

Чтобы воспользоваться преимуществом динамической упаковки, необходимо выполнить следующие действия:

* закодировать или перекодировать мезонинный (исходный) файл в набор файлов MP4 или Smooth Streaming с переменной скоростью;  
* получить по крайней мере одну единицу потоковой передачи для конечной точки потоковой передачи, из которой вы планируете доставлять содержимое.

В следующем коде показано, как отправить задание кодирования. Задание содержит одну задачу, которая определяет перекодировку мезонинного файла в набор MP4-файлов с переменной скоростью с помощью **Media Encoder Standard**. Код отправляет задание и ждет его выполнения.

После выполнения заданий вы сможете выполнить потоковую передачу актива и поэтапное скачивание MP4-файлов, созданных в результате перекодирования.
Обратите внимание, что вам не нужно получать единицы потоковой передачи, чтобы поэтапно скачать MP4-файлы.

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

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Публикация ресурса-контейнера и получение URL-адресов для потоковой передачи и поэтапного скачивания
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

## <a name="test-by-playing-your-content"></a>Проверка посредством воспроизведения содержимого
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

## <a name="next-steps-media-services-learning-paths"></a>Дальнейшие действия: схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Не нашли ответа?
Если содержимое этой статьи не соответствует вашим ожиданиям, является неточным или не содержит ответов на ваши вопросы, оставьте отзыв в обсуждении Disqus ниже.

<!-- Anchors. -->


<!-- URLs. -->
[Установщик веб-платформы]: http://go.microsoft.com/fwlink/?linkid=255386
[Портал]: http://manage.windowsazure.com/



<!--HONumber=Nov16_HO2-->


