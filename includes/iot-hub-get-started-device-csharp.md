## Создание приложения виртуального устройства

В этом разделе вам предстоит написать консольное приложение для Windows, которое имитирует устройство, отправляющее сообщение "из устройства в облако" в центр IoT.

1. В текущем решении Visual Studio щелкните **Файл -> Новый -> Проект**, чтобы создать новый проект Visual C# для классических приложений с помощью шаблона проекта **Консольное приложение**. Назовите проект **SimulatedDevice**.

   	![][30]

2. В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения...**.

	Откроется окно «Управление пакетами NuGet».

3. Выполните поиск `Microsoft Azure Devices Client` и щелкните **Установить**, после чего примите условия использования.

	После этого будут выполнены скачивание, установка и добавление ссылки в [пакет Azure IoT — Device SDK NuGet].

4. Добавьте следующий оператор `using` в начало файла **Program.cs**.

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Добавьте в класс **Program** следующие поля, заменив значения заполнителей на универсальный код ресурса (URI) центра IoT и ключ устройства, полученные в разделах **Создание центра IoT** и **Создание удостоверения устройства** соответственно:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub URI}";
        static string deviceKey = "{deviceKey}";

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

                Thread.Sleep(1000);
            }
        }

	Этот метод каждую секунду будет отправлять новое сообщение "из устройства в облако", содержащее объект сериализации JSON с идентификатором устройства и случайным числом, представляя имитацию датчика скорости воздуха.

7. Наконец, добавьте следующие строки в метод **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

> [AZURE.NOTE]Для упрощения в этом учебнике не реализуются какие-либо политики повтора. В рабочем коде рекомендуется реализовать политики повтора (например, экспоненциальную задержку), как это указано в статье MSDN [Обработка временного сбоя].

<!-- Links -->

[пакет Azure IoT — Device SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[Обработка временного сбоя]: https://msdn.microsoft.com/ru-RU/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=Oct15_HO3-->