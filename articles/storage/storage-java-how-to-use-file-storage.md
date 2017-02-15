---
title: "Как использовать хранилище файлов из Java | Документация Майкрософт"
description: "Узнайте, как использовать службу хранилища файлов Azure для отправки, загрузки, отображения и удаления файлов. Примеры кода написаны на Java."
services: storage
documentationcenter: java
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 60d4bcafa51a6eedea06901a0557d13b4e85ec7a


---
# <a name="how-to-use-file-storage-from-java"></a>Использование табличного хранилища из Java
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Обзор
В этом руководстве вы узнаете, как выполнять основные операции в службе хранилища файлов Microsoft Azure. С помощью примеров на языке Java вы узнаете, как создавать общие папки и каталоги, отправлять и удалять файлы, а также получать список файлов. Если вы не знакомы со службой хранилища файлов Microsoft Azure, рекомендуем изучить информацию, изложенную в последующих разделах. Это будет очень полезно для понимания примеров программ.

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Создание приложения Java
Для создания примеров вам потребуется комплект разработчика Java (JDK) и [Пакет SDK для службы хранилища Azure для Java][]. Вам также необходимо создать учетную запись хранилища Azure.

## <a name="setup-your-application-to-use-file-storage"></a>Настройка приложения для использования хранилища файлов
Чтобы использовать API-интерфейсы хранилища Azure, добавьте следующую инструкцию в верхнюю часть файла Java, откуда планируется осуществлять доступ к службе хранилища.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Настройка строки подключения к службе хранилища Azure
Чтобы начать работу с хранилищем файлов, необходимо подключиться к вашей учетной записи хранилища Azure. Для начала потребуется настроить строку подключения, которая будет использоваться для подключения к учетной записи хранилища. Для этого определим статическую переменную.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Замените your_storage_account_name и your_storage_account_key на фактические значения для вашей учетной записи.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Подключение к учетной записи хранилища Azure
Чтобы подключиться к учетной записи хранения, необходимо использовать объект **CloudStorageAccount**, передав строку подключения для метода **parse**.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** вызывает прерывание InvalidKeyException, поэтому необходимо поместить его в блок try-catch.

## <a name="how-to-create-a-share"></a>Как создать общую папку
Все файлы и каталоги в хранилище файлов находятся в контейнере с именем **Share**. Учетная запись хранения может иметь столько общих папок, насколько позволяет емкость вашей учетной записи. Чтобы получить доступ к общей папке и ее содержимому, необходимо использовать клиент хранилища файлов.

```java
// Create the file storage client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

С помощью клиента хранилища файлов можно затем получить ссылку на общий ресурс.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Чтобы создать общий ресурс, используйте метод **createIfNotExists** объекта CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

На этом этапе объект **share** содержит ссылку на общую папку с именем **sampleshare**.

## <a name="how-to-upload-a-file"></a>Как отправить файл
Общая папка хранилища файлов Azure содержит по крайней мере, корневой каталог, где могут размещаться файлы. В этом разделе вы узнаете, как отправить файл из локального хранилища в корневой каталог общего ресурса.

Первым шагом при отправке файла является получение ссылки на каталог, где файл будет находиться. Это делается путем вызова метода **getRootDirectoryReference** объекта общей папки.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Теперь, когда у вас имеется ссылка на корневой каталог общего ресурса, вы можете отправить туда файл с помощью следующего кода.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="how-to-create-a-directory"></a>Как создать каталог
Вы также можете организовать хранилище, помещая файлы в подкаталоги вместо их размещения в корневом каталоге. Служба хранилища файлов Azure позволяет создавать столько каталогов, сколько может позволить ваша учетная запись. В следующем примере кода в корневом каталоге создается вложенный каталог с именем **sampledir** .

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="how-to-list-files-and-directories-in-a-share"></a>Как получить список файлов и каталогов в общей папке
Получить список файлов и каталогов в общей папке довольно просто, вызвав метод **listFilesAndDirectories** по ссылке CloudFileDirectory. Метод возвращает список объектов ListFileItem, с которым вы можете производить дальнейшие действия. Например следующий код отображает все файлы и каталоги, содержащиеся в корневом каталоге.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="how-to-download-a-file"></a>Как скачать файл
Одной из наиболее частых операций, которые выполняются над хранилищем файлов, является операция скачивания файлов. В следующем примере происходит скачивание файла SampleFile.txt с последующим отображением его содержимого.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="how-to-delete-a-file"></a>Как удалить файл
Другой распространенной операцией над хранилищем файлов является удаление файлов. Следующий пример программы удаляет файл с именем SampleFile.txt, хранящийся в каталоге с именем **sampledir**.

```java
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
```

## <a name="how-to-delete-a-directory"></a>Как удалить каталог
Удаление каталога является достаточно простой задачей, однако следует отметить, что нельзя удалить каталог, по-прежнему содержащий файлы или другие каталоги.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="how-to-delete-a-share"></a>Как удалить общую папку
Удалить общую папку можно путем вызова метода **deleteIfExists** объекта CloudFileShare. Ниже приведен пример кода, который выполняет это действие.

```java
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
```

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите узнать больше о других API-интерфейсах Azure, пожалуйста перейдите по следующим ссылкам.

* [Центр разработчика Java](http://azure.microsoft.com/develop/java/)
* [Пакет SDK для службы хранилища Azure для Java](https://github.com/azure/azure-storage-java)
* [Microsoft Azure Storage SDK for Android](https://github.com/azure/azure-storage-android)
* [справочнике по пакету SDK для клиента службы хранилища Azure](http://dl.windowsazure.com/storage/javadoc/)
* [API-интерфейс REST служб хранилища Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Блог рабочей группы службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* [Приступая к работе со служебной программой командной строки AzCopy](storage-use-azcopy.md)




<!--HONumber=Nov16_HO3-->


