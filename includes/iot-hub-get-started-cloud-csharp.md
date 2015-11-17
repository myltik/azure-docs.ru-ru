## Создание удостоверения устройства

В этом разделе вам предстоит написать консольное приложение Windows, которое создает новое удостоверение устройства в центре IoT. Более подробную информацию см. в разделе **Реестр удостоверений устройств** [руководства разработчика по центру IoT][IoT Hub Developer Guide - Identity Registry]. После запуска этого консольного приложения вы получите идентификатор и ключ, которые можно использовать в качестве удостоверения устройства, при отправке сообщений, передаваемых с устройства в облако, в центр IoT.

1. Создайте в Visual Studio новый проект Visual C# для классических приложений с помощью шаблона проекта **Консольное приложение**. Присвойте проекту имя **CreateDeviceIdentity**.

	![][10]

2. В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения...**.

	Отобразится окно **Диспетчер пакетов NuGet**.

3. Убедитесь, что флажок **Включить предварительные выпуски** установлен. Выполните поиск по фразе `Microsoft Azure Devices`, нажмите кнопку **Установить**, после чего примите условия использования.

	![][11]

4. После этого будут выполнены скачивание, установка и добавление ссылки на пакет NuGet [Пакет SDK для устройств Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Добавьте следующий оператор `using` в начало файла **Program.cs**.

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Добавьте следующие поля в класс **Program**, заменяя значения заполнителей именем центра IoT, созданного в предыдущем разделе, и его строкой подключения.

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Добавьте следующий метод в класс **Program**.

		private async static Task AddDeviceAsync()
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

	Этот метод создаст новое удостоверение устройства с идентификатором **myFirstDevice** (если удостоверение с таким идентификатором уже существует, метод просто извлечет его). Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ будет использоваться имитацией устройства для подключения к центру IoT.

7. Наконец, добавьте следующие строки в метод **Main**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Запустите это приложение и запишите ключ устройства.

    ![][12]

> [AZURE.NOTE]Важно отметить, что реестр удостоверений центра IoT используется только для хранения удостоверений устройств в целях безопасного доступа, то есть для сохранения учетных данных безопасности и включения или отключения доступа отдельных устройств. Метаданные приложения устройства должны храниться в хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков центра IoT][IoT Hub Developer Guide - Identity Registry].

## Получение сообщений с устройства в облако

В этом разделе вы создадите консольное приложение Windows, которое считывает сообщения, передаваемые с устройства в облако, из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий][Event Hubs Overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве используется упрощенный модуль чтения, который не подходит для развертывания с высокой пропускной способностью. Дополнительные сведения о том, как обрабатывать сообщения центра IoT, передаваемые с устройства в облако, можно найти в руководстве [Обработка сообщений, отправляемых с устройств в облако]. Дополнительные сведения о способах обработки сообщений из концентраторов событий см. в руководстве [Приступая к работе с концентраторами событий].

1. В текущем решении Visual Studio щелкните **Файл -> Добавить -> Проект**, чтобы создать новый проект Visual C# для классических приложений с помощью шаблона проекта **Консольное приложение**. Присвойте проекту имя **ReadDeviceToCloudMessages**.

    ![][10]

2. В обозревателе решений щелкните правой кнопкой мыши проект и выберите пункт **Управление пакетами NuGet**.

    Откроется диалоговое окно **Управление пакетами NuGet**.

3. Выполните поиск `WindowsAzure.ServiceBus` и щелкните **Установить**, после чего примите условия использования.

    После этого будут выполнены скачивание, установка и добавление ссылки на [служебную шину Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) со всеми ее зависимостями.

4. Добавьте следующий оператор `using` в начало файла **Program.cs**.

        using Microsoft.ServiceBus.Messaging;

5. Добавьте следующие поля в класс **Program**, заменяя значения заполнителей именем центра IoT, созданного в предыдущем разделе, и его строкой подключения.

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Добавьте следующий метод в класс **Program**.

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Этот метод использует клиент EventHub для получения данных из всех разделов центра IoT, которые отвечают за прием сообщений, передаваемых с устройства в облако. Обратите внимание, что при создании EventHubReceiver передается параметр `DateTime.Now`. Он создает приемник, который будет принимать только сообщения, отправленные после его запуска.

7. Наконец, добавьте следующие строки в метод **Main**:

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Приступая к работе с концентраторами событий]: event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Event Hubs Overview]: event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Azure preview portal]: https://portal.azure.com/


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=Nov15_HO3-->