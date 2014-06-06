<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Служба BLOB-объектов (2.0)" pageTitle="Использование хранилища BLOB-объектов — руководство по компонентам Azure" metaKeywords="Начало работы с BLOB-объектами Azure, неструктурированные данные Azure, неструктурированное хранилище Azure, BLOB-объекты Azure, хранилище BLOB-объектов Azure, BLOB-объекты Azure в .NET, среда C# для BLOB-объектов Azure, среда C# для BLOB-объектов Azure" description="Узнайте, как использовать службу BLOB-объектов Azure для отправки, загрузки, перечисления и удаления содержимого BLOB-объектов. Примеры написаны на языке C#." metaCanonical="" services="" documentationCenter="" title="Использование службы хранения BLOB-объектов Azure в .NET" authors=""  solutions="" writer="" manager="" editor=""  />














# Использование службы хранения больших двоичных объектов Azure в .NET

<div class="dev-center-tutorial-selector">
<a href="/ru-ru/develop/net/how-to-guides/blob-storage-v17/" title="версия 1.7" class="current">версия 1.7</a>
<a href="/ru-ru/develop/net/how-to-guides/blob-storage/" title="версия 2.0">версия 2.0</a> 
</div>


В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранения BLOB-объектов Azure. Примеры написаны на языке C\# и
используют интерфейс .NET API. Здесь описаны такие сценарии, как
**отправка**, **перечисление**, **загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия][].

<h2>Оглавление</h2>

-   [Что такое хранилище BLOB-объектов][]
-   [Основные понятия][]
-   [Создание учетной записи хранения Azure][]
-   [Настройка строки подключения к хранилищу][]
-   [Практическое руководство. Программный доступ к хранилищу BLOB-объектов][]
-   [Практическое руководство. Создание контейнера][]
-   [Практическое руководство. Отправка BLOB-объекта в контейнер][]
-   [Практическое руководство. Перечисление BLOB-объектов в контейнере][]
-   [Практическое руководство. Загрузка BLOB-объектов][]
-   [Практическое руководство. Удаление BLOB-объектов][]
-   [Дальнейшие действия][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Создание учетной записи</span>Создание учетной записи хранения Azure</h2>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу</h2>

API хранилища Azure для .NET поддерживает использование строки подключения для настройки конечных точек и учетных данных для доступа к службам хранилища. Строку подключения можно разместить в файле конфигурации,
а не жестко задать ее в коде:

- При использовании облачных служб Azure рекомендуется сохранять строки подключения с помощью системы конфигурации службы Azure (в файлах `*.csdef` и `*.cscfg`).
- При использовании веб-сайтов Azure или виртуальных машин Azure рекомендуется сохранять строки подключения с использованием системы конфигурации .NET (например, в файле `web.config`).

В обоих случаях можно извлечь строку подключения с помощью метода `CloudConfigurationManager.GetSetting`, как показано далее в этом руководстве.

### Настройка строки подключения при использовании облачных служб

Механизм настройки службы является уникальным для проектов облачных служб Azure и позволяет динамически изменять параметры конфигурации на портале управления Azure без повторного развертывания приложения.

Настройка строки подключения в конфигурации службы Azure:

1.  В обозревателе решений Visual Studio в папке **Роли**
    вашего проекта развертывания Azure щелкните правой кнопкой мыши
    веб-роль или рабочую роль и выберите пункт **Свойства**.  
    ![Выбор свойств роли облачной службы в Visual Studio][Blob5]

2.  Откройте вкладку **Параметры** и нажмите кнопку **Добавить параметр**.  
    ![Добавление параметра облачной службы в Visual Studio][Blob6]

    В таблице параметров появится новая запись **Setting1**.

3.  В раскрывающемся списке **Тип** новой записи **Setting1** выберите
    **Строка подключения**.  
    ![Blob7][Blob7]

4.  Нажмите кнопку **...** в правой части записи **Setting1**.
    Открывается диалоговое окно **Строка подключения учетной записи хранения**.

5.  Выберите для использования целевой эмулятор (хранилище
    Azure, смоделированное на локальном компьютере) или фактическую учетную
    запись хранения в облаке. Код, представленный в этом руководстве, работает в любом случае. Введите **первичный ключ доступа**, скопированный на предыдущем шаге этого учебника, чтобы сохранить данные BLOB-объекта в учетную запись хранилища, созданную ранее в Azure.   
    ![Blob8][Blob8]

6.  Измените **Имя** записи со значения **Setting1** на более понятное,
    например **StorageConnectionString**. Эта строка подключения будет использоваться далее в этом руководстве.  
    ![Blob9][Blob9]
	
### Настройка строки подключения при использовании веб-сайтов или виртуальных машин

При использовании веб-сайтов или виртуальных машин рекомендуется использовать систему конфигурации .NET (например, файл `web.config`).  Строка подключения хранится с помощью элемента `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

Дополнительные сведения о строках подключения к хранилищу см. в разделе [Настройка строк подключения][].
	
Теперь все готово для выполнения задач, описанных в данном руководстве.

<h2> <a name="configure-access"> </a><span  class="short-header">Программный доступ</span>Практическое руководство. Программный доступ к хранилищу BLOB-объектов</h2>

Добавьте следующие объявления пространств имен в начало любого файла C\#, в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Вы можете использовать тип **CloudStorageAccount** и
**CloudConfigurationManager**, чтобы
извлечь строку подключения и сведения об учетной записи
хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Тип **CloudBlobClient** позволяет извлекать объекты, представляющие контейнеры и BLOB-объекты, хранящихся в службе хранилища BLOB-объектов. Следующий код создает объект **CloudBlobClient** с помощью объекта учетной записи хранения, полученной выше:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

<h2> <a name="create-container"> </a><span  class="short-header">Создание контейнера</span>Практическое руководство. Создание контейнера</h2>

Все BLOB-объекты хранилища содержатся в контейнере. С помощью объекта **CloudBlobClient** можно получить ссылку на контейнер, который необходимо использовать. Контейнер можно создать, если он не существует:

    // Получение учетной записи хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создание клиента службы BLOB-объектов 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получение ссылки на контейнер 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Создание контейнера, если он не существует
    container.CreateIfNotExist();

По умолчанию новый контейнер является закрытым, поэтому вам необходимо указать
ключ доступа к хранилищу (как в уже делали раньше), чтобы загрузить BLOB-объекты из этого
контейнера. Чтобы сделать файлы в этом контейнере доступными для всех пользователей, сделайте контейнер открытым, используя следующий код:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Любой пользователь в Интернете может видеть BLOB-объекты в открытом контейнер, но изменить или удалить их можно только при наличии ключа доступа.

<h2> <a name="upload-blob"> </a><span  class="short-header">Отправка в контейнер</span>Практическое руководство. Отправка BLOB-объекта в контейнер</h2>

Для отправки файла в BLOB-объект получите ссылку на контейнер и используйте ее для получения
ссылки на BLOB-объект. После получения ссылки на BLOB-объект вы можете отправить в него любой
поток данных, вызвав метод **UploadFromStream** в ссылке
на BLOB-объект. Эта операция создает BLOB-объект, если он не существует,
или заменяет его, если он существует. Ниже приведен соответствующий пример кода, в котором предполагается,
что контейнер уже был создан.

    // Получение учетной записи хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создание клиента службы BLOB-объектов
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получение ссылки на ранее созданный контейнер
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Получение ссылки на BLOB-объект с именем "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");

    // Создание или замена BLOB-объекта "myblob" содержимым из локального файла
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blob.UploadFromStream(fileStream);
    } 

<h2> <a name="list-blob"> </a><span  class="short-header">Перечисление BLOB-объектов в контейнере</span>Практическое руководство. Перечисление BLOB-объектов в контейнере</h2>

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. Затем
можно использовать метод **ListBlobs** контейнера, чтобы извлечь BLOB-объекты
или каталоги в нем. Следующий код демонстрирует, как получить и вывести
**Uri** каждого BLOB-объекта в контейнере.

    // Получение учетной записи хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создание клиента службы BLOB-объектов
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получение ссылки на ранее созданный контейнер
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Выполнение цикла по BLOB-объектам в контейнере и вывод URI для каждого из них
    foreach (var blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    } 

Служба BLOB-объектов также использует концепцию каталогов
внутри контейнеров. Таким образом можно организовать BLOB-объекты в структуре, похожую на папки. Например, можно использовать контейнер с именем "photos", в
который отправлены BLOB-объекты 'rootphoto1', '2010/photo1',
'2010/photo2' и '2011/photo1'. При этом в контейнере 'photos' виртуально
создаются каталоги '2010' и '2011'. При вызове метода **ListBlobs** для контейнера 'photos' возвращаемая коллекция
будет содержать объекты **CloudBlobDirectory** и **CloudBlob**,
представляющие собой каталоги и BLOB-объекты верхнего уровня. В
данном случае будут возвращены каталоги '2010' и '2011', а также
фотография 'rootphoto1'. При необходимости можно передать новый
класс **BlobRequestOptions**, у которого для параметра **UseFlatBlobListing** установлено значение
**true**. Это приведет к возвращению каждого BLOB-объекта
независимо от каталога. Дополнительные сведения см. в разделе [CloudBlobContainer.ListBlobs][].

<h2> <a name="download-blobs"> </a><span  class="short-header">Загрузка BLOB-объектов</span>Практическое руководство. Загрузка BLOB-объектов</h2>

Чтобы загрузить BLOB-объекты, сначала нужно получить ссылку на BLOB-объект. В следующем примере метод **DownloadToStream** используется для переноса содержимого BLOB-объекта в объект stream, который затем можно сохранить в локальном файле.
Можно также вызвать методы **DownloadToFile**, **DownloadByteArray** или **DownloadText** BLOB-объекта.

    // Получение учетной записи хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создание клиента службы BLOB-объектов
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получение ссылки на ранее созданный контейнер
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Получение ссылки на BLOB-объект с именем "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");

    // Сохранение содержимого BLOB-объекта на диске
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blob.DownloadToStream(fileStream);
    } 

<h2> <a name="delete-blobs"> </a><span  class="short-header">Удаление BLOB-объектов</span>Практическое руководство. Удаление BLOB-объектов</h2>

Наконец, для удаления BLOB-объекта нужно получить ссылку на BLOB-объект, а затем вызвать
для него метод **Delete**.

    // Получение учетной записи хранения из строки подключения
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Создание клиента службы BLOB-объектов
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Получение ссылки на ранее созданный контейнер
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Получение ссылки на BLOB-объект с именем "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");

    // Удаление BLOB-объекта
    blob.Delete(); 

<h2> <a name="next-steps"></a><span  class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.
<ul>
<li>Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе BLOB-объектов:
  <ul>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/wl_svchosting_mref_reference_home">Справочник по библиотеке клиента .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Azure</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использование <a href="/ru-ru/develop/net/how-to-guides/table-services/">табличного хранилища</a> для хранения структурированных данных.</li>
    <li>Использование <a href="/ru-ru/develop/net/how-to-guides/sql-database/">базы данных SQL</a> для хранения реляционных данных.</li>
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
  [Практическое руководство. Удаление BLOB-объектов]: #delete-blobs
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs-17/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs-17/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs-20/blob9.png
  
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Настройка строк подключения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758697.aspx
  [Справочник по библиотеке клиента .NET]: http://msdn.microsoft.com/ru-ru/library/windowsazure/wl_svchosting_mref_reference_home
  [Справочник по REST API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355

