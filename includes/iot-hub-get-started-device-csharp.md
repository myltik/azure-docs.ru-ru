## Создание приложения виртуального устройства

В этом разделе вам предстоит создать консольное приложение Windows, которое имитирует устройство, отправляющее сообщения с устройства в облако в центр IoT.

1. В Visual Studio добавьте классический настольный проект Windows на языке Visual C# с помощью шаблона проекта **Консольное приложение**. Убедитесь, что указана версия платформы .NET 4.5.1 или выше. Назовите проект **SimulatedDevice**.

   	![][30]

2. В обозревателе решений щелкните правой кнопкой мыши проект **SimulatedDevice** и выберите **Управление пакетами NuGet**.

3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, выполните поиск по запросу **Microsoft.Azure.Devices.Client**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Client**, и примите условия использования.

	После этого будут выполнены скачивание и установка [пакета SDK NuGet для устройств Azure IoT][lnk-device-nuget], включая зависимости, а также добавление ссылки на него.

4. Добавьте следующий оператор `using` в начало файла **Program.cs**.

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;


5. Добавьте в класс **Program** следующие поля, заменив значения заполнителей на имя узла центра IoT, полученное в разделе *Создание центра IoT*, и ключ устройства, полученный в разделе *Создание удостоверения устройства*:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Добавьте следующий метод в класс **Program**.

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

	Этот метод отправляет новое сообщение с устройства в облако каждую секунду. Сообщение содержит объект сериализации JSON с deviceId и случайное число, что позволяет имитировать датчик скорости ветра.

7. Наконец, добавьте следующие строки в метод **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  По умолчанию метод **Create** создает экземпляр **DeviceClient**, который использует протокол AMQP для связи с центром IoT. Для использования протокола HTTPS используйте переопределение метода **Create**, чтобы указать протокол. Если вы решили использовать протокол HTTPS, добавьте в свой проект также пакет NuGet **Microsoft.AspNet.WebApi.Client**, чтобы включить пространство имен **System.Net.Http.Formatting**.

В этом руководстве описываются шаги по созданию клиентского устройства центра IoT. В качестве альтернативы можно использовать расширение [подключенной службы для центра Azure IoT][lnk-connected-service] \(Visual Studio), чтобы добавить необходимый код в приложение клиентского устройства.


> [AZURE.NOTE] Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя][lnk-transient-faults].

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0413_2016-->