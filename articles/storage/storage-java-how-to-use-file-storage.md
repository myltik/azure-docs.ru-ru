<properties 
	pageTitle="Использование хранилища файлов из Java в Microsoft Azure" 
	description="Узнайте, как использовать службу хранилища файлов Azure для отправки, загрузки, отображения и удаления файлов. Примеры кода написаны на Java." 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe" />

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="v-dedomi"/>

# Использование табличного хранилища из Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## Обзор

В этом руководстве вы узнаете, как выполнять основные операции в службе хранилища файлов Microsoft Azure. С помощью примеров на языке Java вы узнаете, как создавать общие папки и каталоги, отправлять, получать список и удалять файлы. Если вы не знакомы со службой хранилища файлов Microsoft Azure, рекомендуем изучить информацию, изложенную в последующих разделах. Это будет очень полезно для понимания примеров программ.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание приложения Java

Для создания примеров вам потребуется комплект разработчика Java (JDK) и [Пакет SDK для службы хранилища Azure для Java][]. Вам также необходимо создать учетную запись хранилища Azure.

## Настройка приложения для использования хранилища файлов

Чтобы использовать API-интерфейсы хранилища Azure, добавьте следующую инструкцию в верхнюю часть файла Java, откуда планируется осуществлять доступ к службе хранилища.

	// Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
	import com.microsoft.azure.storage.file.*;

## Настройка строки подключения к службе хранилища Azure

Чтобы начать работу с хранилищем файлов, необходимо подключиться к вашей учетной записи хранилища Azure. Для начала потребуется настроить строку подключения, которая будет использоваться для подключения к учетной записи хранилища. Для этого определим статическую переменную.

	// Configure the connection-string with your values
	public static final String storageConnectionString = 
	    "DefaultEndpointsProtocol=http;" + 
	    "AccountName=your_storage_account_name;" + 
	    "AccountKey=your_storage_account_key";

> [AZURE.NOTE]Замените your\_storage\_account\_name и your\_storage\_account\_key на фактические значения для вашей учетной записи.

## Подключение к учетной записи хранилища Azure

Чтобы подключиться к учетной записи хранилища, необходимо использовать объект **CloudStorageAccount**, передав строку подключения для метода **Parse**.

	// Use the CloudStorageAccount object to connect to your storage account
	try {
		CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
	} catch (InvalidKeyException invalidKey) {
		// Handle the exception
	}

**CloudStorageAccount.parse** вызывает прерывание InvalidKeyException, поэтому необходимо поместить его в блок try-catch.

## Как создать общую папку

Все файлы и каталоги в хранилище файлов находятся в контейнере с именем **Share**. Учетная запись хранения может иметь столько общих папок, насколько позволяет емкость вашей учетной записи. Чтобы получить доступ к общей папке и ее содержимому, необходимо использовать клиент хранилища файлов.

	// Create the file storage client.
	CloudFileClient fileClient = storageAccount.createCloudFileClient();

С помощью клиента хранилища файлов можно затем получить ссылку на общий ресурс.

	// Get a reference to the file share
	CloudFileShare share = fileClient.getShareReference("sampleshare"); 

Чтобы создать общий ресурс, используйте метод **createIfNotExists** объекта CloudFileShare.

	if (share.createIfNotExists()) {
		System.out.println("New share created");
	}

На этом этапе объект **Share** содержит ссылку на общую папку с именем **sampleshare**.

## Как отправить файл

Общая папка хранилища файлов Azure содержит по крайней мере, корневой каталог, где могут размещаться файлы. В этом разделе вы узнаете, как отправить файл из локального хранилища в корневой каталог общего ресурса.

Первым шагом при отправке файла является получение ссылки на каталог, где файл будет находиться. Это делается путем вызова метода **getRootDirectoryReference** объекта общей папки.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

Теперь, когда у вас имеется ссылка на корневой каталог общего ресурса, вы можете отправить туда файл с помощью следующего кода.

	// Define the path to a local file.
	final String filePath = "C:\\temp\\Readme.txt";

	CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
	cloudFile.uploadFromFile(filePath);

## Как создать каталог

Вы также можете организовать хранилище, помещая файлы в подкаталоги вместо их размещения в корневом каталоге. Служба хранилища файлов Azure позволяет создавать столько каталогов, сколько может позволить ваша учетная запись. В следующем примере кода в корневом каталоге создается вложенный каталог с именем **sampledir**.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the sampledir directory 
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");
			    
	if (sampleDir.createIfNotExists()) {
		System.out.println("sampledir created");
	} else {
		System.out.println("sampledir already exists");
	}

## Как получить список файлов и каталогов в общей папке

Получить список файлов и каталогов в общей папке довольно просто, вызвав метод **listFilesAndDirectories** по ссылке CloudFileDirectory. Метод возвращает список объектов ListFileItem, с которым вы можете производить дальнейшие действия. Например следующий код отображает все файлы и каталоги, содержащиеся в корневом каталоге.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();
		   
	for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
		System.out.println(fileItem.getUri());
	}


## Как скачать файл

Одной из наиболее частых операций, которые выполняются над хранилищем файлов, является операция скачивания файлов. В следующем примере происходит скачивание файла SampleFile.txt с последующим отображением его содержимого.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();
	
	//Get a reference to the directory that contains the file
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");
			    
	//Get a reference to the file you want to download
	CloudFile file = sampleDir.getFileReference("SampleFile.txt");

	//Write the contents of the file to the console.
	System.out.println(file.downloadText());

## Как удалить файл

Другой распространенной операцией над хранилищем файлов является удаление файлов. Следующий пример программы удаляет файл с именем SampleFile.txt, хранящийся в каталоге с именем **sampledir**.


	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory where the file to be deleted is in
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");
	
	String filename = "SampleFile.txt"
	CloudFile file;

	file = containerDir.getFileReference(filename)
	if ( file.deleteIfExists() ) {
		System.out.println(filename + " was deleted");
	}


## Как удалить каталог

Удаление каталога является достаточно простой задачей, однако следует отметить, что нельзя удалить каталог, по-прежнему содержащий файлы или другие каталоги.

	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory you want to delete
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");	

	// Delete the directory
	if ( containerDir.deleteIfExists() ) {
		System.out.println("Directory deleted");
	}


## Как удалить общую папку

Удалить общую папку можно путем вызова метода **deleteIfExists** объекта CloudFileShare. Ниже приведен пример кода, который выполняет это действие.

	try
	{
	    // Retrieve storage account from connection-string.
	    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	    // Create the file client.
	   CloudFileClient fileClient = storageAccount.createCloudFileClient();

	   // Get a reference to the file share
	   CloudFileShare share = fileClient.getShareReference("sampleshare");
	   
	   if (share.deleteIfExists()) {
		   System.out.println("sampleshare deleted");
	   } 
	} catch (Exception e) {
		e.printStackTrace();
	}

## Дальнейшие действия

Если вы хотите узнать больше о других API-интерфейсах Azure, пожалуйста перейдите по следующим ссылкам.

- [Пакет SDK для службы хранилища Azure для Java]
- [Справочник по пакету SDK для клиента хранилища Azure]
- [REST API службы хранилища Azure]
- [Блог рабочей группы службы хранилища Azure]

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Пакет SDK для службы хранилища Azure для Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Справочник по пакету SDK для клиента хранилища Azure]: http://dl.windowsazure.com/storage/javadoc/
[REST API службы хранилища Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
 

<!---HONumber=August15_HO6-->