## Связывание учетной записи хранения Azure с центром IoT
Так как имитация устройства передает файл в большой двоичный объект в службе хранилища Azure, необходимо связать учетную запись хранения Azure с центром IoT. Можно использовать существующую учетную запись хранения или создать новую, следуя инструкциям в статье [Об учетных записях хранения Azure]. Можно связать учетную запись хранения Azure с центром IoT, следуя инструкциям в разделе [Управление центрами IoT через портал Azure].

## Передача файла из виртуального устройства
В этом разделе вы измените приложение имитации устройства, созданное в разделе [Отправка сообщений из облака на устройства с помощью центра IoT], для получения сообщений из облака на устройство от центра IoT.

1. В Visual Studio щелкните правой кнопкой мыши проект **SimulatedDevice**, а затем щелкните **Добавить** > **Существующий элемент**. Перейдите к файлу образа и добавьте его в проект. В этом учебнике используется образ `image.jpg`.
2. Щелкните его правой кнопкой мыши и выберите пункт **Свойства**. Убедитесь, что параметр **Копировать в выходной каталог** имеет значение **Всегда копировать**.
   
    ![][1]
3. Добавьте в начало файла **Program.cs** следующие инструкции:
   
        using System.IO;
4. Добавьте следующий метод в класс **Program**.
   
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();
   
            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }
   
            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }
   
    Метод `UploadToBlobAsync` принимает имя передаваемого файла и источник его передачи и обрабатывает передачу в хранилище. Консольное приложение отображает время, необходимое для передачи файла.
5. Добавьте в метод **Main** непосредственно перед строкой `Console.ReadLine()` следующий метод.
   
        SendToBlobAsync();

> [!NOTE]
> Для упрощения в этом учебнике не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя].
> 
> 

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Обработка временного сбоя]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Об учетных записях хранения Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Управление центрами IoT через портал Azure]: ../articles/iot-hub-manage-through-portal/#file-upload

<!-- Images -->
[1]: ../articles/iot-hub/media/iot-hub-csharp-csharp-file-upload/image-properties.png

<!---HONumber=AcomDC_0622_2016-->