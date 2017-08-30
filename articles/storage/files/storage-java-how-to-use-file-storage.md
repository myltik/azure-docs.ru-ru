---
title: "Разработка для хранилища файлов Azure на языке Java | Документация Майкрософт"
description: "Узнайте, как разрабатывать приложения и службы Java, использующие хранилище файлов Azure для хранения файлов данных."
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
ms.date: 05/27/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ce38944b9d5e663505c5808864ba61a5e2284f3b
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---

# <a name="develop-for-azure-file-storage-with-java"></a>Разработка для хранилища файлов Azure на языке Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>О данном учебнике
В этом руководстве мы рассмотрим основы использования Java для разработки приложений и служб, использующих хранилище файлов Azure для хранения данных файлов. В рамках этого руководства мы создадим простое консольное приложение, а также покажем, как выполнять базовые действия с Java и хранилищем файлов Azure.

* Создание и удаление общих папок Azure.
* Создание и удаление каталогов.
* Перечисление файлов и каталогов в файловом ресурсе Azure.
* Передача, загрузка и удаление файлов.

> [!Note]  
> Так как к хранилищу файлов Azure можно обращаться через SMB, вы можете создавать простые приложения, которые получают доступ к общей папке Azure с использованием стандартных классов ввода-вывода в Java. Из этой статьи вы узнаете, как применять в приложениях пакет SDK Java для службы хранилища Azure, который использует [REST API хранилища файлов Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) для взаимодействия с хранилищем файлов Azure.

## <a name="create-a-java-application"></a>Создание приложения Java
Для создания примеров вам потребуется комплект разработчика Java (JDK) и [Пакет SDK для службы хранилища Azure для Java][]. Вам также необходимо создать учетную запись хранилища Azure.

## <a name="setup-your-application-to-use-azure-file-storage"></a>Настройка приложения для работы с хранилищем файлов Azure
Чтобы использовать API-интерфейсы хранилища Azure, добавьте следующую инструкцию в верхнюю часть файла Java, откуда планируется осуществлять доступ к службе хранилища.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Настройка строки подключения к службе хранилища Azure
Чтобы начать работу с хранилищем файлов Azure, необходимо подключиться к учетной записи хранения Azure. Для начала потребуется настроить строку подключения, которая будет использоваться для подключения к учетной записи хранилища. Для этого определим статическую переменную.

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
Все файлы и каталоги в хранилище файлов Azure размещаются в контейнере, который называется общей папкой (**Share**). Учетная запись хранения может иметь столько общих папок, насколько позволяет емкость вашей учетной записи. Чтобы получить доступ к общей папке и ее содержимому, необходимо использовать клиент хранилища файлов Azure.

```java
// Create the Azure File storage client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

С помощью клиента хранилища файлов Azure вы получите ссылку на общую папку.

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
Вы также можете организовать хранилище, помещая файлы в подкаталоги вместо их размещения в корневом каталоге. Хранилище файлов Azure позволяет создать столько каталогов, сколько допускает учетная запись. В следующем примере кода в корневом каталоге создается вложенный каталог с именем **sampledir** .

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
Одной из наиболее частых операций, которые выполняются с хранилищем файлов Azure, является операция скачивания файлов. В следующем примере происходит скачивание файла SampleFile.txt с последующим отображением его содержимого.

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
Следующая распространенная операция в хранилище файлов Azure — это удаление файлов. Следующий пример программы удаляет файл с именем SampleFile.txt, хранящийся в каталоге с именем **sampledir**.

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
* [Приступая к работе со служебной программой командной строки AzCopy](../common/storage-use-azcopy.md* [Troubleshooting Azure File storage problems - Windows](storage-troubleshoot-windows-file-connection-problems.md)
)
