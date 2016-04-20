## Создание удостоверения устройства

В этом разделе вам предстоит написать консольное приложение Windows, которое создает новое удостоверение устройства в реестре удостоверений в центре IoT. Устройство может подключиться к центру IoT, только если в реестре удостоверений устройств есть соответствующая запись. Более подробную информацию см. в разделе **Реестр удостоверений устройств** [руководства разработчика по центру IoT][lnk-devguide-identity]. При запуске этого консольного приложения создается уникальный идентификатор устройства и ключ, с помощью которого выполняется идентификация устройства во время отправки сообщений с устройства в облако для центра IoT.

1. В Visual Studio добавьте классический настольный проект Windows на языке Visual C# с помощью шаблона проекта **Консольное приложение**. Убедитесь, что указана версия платформы .NET 4.5.1 или выше. Присвойте проекту имя **CreateDeviceIdentity**.

	![][10]

2. В обозревателе решений щелкните правой кнопкой мыши проект **CreateDeviceIdentity** и выберите пункт **Управление пакетами NuGet**.

3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования.

	![][11]

4. После этого пакет NuGet [пакета SDK для службы IoT Microsoft Azure][lnk-nuget-service-sdk] будет загружен и установлен, а затем будет добавлена ссылка на него и его зависимости.

4. Добавьте следующие инструкции `using` в начало файла **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Добавьте следующие поля в класс **Program**, заменяя значения заполнителей строкой подключения для центра IoT, созданного в предыдущем разделе.

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Добавьте следующий метод в класс **Program**.

		private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

	Этот метод создает новое удостоверение устройства с идентификатором **myFirstDevice** (если этот идентификатор устройства уже существует в реестре, код просто извлекает сведения о существующем устройстве). Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ будет использоваться в виртуальном устройстве для подключения к центру IoT.

7. Наконец, добавьте следующие строки в метод **Main**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Запустите это приложение и запишите ключ устройства.

    ![][12]

> [AZURE.NOTE] В реестре удостоверений центра IoT хранятся только идентификаторы устройств для предоставления безопасного доступа к концентратору. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок "Вкл./Выкл.", который позволяет отключить доступ для отдельного устройства. Если в вашем приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков центра IoT][lnk-devguide-identity].

## Получение сообщений с устройства в облако

В этом разделе вы создадите консольное приложение Windows, которое считывает сообщения, передаваемые с устройства в облако из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве [Как обрабатывать сообщения, отправляемые с устройства в облако][lnk-processd2c-tutorial] показано, как обрабатывать такие сообщения с применением масштабирования. В руководстве [Приступая к работе с концентраторами событий][lnk-eventhubs-tutorial] приводятся дополнительные сведения о том, как обрабатываются сообщения из концентраторов событий. Это руководство применяется к конечным точкам центра IoT, совместимым с концентраторами событий.

> [AZURE.NOTE] Совместимая с концентраторами событий конечная точка для чтения сообщений, отправляемых с устройства в облако, всегда использует протокол AMQPS.

1. В Visual Studio добавьте классический настольный проект Windows на языке Visual C# с помощью шаблона проекта **Консольное приложение**. Убедитесь, что указана версия платформы .NET 4.5.1 или выше. Присвойте проекту имя **ReadDeviceToCloudMessages**.

    ![][10]

2. В обозревателе решений щелкните правой кнопкой мыши проект **ReadDeviceToCloudMessages** и выберите **Управление пакетами NuGet**.

3. В окне **Диспетчер пакетов NuGet** найдите пакет **WindowsAzure.ServiceBus**, щелкните **Установить** и примите условия использования.

    После этого будут выполнены скачивание, установка и добавление ссылки на [служебную шину Azure][lnk-servicebus-nuget] со всеми ее зависимостями. С помощью этого пакета приложение может подключаться к конечной точке, совместимой с концентратором событий, в центре IoT.

4. Добавьте следующие инструкции `using` в начало файла **Program.cs**:

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Добавьте следующие поля в класс **Program**, заменяя значения заполнителей строкой подключения для центра IoT, созданного в разделе *Создание центра IoT*.

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Добавьте следующий метод в класс **Program**.

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Этот метод использует экземпляр **EventHubReceiver** для получения сообщений из всех разделов центра IoT, которые отвечают за прием сообщений, передаваемых с устройства в облако. Обратите внимание на то, что параметр `DateTime.Now` при создании объекта **EventHubReceiver** передается таким образом, чтобы получать только сообщения, отправленные после его запуска. Это удобно в тестовой среде, где можно увидеть текущий набор сообщений, но в рабочей среде код должен обрабатывать все сообщения. Дополнительные сведения см. в руководстве [Как обрабатывать сообщения, отправляемые с устройства в облако, с помощью центра IoT][lnk-processd2c-tutorial].

7. Наконец, добавьте следующие строки в метод **Main**:

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());


<!-- Links -->

[lnk-eventhubs-tutorial]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../articles/event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-processd2c-tutorial]: ../articles/iot-hub/iot-hub-csharp-csharp-process-d2c.md

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=AcomDC_0413_2016-->