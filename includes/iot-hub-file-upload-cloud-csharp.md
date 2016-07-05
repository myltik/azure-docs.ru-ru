## Получение уведомления о передачи файла

В этом разделе вам предстоит создать консольное приложение для Windows, которое принимает уведомления о передаче файлов из центра IoT.

1. В текущем решении Visual Studio создайте проект Windows на языке Visual C#, используя шаблон проекта **Консольное приложение**. Назовите проект **ReadFileUploadNotification**.

    ![Новый проект в Visual Studio][2]

2. В обозревателе решений щелкните правой кнопкой мыши проект **ReadFileUploadNotification** и выберите пункт **Управление пакетами NuGet**.

    Откроется окно "Управление пакетами NuGet".

2. Выполните поиск `Microsoft.Azure.Devices` и щелкните **Установить**, после чего примите условия использования.

	После этого будут выполнены скачивание, установка [пакета NuGet пакета SDK служб в Azure IoT] и добавление ссылки на него в проект **ReadFileUploadNotification**.

3. Добавьте в начало файла **Program.cs** следующие инструкции:

        using Microsoft.Azure.Devices;

4. Добавьте следующие поля в класс **Program**. Замените значение заполнителя строкой подключения центра IoT из раздела [Приступая к работе с центром IoT].

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Добавьте следующий метод в класс **Program**.
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Обратите внимание, что шаблон получения здесь аналогичен шаблону, использовавшемуся для получения сообщений из облака на устройство из приложения устройства.

6. Наконец, добавьте следующие строки в метод **Main**:

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[пакета NuGet пакета SDK служб в Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Приступая к работе с центром IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[2]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0622_2016-->