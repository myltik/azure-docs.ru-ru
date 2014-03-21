<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Служба BLOB-объектов (2.0)" pageTitle="Использование хранилища BLOB-объектов — руководство по компонентам Windows Azure" metaKeywords="Начало работы с BLOB-объектами Azure Неструктурированные данные Azure Неструктурированное хранилище Azure BLOB-объекты Azure Хранилище BLOB-объектов Azure Хранилище BLOB-объектов Azure .NET Хранилище BLOB-объектов Azure C# Хранилище BLOB-объектов Azure C#" description="Узнайте, как использовать службу BLOB-объектов Windows Azure для отправки, загрузки, перечисления и удаления содержимого BLOB-объектов. Примеры написаны на C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Использование службы хранения больших двоичных объектов Windows Azure в .NET" />


# Использование службы хранения больших двоичных объектов Windows Azure в .NET

<div class="dev-center-tutorial-selector">
<a href="/ru-ru/develop/net/how-to-guides/blob-storage-v17/" title="версия 1.7">версия 1.7</a>
<a href="/ru-ru/develop/net/how-to-guides/blob-storage/" title="версия 2.0" class="current">версия 2.0</a> 
</div>


В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Windows Azure. Примеры написаны на C\#
и используют библиотеку клиента хранения Windows Azure для .NET (версия 2.0). Здесь описаны такие сценарии, как **отправка**, **перечисление**, **загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия][].

##Оглавление

