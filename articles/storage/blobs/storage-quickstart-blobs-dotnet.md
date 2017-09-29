---
title: "Краткое руководство по Azure. Передача объектов в хранилище BLOB-объектов Azure и обратно с помощью .NET | Документы Майкрософт"
description: "Краткие сведения о передаче объектов в хранилище BLOB-объектов Azure и обратно с помощью .NET"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/01/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 97bacc2c1285fe4a467a54f224bb9fabbd851fee
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Передача объектов в хранилище BLOB-объектов Azure и обратно с помощью .NET

В этом кратком руководстве вы узнаете, как использовать C#.NET для отправки, скачивания и перечисления блочных BLOB-объектов в контейнере в хранилище BLOB-объектов Azure в Windows.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

* Установите [Visual Studio 2017](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) с указанной ниже рабочей нагрузкой:
    - **разработка Azure.**

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Создание учетной записи хранения с помощью портала Azure

Сначала создайте учетную запись хранения общего назначения, которая будет использоваться в этом кратком руководстве. 

1. Войдите на [портал Azure](https://portal.azure.com), используя свою учетную запись Azure. 
2. В главном меню выберите **Создать** > **Хранилище** > **Учетная запись хранения — BLOB-объект, файл, таблица, очередь**. 
3. Выберите имя для своей учетной записи хранения. Имя должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв. Оно также должно быть уникальным.
4. Задайте для параметра `Deployment model` значение **Resource Manager**.
5. Задайте для параметра`Account kind` значение **Универсальные**.
6. Задайте для параметра `Performance` значение **Стандартный**. 
7. Задайте для параметра `Replication` значение **Локально избыточное хранилище (LRS)**.
8. Задайте для параметра `Storage service encryption` значение **Отключено**.
9. Задайте для параметра `Secure transfer required` значение **Отключено**.
10. Выберите свою подписку. 
11. Для параметра `resource group` создайте группу ресурсов и присвойте ей уникальное имя. 
12. Выберите `Location` для использования вашей учетной записи хранения.
13. Щелкните **Закрепить на панели мониторинга** и нажмите кнопку **Создать**, чтобы создать учетную запись хранения. 

Созданная учетная запись хранения будет закреплена на панели мониторинга. Щелкните ее, чтобы открыть. В разделе "Параметры" щелкните **Ключи доступа**. Выберите ключ и скопируйте строку подключения в буфер обмена, а затем вставьте его в Блокнот для последующего использования.

## <a name="download-the-sample-application"></a>Загрузка примера приложения

Пример приложения, используемый в этом кратком руководстве, является простым консольным приложением. 

Используйте команду [git](https://git-scm.com/), чтобы скачать копию приложения в среду разработки. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Эта команда клонирует репозиторий в локальную папку git. Чтобы открыть решение Visual Studio, найдите папку storage-blobs-dotnet-quickstart, откройте ее и дважды щелкните файл storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

В приложении необходимо указать строку подключения для учетной записи хранения. Откройте файл `app.config` в обозревателе решений в Visual Studio. Найдите запись StorageConnectionString. Для параметра **value** замените все значение строки подключения на значение с портала Azure, сохраненное в Блокноте. После этого должен отобразиться результат, аналогичный приведенному ниже.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=youraccountname;
    AccountKey=7NGE5jasdfdRzASDFNOMEx1u20W/thisisjustC/anexampleZK/Rt5pz2xNRrDckyv8EjB9P1WGF==" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>Запуск примера

В этом примере создается тестовый файл в папке "My Documents", затем он отправляется в хранилище BLOB-объектов, после чего выводится список BLOB-объектов в контейнере, а затем файл загружается с новым именем, чтобы можно было сравнить старый и новый файлы. 

Запустите пример, нажав клавишу F5. Выходные данные в окне консоли будут выглядеть следующим образом: 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

Если нажать любую клавишу для продолжения, контейнер хранилища и файлы будут удалены. Прежде чем продолжить, проверьте наличие двух файлов в папке "My Documents". Открыв их, вы увидите, что они идентичны. Скопируйте URL-адрес BLOB-объекта из окна консоли и вставьте его в адресную строку браузера, чтобы просмотреть содержимое файла в хранилище BLOB-объектов.

Для просмотра файлов в хранилище BLOB-объектов можно также воспользоваться таким средством, как [обозреватель службы хранилища Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Обозреватель службы хранилища Azure — это бесплатное кроссплатформенное средство для доступа к данным учетной записи хранения. 

После проверки файлов нажмите любую клавишу для завершения демонстрации и удаления тестовых файлов. Теперь, когда известна функциональность примера, откройте файл Program.cs, чтобы взглянуть на код. 

## <a name="get-references-to-the-storage-objects"></a>Получение ссылок на объекты хранилища

Сначала необходимо создать ссылки на объекты, используемые для доступа к хранилищу BLOB-объектов и управлению им. Эти объекты зависят друг от друга — каждый объект используется следующим в списке объектом.

* Создайте объект **CloudStorageAccount**, указывающий на учетную запись хранения. 

* Создайте объект **CloudBlobClient**, указывающий на службу BLOB-объектов в учетной записи хранения. 

* Создайте объект **CloudBlobContainer**, представляющий контейнер, к которому выполняется доступ. Контейнеры используются для организации BLOB-объектов аналогично папкам для упорядочения файлов на компьютере.

После получения объекта **CloudBlobContainer** можно создать объект **CloudBlockBlob**, который указывает на конкретный нужный вам BLOB-объект, и выполнить операцию отправки, скачивания, копирования и т. д.

В этом разделе описано создание объектов и контейнера и последующее задание разрешений для контейнера, чтобы предоставить общий доступ к BLOB-объектам, осуществляемый по URL-адресу. Контейнер называется **quickstartblobs**. 

В этом примере используется CreateIfNotExists, поскольку требуется создавать контейнер при каждом запуске примера. В рабочей среде, где в рамках приложения используется один и тот же контейнер, рекомендуется вызывать CreateIfNotExists только один раз или же создать контейнер заранее, чтобы не создавать его в коде.

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

## <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. Чаще всего используются блочные BLOB-объекты. Именно этот тип представлен в этом кратком руководстве. 

Чтобы отправить файл в BLOB-объект, получите ссылку на BLOB-объект в целевом контейнере. При наличии ссылки на BLOB-объект вы можете отправить в него данные с помощью командлета [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует.

В примере кода создается локальный файл, который будет использоваться для отправки и скачивания, отправляемый файл сохраняется как **fileAndPath**, и BLOB-объекту задается имя в **localFileName**. В приведенном ниже примере файл отправляется в контейнер с именем **quickstartblobs**.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

Существует несколько методов отправки, которые можно использовать с хранилищем BLOB-объектов. Например, при наличии потока в памяти можно применить метод UploadFromStreamAsync, а не UploadFromFileAsync. 

Блочные BLOB-объекты могут иметь размер 4,7 ТБ и представлять собой любые объекты, начиная от электронных таблиц Excel до больших видеофайлов. Страничные BLOB-объекты в основном используются для файлов виртуального жесткого диска, применяемых для поддержки виртуальных машин IaaS. Добавочные BLOB-объекты используются для ведения журнала, например если требуется выполнить запись в файл и затем добавлять дополнительные сведения. Большинство объектов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты.

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Получите список файлов в контейнере с помощью метода [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). Следующий код извлекает список BLOB-объектов, затем переходит по ним, отображая найденные URI. Можно скопировать URI из окна командной строки и вставить его в адресную строку браузера для просмотра файла.

При наличии в контейнере 5 000 или менее BLOB-объектов все имена BLOB-объектов извлекаются в одном вызове метода ListBlobsSegmentedAsync. При наличии в контейнере более 5 000 BLOB-объектов служба получает список наборами по 5 000 объектов вплоть до извлечения всех имен BLOB-объектов. Таким образом, при первом вызове API возвращаются первые 5 000 имен BLOB-объектов и маркер продолжения. Во второй раз вы указываете маркер, служба получает следующий набор имен BLOB-объектов, и так продолжается до тех пор, пока значение маркера продолжения не будет равно NULL, означающему, что были получены все имена BLOB-объектов. 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

## <a name="download-blobs"></a>Скачивание больших двоичных объектов

Скачайте BLOB-объекты на локальный диск с помощью метода [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

Следующий код скачивает BLOB-объект, отправленный в предыдущем разделе, добавляя к имени BLOB-объекта суффикс "_DOWNLOADED", чтобы вы увидели оба файла на локальном диске. 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны BLOB-объекты, отправленные в этом кратком руководстве, удалите весь контейнер с помощью метода [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Кроме того, удалите созданные файлы, если они больше не требуются.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы узнали, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure с помощью .NET. Дополнительные сведения о работе с хранилищем BLOB-объектов см. в соответствующем практическом руководстве.

> [!div class="nextstepaction"]
> [Практическое руководство по операциям в хранилище BLOB-объектов](storage-dotnet-how-to-use-blobs.md)

Дополнительные сведения об обозревателе объектов и BLOB-объектах см. в статье [Управление ресурсами хранилища BLOB-объектов Azure с помощью обозревателя хранилищ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

