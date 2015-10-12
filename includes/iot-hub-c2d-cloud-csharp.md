## Отправка сообщения, передаваемого из облака на устройство, из серверной части приложений

В этом разделе вам предстоит написать консольное приложение для Windows, которое отправляет сообщения, передаваемые из облака на устройство, в имитацию приложения устройства.

1. В текущем решении Visual Studio создайте новый проект классического приложения Visual C# с помощью шаблона проекта **Консольное приложение**. Присвойте проекту имя **SendCloudToDevice**.

   	![][20]

2. В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения...**.

	Откроется окно «Управление пакетами NuGet».

3. Выполните поиск `Microsoft Azure Devices` и щелкните **Установить**, после чего примите условия использования.

	После этого будут выполнены скачивание, установка и добавление ссылки на [пакет NuGet пакета SDK служб в Azure IoT].

4. Добавьте следующий оператор `using` в начало файла **Program.cs**.

		using Microsoft.Azure.Devices;

5. Добавьте следующие поля в класс **Program**, заменяя значения заполнителей именем центра IoT и строкой подключения центра IoT из раздела [Приступая к работе с центром IoT].

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Добавьте следующий метод в класс **Program**.

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	Этот метод будет отправлять новое сообщение, передаваемое из облака на устройство, на устройстве с идентификатором `myFirstDevice`. Измените этот параметр соответствующим образом, если вы использовали значение, отличное от указанного в разделе [Приступая к работе с центром IoT].

7. Наконец, добавьте следующие строки в метод **Main**:

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. В Visual Studio щелкните правой кнопкой мыши на вашем решении и выберите **Настроить запускаемые при включении проекты**. Выберите **Несколько запускаемых проектов**, а затем выберите действие **Запуск** для **ProcessDeviceToCloudMessages**, **SimulatedDevice** и **SendCloudToDevice**.

9.  Нажмите клавишу **F5**, после чего должны запуститься все три приложения. Выберите окна **SendCloudToDevice** и нажмите клавишу **ВВОД**, после чего сообщение должно быть принято имитацией приложения.

    ![][21]

## Получение подтверждений доставки
Для каждого сообщения, передаваемого из облака на устройство, в центре IoT можно запросить подтверждения доставки (или истечения срока действия). Это позволяет серверной части облака легко передавать данные в логику повторных попыток или компенсаций. Более подробную информацию о подтверждениях при передаче из облака на устройство можно найти в [руководстве разработчика по центру IoT][IoT Hub Developer Guide - C2D].

В этом разделе вы измените приложение **SendCloudToDevice**, чтобы оно выводило запрос на подтверждение и получало соответствующие данные из центра IoT.

1. В Visual Studio в проекте **SendCloudToDevice** добавьте в класс **Program** следующий метод:
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Обратите внимание, что шаблон получения здесь аналогичен шаблону, использовавшемуся для получения сообщений из облака на устройство из приложения устройства.

2. Добавьте следующий метод в метод **Main** прямо после строки `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`:

        ReceiveFeedbackAsync();

3. Чтобы подтвердить запрос на подтверждение доставки сообщения облака для устройства, необходимо указать свойство в методе **SendCloudToDeviceMessageAsync**. Добавьте следующую строку сразу после строки `var commandMessage = new Message(...);`:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Запустите приложения, нажав клавишу **F5**, после чего должны запуститься все три приложения. Выберите окна **SendCloudToDevice** и нажмите клавишу **ВВОД**, после чего имитация приложения должна получить сообщение, а через несколько секунд ваше приложение **SendCloudToDevice** должно получить сообщение о подтверждении.

    ![][22]

> [AZURE.NOTE]Для упрощения в этом руководстве не реализуются какие-либо политики повтора. В рабочем коде рекомендуется реализовать политики повтора (например, экспоненциальную задержку), как это указано в статье MSDN [Обработка временного сбоя].

<!-- Links -->

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[пакет NuGet пакета SDK служб в Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Обработка временного сбоя]: https://msdn.microsoft.com/ru-RU/library/hh680901(v=pandp.50).aspx
[Приступая к работе с центром IoT]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png

<!---HONumber=Oct15_HO1-->