-   [Что такое хранилище BLOB-объектов][]
-   [Основные понятия][]
-   [Создание учетной записи хранения Windows Azure][]
-   [Настройка строки подключения к хранилищу Windows Azure][]
-   [Программный доступ к хранилищу BLOB-объектов][]
-   [Создание контейнера][]
-   [Отправка BLOB-объекта в контейнер][]
-   [Перечисление BLOB-объектов в контейнере][]
-   [Загрузка BLOB-объектов][]
-   [Удаление BLOB-объектов][]
-   [Дальнейшие действия][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a><span  class="short-header">Создание учетной записи</span>Создание учетной записи хранения Windows Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a><span  class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу

Библиотека клиента хранения Windows Azure для .NET поддерживает использование строки подключения для настройки конечных точек и учетных данных для доступа к службам хранилища. Строку подключения можно разместить в файле конфигурации,
а не жестко задать ее в коде:

- При использовании облачных служб Windows Azure рекомендуется хранить строки подключения с помощью системы конфигурации службы Windows Azure (*CSDEF- и *CSCFG-файлы).
- Если используются веб-сайты Windows Azure, виртуальных машин Windows Azure или создаются приложения .NET, которые должны работать вне Windows Azure, рекомендуется хранить строки подключения с помощью системы конфигурации .NET (например, в файле web.config или app.config).

Извлечение строки подключения показано далее в этом руководстве.

### Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Windows Azure и позволяет динамически изменять параметры конфигурации на портале управления Windows Azure без повторного развертывания приложения.

Настройка строки подключения в конфигурации службы Windows Azure:

1.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши в папке **Роли**
    вашего проекта развертывания Windows Azure веб-роль или рабочую роль и выберите **Свойства**  
    ![Выбор свойств роли облачной службы в Visual Studio][Blob5]

2.  Откройте вкладку **Параметры** и нажмите кнопку **Добавить параметр**.  
    ![Добавление параметра облачной службы в Visual Studio][Blob6]

    В таблице параметров появится новая запись **Setting1**.

3.  В раскрывающемся списке **Тип** новой записи **Setting1** выберите **Строка подключения**.  
    ![Blob7][Blob7]

4.  Нажмите кнопку **...** в правой части записи **Setting1**.
    Откроется диалоговое окно **Строка подключения учетной записи хранения**.

5.  Выберите для использования целевой эмулятор (хранилище Windows Azure, смоделированное на локальном компьютере) или учетную запись хранения в облаке. Код, представленный в этом руководстве, работает в любом случае. Введите **первичный ключ доступа**, скопированный на предыдущем шаге этого учебника, чтобы сохранить данные BLOB-объекта в учетную запись хранилища, созданную ранее в Windows Azure.   
    ![Blob8][Blob8]

6.  Изменить **имя** записи с **Setting1** на более понятное, например **StorageConnectionString**. Эта строка подключения будет использоваться далее в этом руководстве.  
    ![Blob9][Blob9]
	
### Настройка строки подключения с помощью конфигурации .NET

При написании приложения, которое не является облачной службой Windows Azure (см. предыдущий раздел), рекомендуется использовать систему конфигурации .NET (например, файл web.config или app.config).  Это относится к веб-сайтам Windows Azure или виртуальным машинам для Windows Azure, а также приложениям, разработанным для работы вне Windows Azure.  Строка подключения хранится с помощью элемента "<appSettings>" следующим образом:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

Дополнительные сведения о строках подключения к хранилищу см. в разделе [Настройка строк подключения][].
	
Теперь все готово для выполнения задач, описанных в данном руководстве.

## <a name="configure-access"> </a><span  class="short-header">Программный доступ</span>Программный доступ к хранилищу BLOB-объектов

###Получение сборки
Вы можете получить сборку "Microsoft.WindowsAzure.Storage.dll" с помощью NuGet. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Windows Azure и зависимые компоненты.

Библиотека "Microsoft.WindowsAzure.Storage.dll" также включена в пакет Windows Azure SDK для .NET 2.0, который можно загрузить из <a href="http://www.windowsazure.com/ru-ru/develop/net/#">Центра разработчиков .NET</a>. Эта сборка устанавливается в каталог %Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\v2.0\ref\.

###Объявления пространств имен
Добавьте следующие объявления пространств имен в начало любого файла C\#, в котором вы собираетесь получать доступ к хранилищу Windows Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Убедитесь, что указана ссылка на сборку "Microsoft.WindowsAzure.Storage.dll".

###Получение строки подключения
Для представлений сведений о вашей учетной записи хранения можно использовать тип **CloudStorageAccount**. Если вы используете шаблон проекта Windows Azure или ссылаетесь на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, с помощью типа **CloudConfigurationManager** можно извлечь строку подключения и сведения об учетной записи хранения из конфигурации службы Windows Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в "web.config" или "app.config", как показано выше, для получения строки подключения можно использовать **ConfigurationManager**.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Тип **CloudBlobClient** позволяет извлекать объекты, представляющие контейнеры и BLOB-объекты, хранящихся в службе хранилища BLOB-объектов. Следующий код создает объект **CloudBlobClient** с помощью объекта учетной записи хранения, полученной выше:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Зависимости ODataLib
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты ODataLib [OData], [Edm] и [Spatial].

## <a name="create-container"> </a><span  class="short-header">Создание контейнера</span>Создание контейнера

Все BLOB-объекты хранилища содержатся в контейнере. С помощью объекта **CloudBlobClient** можно получить ссылку на контейнер, который необходимо использовать. Контейнер можно создать, если он не существует:

    // Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент службы BLOB-объектов.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получить ссылку на контейнер. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Создать контейнер, если он не существует.
    container.CreateIfNotExists();

По умолчанию новый контейнер является закрытым, и вам необходимо указать ключ доступа к хранилищу, чтобы загрузить BLOB-объекты из этого контейнера. Чтобы сделать файлы в этом контейнере доступными для всех пользователей, сделайте контейнер открытым, используя следующий код:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Любой пользователь в Интернете может видеть BLOB-объекты в открытом контейнер, но изменить или удалить их можно только при наличии ключа доступа.

<h2> <a name="upload-blob"> </a><span  class="short-header">Отправка в контейнер</span>Отправка BLOB-объекта в контейнер</h2>

Хранилище BLOB-объектов Windows Azure поддерживает блочные и страничные BLOB-объекты.  В большинстве случаев рекомендуется использовать блочные BLOB-объекты.

Для отправки файла в блочный BLOB-объект получите ссылку на контейнер и используйте ее для получения ссылки на блочный BLOB-объект. Получив ссылку, вы можете отправить в него любой поток данных с помощью метода **UploadFromStream**. Эта операция создает BLOB-объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

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
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a><span  class="short-header">Перечисление BLOB-объектов в контейнере</span>Перечисление BLOB-объектов в контейнере

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. Затем можно использовать метод **ListBlobs** контейнера, чтобы извлечь BLOB-объекты или каталоги в нем. Для доступа к широкому набору свойств и методов возвращаемого объекта **IListBlobItem** необходимо преобразовать его в объект **CloudBlockBlob**, 
**CloudPageBlob** или **CloudBlobDirectory**.  Если тип неизвестен, можно использовать проверку типов, чтобы определить нужный тип.  Следующий код демонстрирует, как получить и вывести URI каждого элемента в контейнере "photos".

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

Как показано выше, служба BLOB-объектов также использует концепцию каталогов внутри контейнеров. Таким образом можно организовать BLOB-объекты в структуре, похожую на папки. Например, рассмотрим следующий набор блочных BLOB-объектов контейнере с именем "photos":

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

При вызове метода **ListBlobs** в контейнере "photos" (как в примере выше) возвращаемая коллекция будет содержать объекты **CloudBlobDirectory** и **CloudBlockBlob**, представляющие собой каталоги и BLOB-объекты верхнего уровня. Здесь результатом будет:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


При необходимости можно установить для параметра **UseFlatBlobListing** метода **ListBlobs** значение **true**. Это приведет к возвращению каждого BLOB-объекта в виде **CloudBlockBlob** независимо от каталога.  Здесь вызывается метод **ListBlobs**:

    // Выполнить цикл по элементам в контейнере и показать их длину и URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

а здесь результатом будет:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Дополнительные сведения см. в разделе [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a><span  class="short-header">Загрузка BLOB-объектов</span>Загрузка BLOB-объектов

Для загрузки BLOB-объектов сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод **DownloadToStream**. В следующем примере метод **DownloadToStream** используется для переноса содержимого BLOB-объекта в объект stream, который затем можно сохранить в локальном файле.

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
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Можно также использовать **DownloadToStream** метод, чтобы загрузить содержимое BLOB-объекта как текстовую строку.

	// Получить учетную запись хранения из строки подключения.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создать клиент службы BLOB-объектов.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получить ссылку на ранее созданный контейнер.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Получить ссылку на BLOB-объект с именем "myblob.txt".	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a><span  class="short-header">Удаление BLOB-объектов</span>Удаление BLOB-объектов

Для удаления BLOB-объекта сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод **Delete**.

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

## <a name="next-steps"></a><span  class="short-header">Дальнейшие действия</span>Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.
<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе BLOB-объектов:
  <ul>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/wa_storage_api_ref_reference_home.aspx">Справочник по клиентской библиотека хранения для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Windows Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Windows Azure</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Windows Azure.
  <ul>
    <li>Использование <a href="/ru-ru/develop/net/how-to-guides/table-services/">табличного хранилища</a> для хранения структурированных данных.</li>
    <li>Использование <a href="/ru-ru/develop/net/how-to-guides/sql-database/">базы данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>

  [Следующие шаги]: #next-steps
  [Что такое хранилище BLOB-объектов]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Windows Azure]: #create-account
  [Настройка строки подключения к хранилищу]: #setup-connection-string
  [Программный доступ к хранилищу BLOB-объектов]: #configure-access
  [Создание контейнера]: #create-container
  [Отправка BLOB-объекта в контейнер]: #upload-blob
  [Перечисление BLOB-объектов в контейнере]: #list-blob
  [Загрузка BLOB-объектов]: #download-blobs
  [Удаление BLOB-объектов]: #delete-blobs
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs-20/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs-20/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs-20/blob9.png
  
  [Хранение данных и доступ к ним в Windows Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Блог команды разработчиков хранилища Windows Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Настройка строк подключения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758697.aspx
  [Справочник по библиотеке клиента .NET]: http://msdn.microsoft.com/ru-ru/library/windowsazure/wl_svchosting_mref_reference_home
  [Справочник по REST API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

