## Передача файла из виртуального устройства

В этом разделе вы измените приложение виртуального устройства, созданное в разделе [Отправка сообщений "из облака в устройство" с помощью центра IoT], для получения сообщений "из облака в устройство" от центра IoT.

1. В Visual Studio щелкните правой кнопкой мыши по решению **SimulatedDevice**, а затем — **Управление пакетами NuGet...**. 

    Откроется окно "Управление пакетами NuGet".

2. Выполните поиск `WindowsAzure.Storage` и щелкните **Установить**, после чего примите условия использования.

    После этого будут выполнены скачивание, установка и добавление ссылки в [пакет SDK для службы хранилища Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. Добавьте в начало файла **Program.cs** следующие инструкции:

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. В классе **Program** измените метод **ReceiveC2dAsync** следующим образом:
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    Это позволит **ReceiveC2dAsync** различать сообщения со свойством `command`, значение которого равно `FileUpload`. Далее это сообщение будет обрабатываться методом **UploadFileToBlobAsync**.

    Добавьте представленный ниже метод, чтобы обрабатывать команды передачи файла.
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    Этот метод использует пакет SDK для службы хранилища Azure для передачи случайно сгенерированного большого двоичного объекта размером 10 МБ по указанному универсальному коду ресурса (URI). Более подробные сведения об отправке больших двоичных объектов можно найти в разделе [Служба хранилища Azure. Использование больших двоичных объектов].

> [AZURE.NOTE]Обратите внимание, как данная реализация виртуального устройства завершает только данное сообщение "из облака в устройство" после передачи большого двоичного объекта. Такой подход упрощает обработку передаваемых файлов на стороне сервера, так как подтверждение доставки означает, что отправленный файл доступен для обработки. Однако согласно [Руководству разработчика по центру IoT][IoT Hub Developer Guide - C2D], сообщение, которое не было завершено до истечения *времени ожидания видимости* (обычно 1 минута), помещается обратно в очередь, и метод **ReceiveAsync()** получает его снова. В сценариях, где передача файла может занять больше времени, для виртуального устройства рекомендуется использовать постоянное хранилище текущих заданий отправки. Это позволяет виртуальному устройству завершить сообщение "из облака в устройство" до завершения передачи файла, а затем отправить сообщение "из устройства в облако", чтобы уведомить о завершении работы на стороне сервера.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Служба хранилища Azure. Использование больших двоичных объектов]: https://azure.microsoft.com/ru-RU/documentation/articles/storage-dotnet-how-to-use-blobs/#upload-a-blob-into-a-container

<!-- Images -->

<!---HONumber=Oct15_HO3-->