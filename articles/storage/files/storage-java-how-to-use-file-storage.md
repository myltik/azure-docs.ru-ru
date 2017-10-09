---
title: "Разработка для файлов Azure с использованием языка Java | Документы Майкрософт"
description: "Узнайте, как разрабатывать приложения и службы Java, использующие файлы Azure для хранения файлов данных."
services: storage
documentationcenter: java
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 192c4b5b89feca2a2e39c5e0670d05cc8868eb03
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="develop-for-azure-files-with-java"></a>Разработка для файлов Azure с использованием языка Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>О данном учебнике
В этом руководстве мы рассмотрим основы использования Java для разработки приложений и служб, использующих файлы Azure для хранения данных файлов. В рамках этого руководства мы создадим простое консольное приложение, а также покажем, как выполнять основные действия с Java и файлами Azure:

* Создание и удаление общих папок Azure.
* Создание и удаление каталогов.
* Перечисление файлов и каталогов в файловом ресурсе Azure.
* Передача, загрузка и удаление файлов.

> [!Note]  
> Так как к файлам Azure можно обращаться через SMB, вы можете создавать простые приложения, которые получают доступ к общим папкам файлов Azure с использованием стандартных классов ввода-вывода в Java. Из этой статьи вы узнаете, как создавать приложения на основе пакета SDK Java для службы хранилища Azure. Этот пакет SDK использует [API REST файлов Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) для взаимодействия с файлами Azure.

## <a name="create-a-java-application"></a>Создание приложения Java
Для создания примеров вам потребуется комплект разработчика Java (JDK) и [Пакет SDK для службы хранилища Azure для Java][]. Вам также необходимо создать учетную запись хранилища Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Настройка приложения для работы с файлами Azure
Чтобы использовать API-интерфейсы хранилища Azure, добавьте следующую инструкцию в верхнюю часть файла Java, откуда планируется осуществлять доступ к службе хранилища.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Настройка строки подключения к хранилищу Azure
Для использования файлов Azure необходимо подключиться к учетной записи хранения Azure. Для начала потребуется настроить строку подключения, которая будет использоваться для подключения к учетной записи хранения. Для этого определим статическую переменную.

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

## <a name="create-an-azure-file-share"></a>Создание файлового ресурса Azure
Все файлы и каталоги в файлах Azure находятся в контейнере, который называется **общей папкой**. Учетная запись хранения может иметь столько общих папок, насколько позволяет емкость вашей учетной записи. Чтобы получить доступ к общей папке и ее содержимому, необходимо использовать клиент файлов Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

С помощью клиента файлов Azure вы получите ссылку на общую папку.

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

## <a name="delete-an-azure-file-share"></a>Удаление общей папки Azure
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

## <a name="create-a-directory"></a>Создайте каталог
Вы также можете организовать хранилище, помещая файлы в подкаталоги вместо их размещения в корневом каталоге. Файлы Azure позволяют создать такое количество каталогов, которое допускается в вашей учетной записи. В следующем примере кода в корневом каталоге создается вложенный каталог с именем **sampledir** .

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

## <a name="delete-a-directory"></a>Удаление каталога
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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Перечисление файлов и каталогов в файловом ресурсе Azure
Получить список файлов и каталогов в общей папке довольно просто, вызвав метод **listFilesAndDirectories** по ссылке CloudFileDirectory. Метод возвращает список объектов ListFileItem, с которым вы можете производить дальнейшие действия. Например следующий код отображает все файлы и каталоги, содержащиеся в корневом каталоге.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Отправить файл.
Общая папка Azure содержит по меньшей мере один каталог для размещения файлов (корневой каталог). В этом разделе вы узнаете, как отправить файл из локального хранилища в корневой каталог общего ресурса.

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

## <a name="download-a-file"></a>Скачивание файла
Одной из наиболее частых операций, которые выполняются с файлами Azure, является скачивание файлов. В следующем примере происходит скачивание файла SampleFile.txt с последующим отображением его содержимого.

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

## <a name="delete-a-file"></a>Удаление файла
Другая распространенная операция с файлами Azure — это удаление файлов. Следующий пример программы удаляет файл с именем SampleFile.txt, хранящийся в каталоге с именем **sampledir**.

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

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите узнать больше о других API-интерфейсах Azure, пожалуйста перейдите по следующим ссылкам.

* [Azure for Java developers](/java/azure) (Azure для разработчиков Java)
* [Пакет SDK для службы хранилища Azure для Java](https://github.com/azure/azure-storage-java)
* [Microsoft Azure Storage SDK for Android](https://github.com/azure/azure-storage-android)
* [справочнике по пакету SDK для клиента службы хранилища Azure](http://dl.windowsazure.com/storage/javadoc/)
* [API-интерфейс REST служб хранилища Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Блог рабочей группы службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* [Приступая к работе со служебной программой командной строки AzCopy](../common/storage-use-azcopy.md)
* [Устранение неполадок с файлами Azure в Windows](storage-troubleshoot-windows-file-connection-problems.md)
