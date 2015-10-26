## Подготовка учетной записи хранения Azure
Поскольку имитация устройства отправляет файл в большой двоичный объект в службе хранилища Azure, необходимо иметь учетную запись хранения Azure. Можно использовать существующую учетную запись или создать новую, следуя инструкциям из раздела [Сведения о службе хранилища Azure]. Запишите строку подключения учетной записи хранения.

## Отправка универсального кода ресурса (URI) большого двоичного объекта Azure в имитацию устройства

В этом разделе вы измените консольное приложение **SendCloudtoDevice**, созданное в разделе [Отправка сообщений с облака на устройства в центре IoT], чтобы включить универсальный код ресурса (URI) большого двоичного объекта Azure с подписанным URL-адресом с общим доступом. Это позволяет серверной части облака предоставлять доступ на запись в большой двоичный объект только получателю сообщения, передаваемого из облака на устройство.

1. В Visual Studio щелкните правой кнопкой мыши проект **SendCloudtoDevice** и выберите команду **Управление пакетами NuGet...**. 

    Откроется окно «Управление пакетами NuGet».

2. Выполните поиск `WindowsAzure.Storage` и щелкните **Установить**, после чего примите условия использования.

    После этого будут выполнены скачивание, установка и добавление ссылки на [пакет SDK для службы хранилища Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. Добавьте в начало файла **Program.cs** следующие операторы:

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. В классе **Программа** добавьте следующие поля класса, заменив строку подключения для своей учетной записи хранения.

        static string storageConnectionString = "{storage connection string}";

    Затем добавьте следующий метод (можно использовать любое имя контейнера больших двоичных объектов, в этом руководстве используется **iothubfileuploadtutorial**):
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    Этот метод создает новую ссылку на большой двоичный объект и создает универсальный код ресурса (URI) подписанного URL-адреса с общим доступом, как описано в разделе [Создание и использование SAS с помощью службы BLOB-объектов](https://azure.microsoft.com/ru-RU/documentation/articles/storage-dotnet-shared-access-signature-part-2/). Обратите внимание, что описанный выше метод создает универсальный код ресурса (URI) подписи, который действителен в течение 24 часов. Если целевому устройству требуется больше времени для передачи файла (например, он подключается к сети редко и имеет недостаточно надежное подключение для передачи большого файла), может потребоваться увеличение срока действия подписей.

5. Измените **SendCloudToDeviceMessageAsync** следующим образом:

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Этот метод отправляет сообщение, передаваемое из облака на устройство, которое содержит два свойства приложения: одно идентифицирует это сообщение в качестве команды на отправку файла, другое содержит универсальный код ресурса (URI) большого двоичного объекта. Он также запрашивает все подтверждения доставки. Обратите внимание, что сведения в этих двух свойствах приложения могут быть сериализованы в текст сообщения, однако это потребует выполнения дополнительных операций по сериализации и десериализации данных.

<!-- Links -->

[Сведения о службе хранилища Azure]: https://azure.microsoft.com/ru-RU/documentation/articles/storage-create-storage-account/#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/ru-RU/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->

<!---HONumber=Oct15_HO3-->