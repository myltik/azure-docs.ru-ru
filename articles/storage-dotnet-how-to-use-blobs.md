<properties urlDisplayName="Blob Service" pageTitle="Использование хранилища BLOB-объектов из .NET на Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Использование хранилища BLOB-объектов из .NET

В этом руководстве показано, как реализовать типичные сценарии с использованием
службы хранения BLOB-объектов Azure. Примеры кода написаны на C\#
и используют библиотеку клиента хранения Azure для .NET. Здесь описаны такие сценарии, как
**отправка**, **перечисление**, **загрузка** и **удаление** BLOB-объектов. Для
Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия][Дальнейшие действия].

> [WACOM.NOTE] В этом руководстве используется библиотека клиента хранения Azure для .NET 2.x и выше. Рекомендуется использовать библиотеку клиента хранения 4.x, которая доступна через [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) или как часть [Пакета Azure SDK для .NET](/ru-ru/downloads/). См. [Практическое руководство. Программный доступ к хранилищу BLOB-объектов][Практическое руководство. Программный доступ к хранилищу BLOB-объектов] ниже для дополнительной информации о получении библиотеки клиента хранения.

##Оглавление

-   [Что такое хранилище BLOB-объектов][Что такое хранилище BLOB-объектов]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]
-   [Настройка строки подключения к хранилищу][Настройка строки подключения к хранилищу]
-   [Практическое руководство. Программный доступ к хранилищу BLOB-объектов][Практическое руководство. Программный доступ к хранилищу BLOB-объектов]
-   [Практическое руководство. Создание контейнера][Практическое руководство. Создание контейнера]
-   [Практическое руководство. Отправка BLOB-объекта в контейнер][Практическое руководство. Отправка BLOB-объекта в контейнер]
-   [Практическое руководство. Перечисление BLOB-объектов в контейнере][Практическое руководство. Перечисление BLOB-объектов в контейнере]
-   [Практическое руководство. Загрузка BLOB-объектов][Практическое руководство. Загрузка BLOB-объектов]
-   [Практическое руководство. Удаление blob-объектов][Практическое руководство. Удаление blob-объектов]
-   [Практическое руководство. Асинхронное расположение BLOB-объектов на странице.][Практическое руководство. Асинхронное расположение BLOB-объектов на странице.]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-блоб-объект-хранилище](../includes/howto-blob-storage.md)]

##<a name="create-account"></a><span  class="short-header">Создание учетной записи хранения Azure</span>
[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a><span  class="short-header">Настройка строки подключения к хранилищу</span>

[WACOM.INCLUDE [хранилище-настройка-подключение-строка](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">Практическое руководство. Программный доступ к хранилищу BLOB-объектов</span>

###Получение сборки
Мы рекомендуем использовать NuGet для получения сборки `Microsoft.WindowsAzure.Storage.dll`. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

Библиотека `Microsoft.WindowsAzure.Storage.dll` также включена в пакет Azure SDK для .NET, который можно загрузить из <a href="http://www.windowsazure.com/ru-ru/develop/net/#">Центра разработчиков .NET</a>. Эта сборка устанавливается в каталог `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

###Объявления пространств имен
Добавьте следующие объявления пространств имен кода в начало любого файла\#,
в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Убедитесь, что указана ссылка на сборку `Microsoft.WindowsAzure.Storage.dll`.

###Получение строки подключения
Можно использовать тип **CloudStorageAccount** для представлений 
сведений о вашей учетной записи хранения.  Если вы используете 
шаблон проекта Azure или ссылаетесь на 
пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, 
с помощью типа **CloudConfigurationManager**
можно извлечь строку подключения и сведения об учетной записи
хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на Microsoft.WindowsAzure.CloudConfigurationManager, и строка подключения находится в `web.config` или `app.config` как показано выше, для получения строки подключения можно использовать **ConfigurationManager** для получения строки подключения.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Тип **CloudBlobClient** позволяет извлекать объекты, представляющие
контейнеры и BLOB-объекты, хранящиеся в службе хранилища BLOB-объектов. Следующий
пример кода создает объект **CloudBlobClient** с помощью 
объекта учетной записи хранения, полученной выше:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Зависимости ODataLib
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты [OData], [Edm] и [Spatial].

## <a name="create-container"> </a><span  class="short-header">Практическое руководство. Создание контейнера</span>

Каждый BLOB-объект в Azure должен располагаться в контейнере. В этом примере показано, как создать контейнер:

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент службы BLOB-объектов.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получить ссылку на контейнер. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Создать контейнер, если он не существует.
    container.CreateIfNotExists();

По умолчанию новый контейнер является закрытым, и вам необходимо указать
ключ доступа к хранилищу, чтобы загрузить BLOB-объекты из этого
контейнера. Чтобы сделать файлы в этом контейнере доступными
для всех пользователей, сделайте контейнер открытым, используя следующий
код:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Любой пользователь в Интернете может видеть BLOB-объекты в открытом контейнере, но
изменить или удалить их можно только при наличии ключа доступа.

## <a name="upload-blob"> </a><span  class="short-header">Практическое руководство. Отправка BLOB-объекта в контейнер</span>

Хранилище BLOB-объектов Azure поддерживает блочные и страничные BLOB-объекты.  В большинстве случаев рекомендуется использовать блочные BLOB-объекты.

Для отправки файла в блочный BLOB-объект получите ссылку на контейнер и используйте ее для получения
ссылки на блочный BLOB-объект. Получив ссылку, вы можете
отправить в него любой поток данных с помощью метода **UploadFromStream**. Эта операция создает BLOB-объект, если он не существует,
или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент службы BLOB-объектов.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получить ссылку на ранее созданный контейнер.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Получить ссылку на BLOB-объект с именем "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Создать или заменить BLOB-объект "myblob" содержимым из локального файла.
    с помощью (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a><span  class="short-header">Практическое руководство. Перечисление BLOB-объектов в контейнере</span>

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. Затем
можно использовать метод **ListBlobs** контейнера, чтобы извлечь BLOB-объекты и/или каталоги
в нем. Для доступа к широкому набору свойств и методов 
возвращаемого объекта **IListBlobItem**, необходимо преобразовать его в объект **CloudBlockBlob**, 
**CloudPageBlob** или **CloudBlobDirectory**.  Если тип неизвестен, можно использовать 
проверку типов, чтобы определить нужный тип.  Следующий код 
демонстрирует, как получить и вывести URI каждого элемента в 
контейнере `photos`:

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент службы BLOB-объектов. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Получить ссылку на ранее созданный контейнер.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Выполнить цикл по элементам в контейнере и показать их длину и URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;
			
			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

Как показано выше, служба BLOB-объектов также использует концепцию каталогов внутри
контейнеров. Таким образом можно организовать BLOB-объекты в структуре, похожую
на папки. Например, рассмотрим следующий набор блочных BLOB-объектов контейнере
с именем "photos":

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

При вызове метода **listBlobs** для контейнера "photos" возвращаемая коллекция
будет содержать объекты **CloudBlobDirectory** и **CloudBlockBlob**,
представляющие каталоги и BLOB-объекты, содержащиеся на верхнем уровне. Здесь результатом будет:

	Каталог: https://<accountname>.blob.core.windows.net/photos/2010/
	Каталог: https://<accountname>.blob.core.windows.net/photos/2011/
	BLOB-блок длиной 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


При необходимости можно установить для параметра **UseFlatBlobListing** метода **ListBlobs** 
**значение**. Это приведет к возвращению каждого BLOB-объекта в виде ** CloudBlockBlob**
независимо от каталога.  Здесь вызывается метод **ListBlobs**:

    // Выполнить цикл по элементам в контейнере и показать их длину и URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

а здесь результатом будет:

	BLOB-блок длиной 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	BLOB-блок длиной 314618: ttps://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	BLOB-блок длиной 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	BLOB-блок длиной 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	BLOB-блок длиной 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	BLOB-блок длиной 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	BLOB-блок длиной 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	BLOB-блок длиной 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Дополнительные сведения см. в описании метода [CloudBlobContainer.ListBlobs][CloudBlobContainer.ListBlobs].

## <a name="download-blobs"> </a><span  class="short-header">Практическое руководство. Загрузка BLOB-объектов</span>

Для загрузки BLOB-объектов сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод **DownloadToStream**. В следующем
примере метод **DownloadToStream** используется для переноса содержимого большого двоичного объекта
в объект stream, который затем можно сохранить в локальном файле.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент службы BLOB-объектов.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получить ссылку на ранее созданный контейнер.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Получить ссылку на BLOB-объект с именем "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Сохранить содержимое BLOB-объекта в файле.
    с помощью (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Можно также использовать метод **DownloadToStream**, чтобы загрузить содержимое BLOB-объекта как текстовую строку.

	// Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент службы BLOB-объектов.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получить ссылку на ранее созданный контейнер.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Получить ссылку на BLOB-объект с именем "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	с помощью (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a><span  class="short-header">Практическое руководство. Удаление blob-объектов</span>

Для удаления BLOB-объекта сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод
**Delete**.

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент службы BLOB-объектов.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получить ссылку на ранее созданный контейнер.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Получить ссылку на BLOB-объект с именем "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Удалить BLOB-объект.
    blockBlob.Delete(); 


##<a name="list-blobs-async"> </a><span  class="short-header">Практическое руководство. Асинхронное расположение BLOB-объектов на странице.</span> 

Если вам нужно расположить большое количество BLOB-объектов или вы хотите управлять отображением количества объектов в результате запроса, вы можете задать расположение BLOB-объектов на странице. В этом примере вы узнаете, как расположить запрошенные результаты на странице асинхронно для того, чтобы не блокировать выполнение задачи ожиданием большого объема возвращаемых данных.

В этом примере вы узнаете о плоском размещении результатов, а также о том, как задать иерархическое размещение результатов, установив параметр `useFlatBlobListing` метода **ListBlobsSegmentedAsync** на `false`.

Поскольку в примере вызывается метод асинхронного расположения результатов, вам необходимо задать ключевое слово `async` перед ним. После этого метод вернет объект  **Задания**. При ожидании ключевого слова для **ListBlobsSegmentedAsync** метод приостанавливает выполнение примера до тех пор, пока задача размещения результатов не завершена.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Получить учетную запись хранения из строки подключения.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Создать клиент службы BLOB-объектов.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Получить ссылку на ранее созданный контейнер.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        Расположение BLOB-объектов на странице.
        Console.WriteLine("Расположение BLOB-объектов на странице:");

        //Для примера расположите BLOB-объекты на странице по 10. 
		Первый вызов не содержит маркер продолжения.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Перечислите результат возвращенных сегментов.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //При наличии дополнительных страниц с результатами получить маркер продолжения.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Проверьте наличие дополнительных результатов и укажите их на страницах по 10 в т время как будет возвращен маркер продолжения.
        while (continuationToken != null)
        {
            //Эта перегрузка позволяет контролировать размер страницы. 
			//Вы можете вернуть все оставшиеся результаты, передав значение null для параметра maxResults 
            //или вызвав другую перегрузку.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Получить другой маркер продолжения.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a><span  class="short-header">Дальнейшие действия</span>

Вы узнали основные сведения о хранилище BLOB-объектов. По следующим ссылкам
можно найти дополнительные сведения о более сложных задачах по использованию хранилища.
<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе BLOB-объектов:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Справочник по клиентской библиотеке хранения для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Azure</a>.</li>
<li>Дополнительные сведения о работе с хранилищем Azure в серверных процессах для веб-сайтов Azure см. в статье <a href="/ru-ru/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Начиная работу с пакетом Azure WebJobs SDK</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использование <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/">табличного хранилища</a> для хранения структурированных данных.</li>
    <li>Использование <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-queues/">Хранилища очередей</a> для хранения неструктурированных данных.</li>
    <li>Использование <a href="/ru-ru/documentation/articles/sql-database-dotnet-how-to-use/">базы данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>

  [Дальнейшие действия]: #next-steps
  [Что такое хранилище BLOB-объектов]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Настройка строки подключения к хранилищу]: #setup-connection-string
  [Практическое руководство. Программный доступ к хранилищу BLOB-объектов]: #configure-access
  [Практическое руководство. Создание контейнера]: #create-container
  [Практическое руководство. Отправка BLOB-объекта в контейнер]: #upload-blob
  [Практическое руководство. Перечисление BLOB-объектов в контейнере]: #list-blob
  [Практическое руководство. Загрузка BLOB-объектов]: #download-blobs
  
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
