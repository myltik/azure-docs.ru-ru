###### Создание контейнера

Так же как файлы располагаются в папках, большие двоичные объекты хранилища располагаются в контейнерах.

-   С помощью объекта **CloudBlobClient** можно получить ссылку на контейнер, который необходимо использовать.

-   Можно вызвать метод CreateCloudBlobClient(), чтобы при необходимости создать контейнер.

В коде следующего примера показано, как создать контейнер хранилища большого двоичного объекта. В результате выполнения кода создается объект **BlobClient**, и можно получать доступ к функциям этого объекта, например к созданию контейнера хранилища. Затем код попытается создать ссылку на контейнер хранилища с именем "mycontainer". Если контейнер с таким именем отсутствует, он будет создан.

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “mycontainer.”
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If “mycontainer” doesn’t exist, create it.
    container.CreateIfNotExists();

**Примечание.** Используйте этот блок кода перед кодом из следующих разделов.

###### Отправка BLOB-объекта в контейнер

Для отправки файла большого двоичного объекта в контейнер получите ссылку на контейнер и используйте ее для получения ссылки на большой двоичный объект. Получив ссылку, вы можете отправить в него любой поток данных с помощью метода UploadFromStream(). Эта операция создает большой двоичный объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

###### Перечисление BLOB-объектов в контейнере

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. Затем можно использовать метод ListBlobs() контейнера, чтобы извлечь большие двоичные объекты и (или) каталоги в нем. Для доступа к широкому набору свойств и методов возвращаемого объекта **IListBlobItem** необходимо преобразовать его в объект**CloudBlockBlob**, **CloudPageBlob** или **CloudBlobDirectory**. Если тип большого двоичного объекта неизвестен, можно использовать проверку типов, чтобы определить нужный. Следующий код демонстрирует, как получить и вывести URI каждого элемента в контейнере с именем "photos".

    // Get a reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop through items in the container and output the length and URI for each 
    // item.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Есть другие способы вывести список содержимого контейнера больших двоичных объектов. Дополнительные сведения см. в статье [Как использовать хранилище больших двоичных объектов из .NET][Как использовать хранилище больших двоичных объектов из .NET].

###### Загрузка blob-объектов

Чтобы скачать большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод DownloadToStream(). В следующем примере метод DownloadToStream используется для переноса содержимого большого двоичного объекта в объект stream, который затем можно сохранить в локальном файле.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Есть другие способы сохранения больших двоичных объектов в виде файлов. Дополнительные сведения см. в статье [Как использовать хранилище больших двоичных объектов из .NET][1].

###### Удаление BLOB-объекта

Чтобы удалить большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод Delete().

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();

  [Как использовать хранилище больших двоичных объектов из .NET]: http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob
  [1]: http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs
