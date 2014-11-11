<properties linkid="dev-net-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Использование хранилища BLOB-объектов из .NET

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранения BLOB-объектов Windows Azure. Примеры написаны на C# и используют библиотеку клиента хранения Azure для .NET. Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия][Дальнейшие действия].

> [WACOM.NOTE] В этом руководстве используется библиотека клиента хранения Azure для .NET 2.x и выше. Рекомендуется использовать библиотеку клиента хранения 4.x, которая доступна через [NuGet][NuGet] или как часть [Пакета Azure SDK для .NET][Пакета Azure SDK для .NET]. См. [Практическое руководство. Программный доступ к хранилищу BLOB-объектов][Практическое руководство. Программный доступ к хранилищу BLOB-объектов] ниже для дополнительной информации о получении библиотеки клиента хранения.

## Оглавление

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
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-блоб-объект-хранилище](../includes/howto-blob-storage.md)]

## <a name="create-account"></a><span class="short-header">Создание учетной записи</span>Создание учетной записи хранения Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <a name="setup-connection-string"></a><span class="short-header">Настройка строки подключения</span>Настройка строки подключения к хранилищу

[WACOM.INCLUDE [хранилище-настройка-подключение-строка](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span class="short-header">Программный доступ</span>Практическое руководство: Программный доступ к хранилищу BLOB-объектов

### Получение сборки

Можно использовать NuGet для получения сборки `Microsoft.WindowsAzure.Storage.dll`. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**. Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

`Microsoft.WindowsAzure.Storage.dll` также включена в состав пакета Azure SDK для .NET, который можно загрузить из [Центра разработчиков .NET][Центра разработчиков .NET]. Сборка устанавливается в каталог `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code>.

### Объявления пространств имен

Добавьте следующие объявления пространств имен кода в начало любого файла C# ,в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Убедитесь, что указана ссылка на сборку `Microsoft.WindowsAzure.Storage.dll`.

### Получение строки подключения

Для представлений сведений о вашей учетной записи хранения можно использовать тип **CloudStorageAccount**.  Если вы используете шаблон проекта Azure или ссылаетесь на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager, с помощью типа **CloudConfigurationManager**  можно извлечь строку подключения и сведения об учетной записи хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в файле `web.config` или `app.config`, как показано выше, то для получения строки подключения можно использовать **ConfigurationManager**. Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Тип **CloudBlobClient** позволяет извлекать объекты, представляющие контейнеры и BLOB-объекты, хранящиеся в службе хранилища BLOB-объектов. Следующий код создает объект **CloudBlobClient** с помощью объекта учетной записи хранения, полученной выше:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### Зависимости ODataLib

Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF. Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet. Мы используем пакеты ODataLib [OData][OData], [Edm][Edm] и [Spatial][Spatial].

## <a name="create-container"> </a><span class="short-header">Создание контейнера</span>Практическое руководство: Создание контейнера

Все BLOB-объекты хранилища содержатся в контейнере. С помощью объекта **CloudBlobClient** можно получить ссылку на контейнер, который необходимо использовать. Контейнер можно создать, если он не существует:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

По умолчанию новый контейнер является закрытым, и вам необходимо указать ключ доступа к хранилищу, чтобы загрузить BLOB-объекты из этого контейнера. Чтобы сделать файлы в этом контейнере доступными для всех пользователей, сделайте контейнер открытым, используя следующий код:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob }); 

Любой пользователь в Интернете может видеть BLOB-объекты в открытом контейнер, но изменить или удалить их можно только при наличии ключа доступа.

## <a name="upload-blob"> </a><span class="short-header">Отправка контейнера</span>Практическое руководство: Отправка BLOB-объекта в контейнер

Хранилище BLOB-объектов Azure поддерживает блочные и страничные BLOB-объекты. В большинстве случаев рекомендуется использовать блочные BLOB-объекты.

Для отправки файла в блочный BLOB-объект получите ссылку на контейнер и используйте ее для получения ссылки на блочный BLOB-объект. Получив ссылку, вы можете отправить в него любой поток данных с помощью метода **UploadFromStream**. Эта операция создает BLOB-объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## <a name="list-blob"> </a><span class="short-header">Перечисление BLOB-объектов в контейнере</span>Практическое руководство: Перечисление BLOB-объектов в контейнере

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. Затем  можно использовать метод **ListBlobs** контейнера, чтобы извлечь BLOB-объекты и (или) каталоги в нем.  Для доступа к широкому набору свойств и методов возвращаемого объекта **IListBlobItem** необходимо преобразовать его в объект **CloudBlockBlob**, **CloudPageBlob** или **CloudBlobDirectory**. Если тип неизвестен, можно использовать проверку типов, чтобы определить нужный тип. Следующий код демонстрирует, как получить и вывести URI каждого элемента в контейнере `photos`:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client. 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
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

Как показано выше, служба BLOB-объектов также использует концепцию каталогов внутри контейнеров. Таким образом, можно организовать BLOB-объекты в структуре, похожей на папки. Например, рассмотрим следующий набор блочных BLOB-объектов в контейнере с именем  `photos`:

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

При необходимости можно установить для параметра **UseFlatBlobListing** метода **ListBlobs** значение **true**. Это приведет к возвращению каждого BLOB-объекта в виде **CloudBlockBlob**  независимо от каталога. Здесь вызывается метод **ListBlobs**:

    // Loop over items within the container and output the length and URI.
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

Дополнительные сведения см. в описании метода [CloudBlobContainer.ListBlobs][CloudBlobContainer.ListBlobs].

## <a name="download-blobs"> </a><span class="short-header">Загрузка BLOB-объектов</span>Практическое руководство: Загрузка BLOB-объектов

Для загрузки BLOB-объектов сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод **DownloadToStream**. В следующем примере метод **DownloadToStream** используется для переноса  содержимого BLOB-объекта в объект stream, который затем можно сохранить в локальном файле.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Можно также использовать метод **DownloadToStream**, чтобы загрузить содержимое BLOB-объекта как текстовую строку.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"> </a><span class="short-header">Удаление BLOB-объектов</span>Практическое руководство: Удаление blob-объектов

Для удаления BLOB-объекта сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод **Удалить**

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 

## <a name="next-steps"></a><span class="short-header">Дальнейшие действия</span>Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   Дополнительные сведения о доступных API-интерфейсах см. в справочной документации по службе BLOB-объектов:
    -   [Справочник по клиентской библиотеке хранения для .NET][Справочник по клиентской библиотеке хранения для .NET]
    -   [Справочник по REST API][Справочник по REST API]
-   Дополнительные сведения о более сложных задачах, которые можно выполнить с хранилищем Azure, см. в статье [Хранение данных и доступ к ним в Azure][Хранение данных и доступ к ним в Azure].
-   Дополнительные сведения о работе с хранилищем Azure в серверных процессах для веб-сайтов Azure см. в статье [Начиная работу с пакетом Azure WebJobs SDK][Начиная работу с пакетом Azure WebJobs SDK].
-   Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
    -   Использование [Табличного хранилища][Табличного хранилища] для хранения структурированных данных.
    -   Использование [Хранилища очередей][Хранилища очередей] для хранения неструктурированных данных.
    -   Использование [Базы данных SQL][Базы данных SQL] для хранения реляционных данных.

</p>

  [Дальнейшие действия]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Пакета Azure SDK для .NET]: /ru-ru/downloads/
  [Практическое руководство. Программный доступ к хранилищу BLOB-объектов]: #configure-access
  [Что такое хранилище BLOB-объектов]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Настройка строки подключения к хранилищу]: #setup-connection-string
  [Практическое руководство. Создание контейнера]: #create-container
  [Практическое руководство. Отправка BLOB-объекта в контейнер]: #upload-blob
  [Практическое руководство. Перечисление BLOB-объектов в контейнере]: #list-blob
  [Практическое руководство. Загрузка BLOB-объектов]: #download-blobs
  [Практическое руководство. Удаление blob-объектов]: #delete-blobs
  [руководство-блоб-объект-хранилище]: ../includes/howto-blob-storage.md
  [создание-хранилище-учетная запись]: ../includes/create-storage-account.md
  [хранилище-настройка-подключение-строка]: ../includes/storage-configure-connection-string.md
  [Центра разработчиков .NET]: http://www.windowsazure.com/ru-ru/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [Справочник по клиентской библиотеке хранения для .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Справочник по REST API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Начиная работу с пакетом Azure WebJobs SDK]: /ru-ru/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Табличного хранилища]: /ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/
  [Хранилища очередей]: /ru-ru/documentation/articles/storage-dotnet-how-to-use-queues/
  [Базы данных SQL]: /ru-ru/documentation/articles/sql-database-dotnet-how-to-use/

