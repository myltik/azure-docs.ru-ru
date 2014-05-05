<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Служба BLOB-объектов" pageTitle="Использование хранилища BLOB-объектов (Java) | Microsoft Azure" metaKeywords="Начало работы с BLOB-объектом Azure, неструктурированные данные Azure, неструктурированное хранилище Azure, BLOB-объект Azure, хранилище BLOB-объектов Azure, BLOB-объект Azure Java" description="Узнайте, как использовать службу BLOB-объектов Azure для передачи, загрузки, перечисления и удаления содержания BLOB-объектов. Примеры, написанные на языке Java." metaCanonical="" services="storage" documentationCenter="Java" title="Использование хранилища BLOB-объектов из Java" authors="" solutions="" manager="" editor="" />




# Использование хранилища BLOB-объектов из Java

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища BLOB-объектов Azure. Примеры написаны на Java и используют [Пакет Azure SDK для Java][]. Здесь описаны такие сценарии, как **отправка**, **перечисление**, **загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия](#NextSteps).

## <a name="Contents"> </a>Оглавление

* [Что такое хранилище BLOB-объектов](#what-is)
* [Основные понятия](#Concepts)
* [Создание учетной записи хранения Azure](#CreateAccount)
* [Создание приложения Java](#CreateApplication)
* [Настройка приложения для доступа к хранилищу BLOB-объектов](#ConfigureStorage)
* [Настройка строки подключения к хранилищу Azure](#ConnectionString)
* [Практическое руководство. Создание контейнера](#CreateContainer)
* [Практическое руководство. Отправка BLOB-объекта в контейнер](#UploadBlob)
* [Практическое руководство. Перечисление BLOB-объектов в контейнере](#ListBlobs)
* [Практическое руководство. Загрузка BLOB-объекта](#DownloadBlob)
* [Практическое руководство. Удаление BLOB-объекта](#DeleteBlob)
* [Практическое руководство. Удаление контейнера BLOB-объектов](#DeleteContainer)
* [Дальнейшие действия](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Создание приложения Java

В этом руководстве будут использоваться компоненты хранилища, которые могут быть вызваны локально в приложении Java или в коде, работающем в веб-роли или рабочей роли в Azure. Предполагается, что комплект разработчика Java (JDK) уже загружен и установлен и что вы, следуя инструкциям, приведенным в статье [Загрузка пакета Azure SDK для Java][Azure SDK for Java], установили библиотеки Azure для Java и пакет Azure SDK и создали учетную запись хранения Azure в своей подписке Azure.

Для создания приложения можно использовать любые средства разработки, включая Блокнот. Все, что нужно — это возможность компилировать проект Java и сослаться на библиотеки Azure для Java.

## <a name="ConfigureStorage"> </a>Настройка приложения для доступа к хранилищу BLOB-объектов

Если нужно использовать API-интерфейсы Azure для доступа к BLOB-объектам, добавьте следующие инструкции импорта в верхнюю часть файла Java:

    // Include the following imports to use blob APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

## <a name="ConnectionString"> </a>Настройка строки подключения к хранилищу Azure

Клиент хранилища Azure использует строку подключения с целью хранения конечных точек и учетных данных для доступа к службам управления данными. При работе в клиентском приложении необходимо указать для хранилища строку подключения в следующем формате, используя имя своей учетной записи хранения и первичный ключ доступа для учетной записи хранения, указанные на портале управления значениями *AccountName* и *AccountKey*. В этом примере показано, как объявить статическое поле для размещения строки подключения:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Если приложение выполняется в роли Azure, эта строка может храниться в файле конфигурации службы, ServiceConfiguration.cscfg, и для доступа к ней можно использовать вызов
метода **RoleEnvironment.getConfigurationSettings**. Ниже приведен пример получения строки подключения из элемента **Setting** с именем
*StorageConnectionString* в файле конфигурации службы:

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

## <a name="CreateContainer"> </a>Практическое руководство. Создание контейнера

Объект CloudQueueClient позволяет ссылаться на объекты контейнеров и BLOB-объектов. Следующий код создает объект **CloudBlobClient**.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

Все BLOB-объекты содержатся в контейнере. С помощью объекта **CloudBlobClient** получите
ссылку на нужный контейнер. Если контейнер не существует, его можно создать с помощью метода **createIfNotExist**, который в противном случае возвратит существующий контейнер. По умолчанию новый контейнер является закрытым, поэтому необходимо указать
ключ доступа к хранилищу (как делалось раньше), чтобы загрузить BLOB-объекты из этого
контейнера.

    // Get a reference to a container
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist
    container.createIfNotExist();

Если нужно сделать файлы общедоступными, можно определить разрешения контейнера.

    // Create a permissions object
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container
    container.uploadPermissions(containerPermissions);

Любой пользователь в Интернете может видеть BLOB-объекты в открытом контейнере, но при открытом доступе разрешается только чтение.

## <a name="UploadBlob"> </a>Практическое руководство. Отправка BLOB-объекта в контейнер

Для отправки файла в BLOB-объект получите ссылку на контейнер и используйте ее для получения
ссылки на BLOB-объект. Получив ссылку на BLOB-объект, можно отправить в него любой поток данных, вызывая передачу по ссылке для BLOB-объекта. Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует. Ниже приведен соответствующий пример кода, в котором предполагается,
что контейнер уже создан.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create or overwrite the "myimage.jpg" blob with contents from a local file
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File("c:\\myimages\\myimage.jpg");
    blob.upload(new FileInputStream(source), source.length());

## <a name="ListBlobs"> </a>Практическое руководство. Перечисление BLOB-объектов в контейнере

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер, как при отправке BLOB-объекта. Можно использовать метод **listBlobs**
контейнера с циклом for. Следующий код выводит на консоль URI каждого BLOB-объекта в контейнере.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }

Служба BLOB-объектов также использует концепцию каталогов
внутри контейнеров. Таким образом можно организовать BLOB-объекты в структуре, похожую на папки.

Например, можно использовать контейнер с именем "photos", в
который можно отправить BLOB-объекты с именами
"rootphoto1", "2010/photo1", "2010/photo2" и "2011/photo1". При этом в контейнере "photos" будут созданы виртуальные каталоги "2010" и "2011". При вызове метода **listBlobs** для контейнера "photos" возвращаемая коллекция будет содержать
объекты **CloudBlobDirectory** и **CloudBlob**, представляющие каталоги и BLOB-объекты, содержащиеся на верхнем уровне. В этом случае будут возвращены каталоги "2010" и "2011", а также "rootphoto1". Чтобы различать эти объекты, можно использовать оператор **instanceof**.

При необходимости можно передать параметры в метод **listBlobs**, установив для параметра **useFlatBlobListing** значение "true". Это приведет к возвращению каждого BLOB-объекта независимо от каталога. Дополнительные сведения см. в описании CloudBlobContainer.listBlobs в документации Javadocs.

## <a name="DownloadBlob"> </a>Практическое руководство. Загрузка BLOB-объекта

Для загрузки BLOB-объектов выполните те же действия, что и для отправки BLOB-объекта, чтобы получить ссылку на BLOB-объект. В приведенном примере отправки вызывалась передача BLOB-объекта. В следующем примере вызовите загрузку для передачи содержимого BLOB-объекта в потоковый объект, например в **FileOutputStream**, который можно использовать для сохранения BLOB-объекта в локальном файле.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // For each item in the container
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream(blob.getName()));
        }
    }

## <a name="DeleteBlob"> </a>Практическое руководство. Удаление BLOB-объекта

Чтобы удалить BLOB-объект, получите ссылку на BLOB-объект и вызовите метод **delete**.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg"
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob
    blob.delete();

## <a name="DeleteContainer"> </a>Практическое руководство. Удаление контейнера BLOB-объектов

Наконец, для удаления контейнера BLOB-объектов нужно получить ссылку на контейнер BLOB-объектов, а затем вызвать метод delete.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container
    container.delete();

## <a name="NextSteps"> </a>Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Windows
    Azure]
-   Посетите блог команды разработчиков хранилища Azure <http://blogs.msdn.com/b/windowsazurestorage/>


[Пакет Azure SDK для Java]: http://www.windowsazure.com/ru-ru/develop/java/
[Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx

