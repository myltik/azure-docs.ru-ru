<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Использование хранилища BLOB-объектов из Java

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Microsoft Azure. Примеры написаны на Java и используют [Пакет SDK хранилища Azure для Java][Пакет SDK хранилища Azure для Java]. Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия][Дальнейшие действия].

Примечание. Пакет SDK доступен разработчикам, использующим хранилище Azure на устройствах Android. Дополнительные сведения см. в разделе [Пакет SDK хранилища Azure для Android][Пакет SDK хранилища Azure для Android].

## <a name="Contents"> </a> Оглавление

-   [Что такое хранилище BLOB-объектов][Что такое хранилище BLOB-объектов]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]
-   [Создание приложения Java][Создание приложения Java]
-   [Настройка приложения для доступа к хранилищу BLOB-объектов][Настройка приложения для доступа к хранилищу BLOB-объектов]
-   [Настройка строки подключения к хранилищу Azure][Настройка строки подключения к хранилищу Azure]
-   [Практическое руководство. Создание контейнера][Практическое руководство. Создание контейнера]
-   [Практическое руководство. Отправка BLOB-объекта в контейнер][Практическое руководство. Отправка BLOB-объекта в контейнер]
-   [Практическое руководство. Перечисление BLOB-объектов в контейнере][Практическое руководство. Перечисление BLOB-объектов в контейнере]
-   [Практическое руководство. Загрузка blob-объектов][Практическое руководство. Загрузка blob-объектов]
-   [Практическое руководство. Удаление blob-объектов][Практическое руководство. Удаление blob-объектов]
-   [Практическое руководство. Удаление контейнера blob-объектов][Практическое руководство. Удаление контейнера blob-объектов]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-блоб-объект-хранилище](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>Создание учетной записи хранения Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Создание приложения Java

В этом руководстве будут использоваться компоненты хранилища, которые могут быть вызваны локально в приложении Java или в коде, работающем в веб-роли или рабочей роли в Azure.

Чтобы это сделать, необходимо установить комплект разработчика Java (JDK) и создать учетную запись Azure в подписке Azure. После того, как это будет сделано, необходимо убедиться, что ваша система разработки отвечает минимальным требованиям и зависимостям, указанным в репозитории [Пакет SDK хранилища Azure для Java][Пакет SDK хранилища Azure для Java] в GitHub. Если ваша система отвечает указанным требованиям можно приступить к выполнению инструкций по загрузке библиотек хранилища Azure для Java из репозитория и их установке на своей системе. После завершение этих задач вы сможете приступить к созданию приложения Java с использованием примеров из данной статьи.

## <a name="ConfigureStorage"> </a>Настройка приложения для доступа к хранилищу BLOB-объектов

Если нужно использовать API-интерфейсы Azure для доступа к BLOB-объектам, добавьте следующие инструкции импорта в верхнюю часть файла Java:

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## <a name="ConnectionString"> </a>Настройка строки подключения к хранилищу Azure

Клиент хранилища Azure использует строку подключения с целью
 хранения конечных точек и учетных данных для доступа к службам управления данными. При работе в клиентском приложении необходимо указать для хранилища строку подключения в следующем формате, используя имя своей учетной записи хранения и первичный ключ доступа для учетной записи хранения, указанные на портале управления значениями *AccountName* и *AccountKey*. В этом примере показано, как объявить статическое поле для размещения строки подключения:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Если приложение выполняется в роли на платформе Microsoft Azure, эта строка может храниться в файле конфигурации службы *ServiceConfiguration.cscfg*, для доступа к которой можно использовать вызов метода **RoleEnvironment.getConfigurationSettings**. Ниже приведен пример получения строки подключения из элемента **Setting** с именем *StorageConnectionString* в файле конфигурации службы:

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

В приведенных ниже примерах предполагается, что вы использовали одно из этих двух определений для получения строки подключения к хранилищу.

## <a name="CreateContainer"> </a>Практическое руководство. Создание контейнера

Объект CloudQueueClient позволяет ссылаться на объекты контейнеров и BLOB-объектов. Следующий код создает объект **CloudBlobClient**. (Примечание: Существуют также другие способы создания объектов **CloudStorageAccount**. Дополнительную информацию см. в разделе **CloudStorageAccount** статьи [Справочник по пакету SDK для клиента хранилища Azure][Справочник по пакету SDK для клиента хранилища Azure].)

Все BLOB-объекты содержатся в контейнере. С помощью объекта **CloudBlobClient** получите ссылку контейнер, который собираетесь использовать. Если контейнер не существует, его можно создать с помощью метода **createIfNotExist**, который в противном случае возвратит существующий контейнер. По умолчанию новый контейнер является закрытым, поэтому необходимо указать ключ доступа к хранилищу (как делалось раньше), чтобы загрузить BLOB-объекты из этого контейнера.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Get a reference to a container.
       // The container name must be lower case
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Create the container if it does not exist.
        container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

### Необязательно: Настройка контейнера для открытого доступа

Разрешения контейнера по умолчанию настроены для закрытого доступа. Однако, можно легко настроить разрешения контейнера на открытый доступ с правами только на чтение для всех пользователей в Интернете.

    // Create a permissions object.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container.
    container.uploadPermissions(containerPermissions);

## <a name="UploadBlob"> </a>Практическое руководство. Отправка BLOB-объекта в контейнер

Для отправки файла в BLOB-объект получите ссылку на контейнер и используйте ее для получения ссылки на BLOB-объект. Получив ссылку на BLOB-объект, можно отправить в него любой поток данных, вызывая передачу по ссылке для BLOB-объекта. Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует. Ниже приведен соответствующий пример кода, в котором предполагается,что контейнер уже создан.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

        // Create or overwrite the "myimage.jpg" blob with contents from a local file.
        CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
        File source = new File(filePath);
        blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="ListBlobs"> </a>Практическое руководство. Перечисление BLOB-объектов в контейнере

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер, как при отправке BLOB-объекта. Можно использовать метод **listBlobs**контейнера с циклом **for**. Следующий код выводит на консоль URI каждого BLOB-объекта в контейнере.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Loop over blobs within the container and output the URI to each of them.
        for (ListBlobItem blobItem : container.listBlobs()) {
           System.out.println(blobItem.getUri());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Служба BLOB-объектов также использует концепцию каталогов внутри контейнеров. Таким образом, можно организовать BLOB-объекты в структуре, похожей на папки.

Например, можно использовать контейнер с именем "photos", в который можно отправить BLOB-объекты с именами "rootphoto1", "2010/photo1", "2010/photo2" и "2011/photo1". При этом в контейнере "photos" будут созданы виртуальные каталоги "2010" и "2011". При вызове метода **ListBlobs** в контейнере "photos" (как в примере выше) возвращаемая коллекция будет содержать объекты **CloudBlobDirectory** и **CloudBlob**, представляющие собой каталоги и BLOB-объекты верхнего уровня. В этом случае будут возвращены каталоги "2010" и "2011", а также "rootphoto1". Чтобы различать эти объекты, можно использовать оператор **instanceof**.

При необходимости можно передать параметры в метод **listBlobs**, установив для параметра **useFlatBlobListing** значение "true". Это приведет к возвращению каждого BLOB-объекта независимо от каталога. Для получения дополнительной  информации см. **CloudBlobContainer.listBlobs** в [Справочник по пакету SDK для клиента хранилища Azure][Справочник по пакету SDK для клиента хранилища Azure].

## <a name="DownloadBlob"> </a>Практическое руководство. Загрузка blob-объектов

Для загрузки BLOB-объектов выполните те же действия, что и для отправки BLOB-объекта, чтобы получить ссылку на BLOB-объект. В приведенном примере отправки вызывалась передача BLOB-объекта. В следующем примере вызовите загрузку для передачи содержимого BLOB-объекта в потоковый объект, например в **FileOutputStream**, который можно использовать для сохранения BLOB-объекта в локальном файле.

    try
    {
        // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Loop through each blob item in the container.
       for (ListBlobItem blobItem : container.listBlobs()) {
           // If the item is a blob, not a virtual directory.
           if (blobItem instanceof CloudBlob) {
               // Download the item and save it to a file with the same name.
                CloudBlob blob = (CloudBlob) blobItem;
                blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteBlob"> </a>Практическое руководство. Удаление BLOB-объекта

Чтобы удалить BLOB-объект, получите ссылку на BLOB-объект и вызовите метод **deleteifExits**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Retrieve reference to a blob named "myimage.jpg".
       CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

       // Delete the blob.
       blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteContainer"> </a>Практическое руководство. Удаление контейнера blob-объектов

Наконец, для удаления контейнера BLOB-объектов нужно получить ссылку на контейнер BLOB-объектов, а затем вызвать метод **deleteIfExists**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Delete the blob container.
       container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   [Пакет Azure SDK для Java][Пакет SDK хранилища Azure для Java]
-   [Справочник по пакету SDK для клиента хранилища Azure][Справочник по пакету SDK для клиента хранилища Azure]
-   [REST API хранилища Azure][REST API хранилища Azure]
-   [Блог команды разработчиков хранилища Azure][Блог команды разработчиков хранилища Azure]

  [Пакет SDK хранилища Azure для Java]: https://github.com/azure/azure-storage-java
  [Дальнейшие действия]: #NextSteps
  [Пакет SDK хранилища Azure для Android]: https://github.com/azure/azure-storage-android
  [Что такое хранилище BLOB-объектов]: #what-is
  [Основные понятия]: #Concepts
  [Создание учетной записи хранения Azure]: #CreateAccount
  [Создание приложения Java]: #CreateApplication
  [Настройка приложения для доступа к хранилищу BLOB-объектов]: #ConfigureStorage
  [Настройка строки подключения к хранилищу Azure]: #ConnectionString
  [Практическое руководство. Создание контейнера]: #CreateContainer
  [Практическое руководство. Отправка BLOB-объекта в контейнер]: #UploadBlob
  [Практическое руководство. Перечисление BLOB-объектов в контейнере]: #ListBlobs
  [Практическое руководство. Загрузка blob-объектов]: #DownloadBlob
  [Практическое руководство. Удаление blob-объектов]: #DeleteBlob
  [Практическое руководство. Удаление контейнера blob-объектов]: #DeleteContainer
  [руководство-блоб-объект-хранилище]: ../includes/howto-blob-storage.md
  [создание-хранилище-учетная запись]: ../includes/create-storage-account.md
  [Справочник по пакету SDK для клиента хранилища Azure]: http://dl.windowsazure.com/storage/javadoc/
  [REST API хранилища Azure]: http://msdn.microsoft.com/ru-ru/library/azure/gg433040.aspx
  [Блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
