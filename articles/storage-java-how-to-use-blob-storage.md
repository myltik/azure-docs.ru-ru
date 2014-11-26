<properties urlDisplayName="Blob Service" pageTitle="Использование хранилища BLOB-объектов (Java) - Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Использование хранилища BLOB-объектов из Java

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Microsoft Azure. Примеры написаны на Java и используют [Пакет SDK хранилища Azure для Java][Пакет SDK хранилища Azure для Java]. Здесь описаны такие сценарии, как **отправка**, **перечисление**, **загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия](#NextSteps).

Примечание. Пакет SDK доступен разработчикам, использующим хранилище Azure на устройствах Android. Дополнительные сведения см. в разделе [Пакет SDK хранилища Azure для Android][Пакет SDK хранилища Azure для Android]. 

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
* [Практическое руководство. Загрузка BLOB-объектов](#DownloadBlob)
* [Практическое руководство. Удаление BLOB-объектов](#DeleteBlob)
* [Практическое руководство. Удаление контейнера blob-объектов](#DeleteContainer)
* [Дальнейшие действия](#NextSteps)

[WACOM.INCLUDE [руководство-блоб-объект-хранилище](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Создание учетной записи хранения Azure</h2>

[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Создание приложения Java

В этом руководстве будут использоваться компоненты хранилища, которые могут быть вызваны локально в приложении Java или в коде, работающем в веб-роли или рабочей роли в Azure.

Чтобы это сделать, необходимо установить комплект разработчика Java (JDK) и создать учетную запись Azure в подписке Azure. После того, как это будет сделано, необходимо убедиться, что ваша система разработки отвечает минимальным требованиям и зависимостям, указанным в репозитории [Пакет SDK хранилища Azure для Java][Пакет SDK хранилища Azure для Java] в GitHub. Если ваша система отвечает указанным требованиям можно приступить к выполнению инструкций по загрузке библиотек хранилища Azure для Java из репозитория и их установке на своей системе. После завершение этих задач вы сможете приступить к созданию приложения Java с использованием примеров из данной статьи.

## <a name="ConfigureStorage"> </a>Настройка приложения для доступа к хранилищу BLOB-объектов

Если нужно использовать API-интерфейсы Azure для доступа к BLOB-объектам, добавьте следующие инструкции импорта в верхнюю часть файла Java:

    // Включите следующий импорт для интерфейсов API BLOB-объектов.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## <a name="ConnectionString"> </a>Настройка строки подключения к хранилищу Azure

Клиент хранилища Azure использует строку подключения с целью
хранения конечных точек и учетных данных для доступа к службам управления данными. При работе в клиентском приложении необходимо указать для хранилища строку подключения в следующем формате, используя имя своей учетной записи хранения и первичный ключ доступа для учетной записи хранения, указанные на портале управления значениями *AccountName* и *AccountKey*. В этом примере показано, как объявить статическое поле для размещения строки подключения:

    // Определить строку подключения с вашим значением
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Если приложение выполняется в роли Azure, эта строка может храниться в файле конфигурации службы, *ServiceConfiguration.cscfg*, и для доступа к ней можно использовать вызов метода **RoleEnvironment.getConfigurationSettings**. Ниже приведен пример получения строки подключения из элемента **Setting** с именем *StorageConnectionString* в файле конфигурации службы:

    // Получить учетную запись хранения из строки подключения.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

В приведенных ниже примерах предполагается, что вы использовали одно из этих двух определений для получения строки подключения к хранилищу.

## <a name="CreateContainer"> </a>Практическое руководство. Создание контейнера

Объект CloudQueueClient позволяет ссылаться на объекты контейнеров и BLOB-объектов. Следующий код создает объект **CloudBlobClient**. (Примечание. Существуют также другие способы создания объектов **CloudStorageAccount**. Дополнительную информацию см. в разделе **CloudStorageAccount** in the [Справочник по пакету SDK для клиента хранилища Azure].)

Все BLOB-объекты содержатся в контейнере. С помощью объекта **CloudBlobClient** получите ссылку контейнер, который собираетесь использовать. Если контейнер не существует, его можно создать с помощью метода **createIfNotExist**, который в противном случае возвратит существующий контейнер. По умолчанию новый контейнер является закрытым, поэтому вам необходимо указать ключ доступа к хранилищу (как в уже делали раньше), чтобы загрузить BLOB-объекты из этого контейнера.

	try
    {
        // Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент службы BLOB-объектов.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Получить ссылку на контейнер.
	   // Имя контейнера должно быть в нижнем регистре
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

	   // Создать контейнер, если он не существует.
    	container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

###Необязательно: Настройка контейнера для открытого доступа ###

Разрешения контейнера по умолчанию настроены для закрытого доступа. Однако, можно легко настроить разрешения контейнера на открытый доступ с правами только на чтение для всех пользователей в Интернете. 

    // Создание объекта разрешений.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Ввести открытый доступ в объект разрешения.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // задать разрешения на контейнер.
    container.uploadPermissions(containerPermissions);

## <a name="UploadBlob"> </a>Практическое руководство. Отправка BLOB-объекта в контейнер

Для отправки файла в BLOB-объект получите ссылку на контейнер и используйте ее для получения ссылки на BLOB-объект. Получив ссылку на BLOB-объект, можно отправить в него любой поток данных, вызывая передачу по ссылке для BLOB-объекта. Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует. Ниже приведен соответствующий пример кода, в котором предполагается,что контейнер уже создан.

	try
    {
        // Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент службы BLOB-объектов.
    	CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Получить ссылку на ранее созданный контейнер.
    	CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
        // Определить путь к локальному файлу.
        final String filePath = "C:\\myimages\\myimage.jpg";

    	// Создать или заменить BLOB-объект "myimage.jpg" содержимым из локального файла.
    	CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    	File source = new File(filePath);
    	blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="ListBlobs"> </a>Практическое руководство. Перечисление BLOB-объектов в контейнере

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер, как при отправке BLOB-объекта. Можно использовать метод **listBlobs** контейнера с циклом **for**. Следующий код выводит на консоль URI каждого BLOB-объекта в контейнере.

	try
    {
        // Получить учетную запись хранения из строки подключения.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Создать клиент службы BLOB-объектов.
    	CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    	// Получить ссылку на ранее созданный контейнер.
    	CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
    	// Выполнение цикла по BLOB-объектам в контейнере и вывод URI для каждого из них.
    	for (ListBlobItem blobItem : container.listBlobs()) {
	       System.out.println(blobItem.getUri());
	   }
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

Служба BLOB-объектов также использует концепцию каталогов внутри контейнеров. Таким образом, можно организовать BLOB-объекты в структуре, похожей на папки.

Например, можно использовать контейнер с именем "photos", в который можно отправить BLOB-объекты с именами "rootphoto1", "2010/photo1", "2010/photo2" и "2011/photo1". При этом в контейнере "photos" будут созданы виртуальные каталоги "2010" и "2011". При вызове метода **ListBlobs** в контейнере "photos" (как в примере выше) возвращаемая коллекция будет содержать объекты **CloudBlobDirectory** и **CloudBlob**, представляющие собой каталоги и BLOB-объекты верхнего уровня. В этом случае будут возвращены каталоги "2010" и "2011", а также "rootphoto1". Чтобы различать эти объекты, можно использовать оператор **instanceof**.

При необходимости можно передать параметры в метод **listBlobs**, установив для
параметра **useFlatBlobListing** значение "true". Это приведет к
возвращению каждого BLOB-объекта независимо от каталога. Для получения дополнительной
информации см. **CloudBlobContainer.listBlobs** в разделе [Справочник по пакету SDK для клиента хранилища Azure].

## <a name="DownloadBlob"> </a>Практическое руководство. Загрузка blob-объектов

Для загрузки BLOB-объектов выполните те же действия, что и для отправки BLOB-объекта, чтобы получить ссылку на BLOB-объект. В приведенном примере отправки вызывалась передача BLOB-объекта. В следующем примере вызовите загрузку для передачи содержимого BLOB-объекта в потоковый объект, например в **FileOutputStream**, который можно использовать для сохранения BLOB-объекта в локальном файле.

    try
    {
    	// Получить учетную запись хранения из строки подключения.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Создать клиент службы BLOB-объектов.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Получить ссылку на ранее созданный контейнер.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // ПРойдитесь по всем BLOB-объектам в контейнере.
	   for (ListBlobItem blobItem : container.listBlobs()) {
	       // Если это BLOB-объект, а не виртуальный каталог.
	       if (blobItem instanceof CloudBlob) {
	           // Скачать объект и сохранить в файле с тем же именем.
    	        CloudBlob blob = (CloudBlob) blobItem;
    	        blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
    	    }
    	}
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="DeleteBlob"> </a>Практическое руководство. Удаление BLOB-объектов

Чтобы удалить BLOB-объект, получите ссылку на BLOB-объект и вызовите метод **deleteIfExists**.

    try
    {
	   // Получить учетную запись хранения из строки подключения.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Создать клиент службы BLOB-объектов.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Получить ссылку на ранее созданный контейнер.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Получить ссылку на BLOB-объект с именем "myimage.jpg".
	   CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

	   // Удалить BLOB-объект.
	   blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="DeleteContainer"> </a>Практическое руководство. Удаление контейнера blob-объектов

Наконец, для удаления контейнера BLOB-объектов нужно получить ссылку на контейнер BLOB-объектов, а затем
вызвать метод **deleteIfExists**.

    try
    {
	   // Получить учетную запись хранения из строки подключения.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Создать клиент службы BLOB-объектов.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Получить ссылку на ранее созданный контейнер.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Удалить контейнер blob-объектов
	   container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Вывод трассировки стека.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- [Пакет Azure SDK для Java]
- [Справочник по пакету SDK для клиента хранилища Azure]
- [REST API хранилища Azure]
- [Блог команды разработчиков хранилища Azure]

[Пакет Azure SDK для Java]: http://www.windowsazure.com/ru-ru/develop/java/
[Пакет SDK хранилища Azure для Java]: https://github.com/azure/azure-storage-java
[Пакет SDK хранилища Azure для Android]: https://github.com/azure/azure-storage-android
[Справочник по пакету SDK для клиента хранилища Azure]: http://dl.windowsazure.com/storage/javadoc/
[REST API хранилища Azure]: http://msdn.microsoft.com/ru-ru/library/azure/gg433040.aspx
[Блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
