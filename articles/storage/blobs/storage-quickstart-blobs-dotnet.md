---
title: Краткое руководство Azure. Отправка, скачивание и составление списка больших двоичных объектов в службе хранилища Azure с помощью .NET | Документация Майкрософт
description: В рамках этого краткого руководства вы создадите учетную запись хранения и контейнер. Затем используете клиентскую библиотеку службы хранилища для .NET, чтобы отправить большой двоичный объект в службу хранилища Azure, скачать его и составить список больших двоичных объектов в контейнере.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: tamram
ms.openlocfilehash: 716e61840f4bfb5a68a995683e67dae0b43d3854
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью .NET

Из этого руководства вы узнаете, как использовать клиентскую библиотеку .NET для службы хранилища Azure для отправки, загрузки и перечисления блочных BLOB-объектов в контейнере.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством сначала создайте учетную запись хранения Azure на [портале Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Инструкции по созданию учетной записи хранения см. в статье [Создайте учетную запись хранения](../common/storage-quickstart-create-account.md).

Затем загрузите и установите .NET Core 2.0 для своей ОС. Кроме того, вы можете установить редактор для использования с ОС.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- Установите [.NET Core для Windows](https://www.microsoft.com/net/download/windows/build). 
- При необходимости установите [Visual Studio для Windows](https://www.visualstudio.com/). 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Установите [.NET Core для Linux](https://www.microsoft.com/net/download/linux/build).
- При необходимости установите [Visual Studio Code](https://www.visualstudio.com/) и [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068).

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Установите [.NET Core для macOS](https://www.microsoft.com/net/download/macos/build).
- При необходимости установите [Visual Studio для Mac](https://www.visualstudio.com/vs/visual-studio-mac/).

---

## <a name="download-the-sample-application"></a>Загрузка примера приложения

Пример приложения, используемый в этом кратком руководстве, является простым консольным приложением. Пример приложения можно найти на сайте [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

Используйте команду [git](https://git-scm.com/), чтобы скачать копию приложения в среду разработки. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Эта команда клонирует репозиторий в локальную папку git. Чтобы открыть решение Visual Studio, найдите папку storage-blobs-dotnet-quickstart, откройте ее и дважды щелкните файл storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

Чтобы запустить приложение, укажите строку подключения для учетной записи хранения. Вы можете хранить эту строку подключения в переменной среде на локальном компьютере, где выполняется приложение. Создайте переменную среду с помощью одной из следующих команд (в зависимости от операционной системы). Замените `<yourconnectionstring>` фактической строкой подключения.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Измените .bash_profile и добавьте переменную среды.

```
export STORAGE_CONNECTION_STRING=
```

После добавления переменной среды выйдите из системы и войдите повторно, чтобы применить изменения. Кроме того, можно ввести в терминале source .bash_profile.

---

## <a name="run-the-sample"></a>Запуск примера

В этом примере тестовый файл создается в локальной папке **My Documents**, а затем отправляется в хранилище BLOB-объектов. После этого выводится список больших двоичных объектов в контейнере, а затем файл загружается с новым именем, чтобы можно было сравнить старый и новый файлы. 

Перейдите к каталогу приложения и запустите приложение с помощью команды `dotnet run`.

```
dotnet run
```

Результат будет аналогичен приведенному ниже:

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

Если нажать клавишу **ВВОД**, приложение удалит контейнер хранилища и файлы. Перед удалением проверьте наличие двух файлов в папке **MyDocuments**. Вы можете открыть их и убедиться, что они идентичны. Скопируйте URL-адрес большого двоичного объекта из окна консоли и вставьте его в адресную строку браузера, чтобы просмотреть содержимое большого двоичного объекта.

После проверки файлов нажмите любую клавишу для завершения демонстрации и удаления тестовых файлов. Теперь, когда известна функциональность примера, откройте файл Program.cs, чтобы взглянуть на код. 

## <a name="understand-the-sample-code"></a>Разбор примера кода

Изучим пример кода, чтобы понять, как он работает.

### <a name="try-parsing-the-connection-string"></a>Разбор строки подключения

Сначала пример проверяет, содержит ли переменная среды строку подключения, которая может быть проанализирована, для создания объекта [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount), указывающего на учетную запись хранения. Чтобы проверить, допустима ли строка подключения, используйте метод [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Если результаты выполнения метода **TryParse** успешны, он инициализирует переменную *storageAccount* и возвращает значение **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring", EnvironmentVariableTarget.User);

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>Создание контейнера и настройка разрешений

Затем пример создает контейнер и настраивает разрешения, чтобы предоставить доступ ко всем большим двоичным объектам в контейнере. Если большой двоичный объект общедоступен, к нему может получить анонимный доступ любой клиент.

Для создания контейнера необходимо сначала создать экземпляр объекта [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient), указывающий на хранилище BLOB-объектов в учетной записи хранения. Затем создайте экземпляр объекта [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer). А после этого можно создать контейнер. 

В этом случае для создания контейнера пример вызывает метод [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync). Чтобы сделать имя контейнера уникальным, к нему добавляется значение GUID. В рабочей среде для создания контейнера (если он не имеется) и во избежание конфликтов имен предпочтительнее использовать метод [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync).

> [!IMPORTANT]
> Имена контейнеров должны состоять из знаков нижнего регистра. Дополнительные сведения об именовании контейнеров и больших двоичных объектов см. в статье [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Затем пример отправляет локальный файл в блочный BLOB-объект. Пример кода получает ссылку на объект **CloudBlockBlob**, вызвав метод [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) для созданного ранее контейнера. После этого он передает выбранный файл в большой двоичный объект, вызвав метод [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). С помощью этого метода создается большой двоичный объект, если он не был создан ранее, или же, если он имеется, происходит его замещение. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Пример перечисляет большие двоичные объекты в контейнере с помощью метода [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). В нашем примере в контейнер был добавлен лишь один большой двоичный объект, поэтому операция перечисления возвращает только его.

Если больших двоичных объектов для возвращения в одном вызове слишком много (по умолчанию свыше 5000), метод **ListBlobsSegmentedAsync** возвращает сегмент общего результирующего набора и маркер продолжения. Чтобы извлечь следующий сегмент больших двоичных объектов, укажите маркер продолжения, возвращенный предыдущим вызовом. Продолжайте делать это до тех пор, пока значение маркера не будет равно NULL. Маркер продолжения со значением NULL указывает на то, что все большие двоичные объекты были получены. Пример кода показывает, как использовать маркер продолжения.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Скачивание больших двоичных объектов

Кроме того, пример загружает созданный ранее большой двоичный объект в локальную файловую систему с помощью метода [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync). Пример кода добавляет суффикс _DOWNLOADED в имя большого двоичного объекта, чтобы можно было просмотреть оба файла в локальной файловой системе.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Очистка ресурсов

Пример очищает созданные ресурсы, полностью удаляя контейнер с помощью метода [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Кроме того, при необходимости можно удалить локальные файлы.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="resources-for-developing-net-applications-with-blobs"></a>Ресурсы для разработки приложений .NET с большими двоичными объектами

Ознакомьтесь со следующими дополнительными ресурсами для разработки .NET с использованием хранилища BLOB-объектов.

### <a name="binaries-and-source-code"></a>Двоичные файлы и исходный код

- Скачайте пакет NuGet для последней версии [клиентской библиотеки .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) для службы хранилища Azure. 
- См. [исходный код клиентской библиотеки .NET](https://github.com/Azure/azure-storage-net) на сайте GitHub.

### <a name="client-library-reference-and-samples"></a>Справочник по клиентской библиотеке и примеры

- Подробные сведения о клиентской библиотеке .NET см. в [справочнике по API-интерфейсу .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Изучите [примеры для хранилища BLOB-объектов](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob), написанные с использованием клиентской библиотеки .NET.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как передавать, загружать и перечислять файлы в большие двоичные объекты с помощью .NET. 

Сведения о создании веб-приложения, которое передает образ в хранилище BLOB-объектов, см. в статье [Передача данных изображений в облако с помощью службы хранилища Azure](storage-upload-process-images.md).

> [!div class="nextstepaction"]
> [Практическое руководство по операциям в хранилище BLOB-объектов](storage-dotnet-how-to-use-blobs.md)

- Дополнительные сведения о .NET Core см. в статье [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Быстрое начало работы с .NET).
- Пример приложения, которое можно развернуть из Visual Studio для Windows, см. в записи блога [.NET Photo Gallery Web Application Sample with Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/) (Пример веб-приложения .NET "Фотоальбом" с хранилищем BLOB-объектов Azure).
 