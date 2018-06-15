---
title: Краткое руководство Azure. Создание большого двоичного объекта в хранилище объектов с помощью Java | Документация Майкрософт
description: В рамках этого краткого руководства вы создадите учетную запись хранения и контейнер в хранилище объектов (больших двоичных объектов). Затем используйте клиентскую библиотеку службы хранилища для языка Java, чтобы отправить большой двоичный объект в службу хранилища Azure, скачать его и составить список больших двоичных объектов в контейнере.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: 197777971b92ad9cd53e91602b88858a371ce1d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192015"
---
# <a name="quickstart-upload-download-and-list-blobs-using-java"></a>Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью языка Java

Из этого краткого руководства вы узнаете, как использовать Java для передачи, скачивания и перечисления блочных BLOB-объектов в контейнере в хранилище BLOB-объектов Azure.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством сделайте следующее:

* Установите интегрированную среду разработки с интегрированным компонентом Maven.

* Либо установите и настройте Maven для работы из командной строки.

В этом руководстве используется среда разработки [Eclipse](http://www.eclipse.org/downloads/) с конфигурацией для разработчиков Java.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Загрузка примера приложения

[Пример приложения](https://github.com/Azure-Samples/storage-blobs-java-quickstart), используемый в этом кратком руководстве, является простым консольным приложением. 

Используйте команду [git](https://git-scm.com/), чтобы скачать копию приложения в среду разработки. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Эта команда клонирует репозиторий в локальную папку git. Чтобы открыть проект, запустите Eclipse и закройте экран приветствия. Выберите **File** (Файл), затем щелкните **Open Projects from File System...** (Открыть проекты из файловой системы). Убедитесь, что флажок **Detect and configure project natures** (Обнаружить и настроить свойства проекта) установлен. Выберите **Directory** (Каталог) и перейдите к папке хранения клонированного репозитория. В ней выберите папку **javaBlobsQuickstart**. Убедитесь, что проект **javaBlobsQuickstarts** отображается как проект Eclipse, затем выберите **Finish** (Готово).

После завершения импорта проекта откройте файл **AzureApp.java** (расположенный в папке **blobQuickstart.blobAzureApp** по пути **main/src/java**) и замените `accountname` и `accountkey` в строке `storageConnectionString`. Затем запустите приложение.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]   

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища
    
В приложении необходимо указать строку подключения для учетной записи хранения. Откройте файл **AzureApp.Java**. Найдите переменную `storageConnectionString` и вставьте значение строки подключения, скопированное в предыдущем разделе. Переменная `storageConnectionString` должна выглядеть следующим образом:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Запуск примера

В этом примере создается тестовый файл в каталоге по умолчанию ("Мои документы" для пользователей Windows), затем он передается в хранилище BLOB-объектов, после чего выводится список BLOB-объектов в контейнере, а затем файл скачивается с новым именем, чтобы можно было сравнить старый и новый файлы. 

Запустите пример, нажав клавиши **CTRL+F11** в Eclipse.

Если вы хотите запустить пример, используя Maven в командной строке, откройте оболочку и перейдите в папку **blobAzureApp** в клонированном каталоге. Затем введите `mvn compile exec:java`.

Ниже приведен пример выходных данных для запуска приложения в Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

 Прежде чем продолжить, проверьте свой каталог по умолчанию ("Мои документы" для пользователей windows) на наличие двух файлов. Вы можете открыть их и убедиться, что они идентичны. Скопируйте URL-адрес BLOB-объекта из окна консоли и вставьте его в адресную строку браузера, чтобы просмотреть содержимое файла в хранилище BLOB-объектов. Если нажать клавишу ВВОД, контейнер хранилища и файлы будут удалены.

Для просмотра файлов в хранилище BLOB-объектов можно также воспользоваться таким средством, как [обозреватель службы хранилища Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Обозреватель службы хранилища Azure — это бесплатное кроссплатформенное средство для доступа к данным учетной записи хранения. 

После проверки файлов нажмите клавишу ВВОД, чтобы завершить работу демонстрационной версии и удалить тестовые файлы. Теперь вы знаете, что делает этот пример. Давайте откроем файл **AzureApp.java** и изучим его код. 

## <a name="understand-the-sample-code"></a>Разбор примера кода

Разберем пример кода, чтобы понять, как он работает.

### <a name="get-references-to-the-storage-objects"></a>Получение ссылок на объекты хранилища

Сначала необходимо создать ссылки на объекты, используемые для доступа к хранилищу BLOB-объектов и управлению им. Эти объекты зависят друг от друга — каждый объект используется следующим в списке объектом.

* Создайте экземпляр объекта [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage._storage_account), указывающий на учетную запись хранения.

    Объект **CloudStorageAccount** представляет вашу учетную запись хранения и позволяет настраивать и использовать ее свойства программным способом. С помощью **CloudStorageAccount** можно создать экземпляр объекта **CloudBlobClient**, который необходим для доступа к службе BLOB-объектов.

* Создайте экземпляр объекта **CloudBlobClient**, указывающий на [службу BLOB-объектов](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) в учетной записи хранения.

    **CloudBlobClient** предоставляет точку доступа к службе BLOB-объектов и позволяет настраивать и использовать свойства хранилища BLOB-объектов программным способом. С помощью **CloudBlobClient** можно создать экземпляр объекта **CloudBlobContainer**, который необходим для создания контейнеров.

* Создайте экземпляр объекта [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container), представляющий контейнер, к которому выполняется доступ. Контейнеры используются для организации BLOB-объектов аналогично папкам для упорядочения файлов на компьютере.    

    После получения объекта **CloudBlobContainer** можно создать экземпляр объекта [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob), который указывает на конкретный нужный вам большой двоичный объект, и выполнить операцию передачи, скачивания, копирования и т. д.

> [!IMPORTANT]
> Имена контейнеров должны состоять из знаков нижнего регистра. Дополнительные сведения об именовании контейнеров и больших двоичных объектов см. в статье [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

### <a name="create-a-container"></a>Создание контейнера 

В этом разделе описано создание экземпляров объектов, создание контейнера и последующее задание разрешений для контейнера, что позволяет предоставить общий доступ к большим двоичным объектам по URL-адресу. Контейнер называется **quickstartblobs**. 

В этом примере используется [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists), так как при каждом запуске примера требуется создавать новый контейнер. В рабочей среде, где для приложения используется один и тот же контейнер, рекомендуется вызывать **CreateIfNotExists** только один раз. Вы можете также создать контейнер заранее, чтобы не создавать его в коде.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. Чаще всего используются блочные BLOB-объекты. Именно этот тип представлен в этом кратком руководстве. 

Чтобы отправить файл в BLOB-объект, получите ссылку на BLOB-объект в целевом контейнере. При наличии ссылки на BLOB-объект вы можете передать в него данные с помощью [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует.

Пример кода создает локальный файл для отправки и скачивания. Файл для отправки хранится как **source**, а имя BLOB-объекта — как **blob**. В приведенном ниже примере файл отправляется в контейнер с именем **quickstartblobs**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Есть несколько вариантов для передач, включая [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) и [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext), которые можно использовать с хранилищем BLOB-объектов. Для строки, например, лучше использовать метод UploadText, а не Upload. 

Блочный BLOB-объект может представлять собой текстовый или двоичный файл любого типа. Страничные BLOB-объекты в основном используются для файлов виртуального жесткого диска, применяемых для поддержки виртуальных машин IaaS. Добавочные BLOB-объекты используются для ведения журнала, например если требуется выполнить запись в файл и затем добавлять дополнительные сведения. Большинство объектов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты.

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Получить список файлов в контейнере можно с помощью метода [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). Следующий код извлекает список BLOB-объектов, затем переходит по ним, отображая найденные URI. Можно скопировать URI из окна командной строки и вставить его в адресную строку браузера для просмотра файла.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Скачивание больших двоичных объектов

Скачайте большие двоичные объекты на локальный диск с помощью метода [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

Следующий код скачивает BLOB-объект, отправленный в предыдущем разделе, добавляя к имени BLOB-объекта суффикс "_DOWNLOADED", чтобы вы увидели оба файла на локальном диске. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны большие двоичные объекты, отправленные в этом кратком руководстве, удалите весь контейнер с помощью метода [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Он также удаляет файлы в контейнере.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="resources-for-developing-java-applications-with-blobs"></a>Ресурсы для разработки приложений Java с большими двоичными объектами

Ознакомьтесь со следующими дополнительными ресурсами для разработки Java с использованием хранилища BLOB-объектов:

### <a name="binaries-and-source-code"></a>Двоичные файлы и исходный код

- Просматривайте и скачивайте [исходный код клиентской библиотеки Java](https://github.com/Azure/azure-storage-java) для службы хранилища Azure в GitHub.

### <a name="client-library-reference-and-samples"></a>Справочник по клиентской библиотеке и примеры

- Подробные сведения о клиентской библиотеке Java см. в [справочнике по API-интерфейсу Java](https://docs.microsoft.com/java/api/overview/azure/storage).
- Изучите [примеры для хранилища BLOB-объектов](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=java&term=blob), написанные с использованием клиентской библиотеки Java.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure с помощью Java. Дополнительные сведения о работе с хранилищем BLOB-объектов см. в соответствующем практическом руководстве.

> [!div class="nextstepaction"]
> [Практическое руководство по операциям в хранилище BLOB-объектов](storage-java-how-to-use-blob-storage.md)

Дополнительные сведения об обозревателе объектов и BLOB-объектах см. в статье [Управление ресурсами хранилища BLOB-объектов Azure с помощью обозревателя хранилищ](../../vs-azure-tools-storage-explorer-blobs.md).

Примеры Java см. в описании [примеров службы хранилища Azure с использованием Java](../common/storage-samples-java.md).
