<properties
	pageTitle="Начало работы с центром IoT Azure. Язык C# | Microsoft Azure"
	description="Начальное руководство по работе с центром IoT Azure для языка C#. Используя центр IoT Azure, язык C# и пакеты SDK для Интернета вещей Microsoft Azure, создайте собственное решение IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Приступая к работе с центром Azure IoT с использованием .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Введение

Центр IoT Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств Интернета вещей (IoT) и серверной частью решения. Одной из крупнейших задач, стоящих перед проектами IoT, является надежное и безопасное подключение устройств к серверной части решения. Чтобы устранить эту проблему, центр IoT предоставляет следующие возможности:

- глобальный надежный двусторонний обмен сообщениями между облаком и устройством;
- безопасную связь — используются учетные данные безопасности, уникальные для каждого устройства, и контроль доступа;
- простое подключение устройств, так как используются библиотеки устройств для большинства популярных языков и платформ.

В этом учебнике описаны следующие процедуры.

- Создание центра IoT с помощью портала Azure.
- Создание удостоверения устройства в центре IoT.
- Создание виртуального устройства, которое взаимодействует с серверной частью облака, отправляя туда данные телеметрии и получая оттуда команды.

В конце этого учебника у вас будет три консольных приложения Windows:

* **CreateDeviceIdentity** — создает удостоверение устройства и соответствующий ключ безопасности для подключения к виртуальному устройству;
* **ReadDeviceToCloudMessages** — отображает данные телеметрии, отправленные виртуальным устройством.
* **SimulatedDevice** — подключается к центру IoT с созданным ранее удостоверением устройства и отправляет сообщения с частотой один раз в секунду с использованием протокола AMQPS.

> [AZURE.NOTE] Статья [Пакеты SDK для центра IoT][lnk-hub-sdks] содержит сведения о разных пакетах SDK, которые можно использовать для создания приложений, которые будут запущены на устройствах и серверной части решения.

Для работы с этим руководством требуется:

+ Microsoft Visual Studio 2015.

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Теперь у вас есть центр IoT, а также имя узла и строка подключения, необходимые для завершения работы с этим руководством.

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

> [AZURE.NOTE] В реестре удостоверений центра IoT хранятся только идентификаторы устройств для предоставления безопасного доступа к концентратору. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок "Вкл./Выкл.", который позволяет отключить доступ для отдельного устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков центра IoT][lnk-devguide-identity].

## Получение сообщений с устройства в облако

В этом разделе вы создадите консольное приложение Windows, которое считывает сообщения, передаваемые с устройства в облако из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве по [обработке сообщений, отправляемых с устройства в облако][lnk-process-d2c-tutorial] показано, как обрабатывать такие сообщения с применением масштабирования. В руководстве по [началу работы с концентраторами событий][lnk-eventhubs-tutorial] приведены дополнительные сведения о том, как обрабатываются сообщения из концентраторов событий. Это руководство применяется к конечным точкам центра IoT, совместимым с концентраторами событий.

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

    Этот метод использует экземпляр **EventHubReceiver** для получения сообщений из всех разделов центра IoT, которые отвечают за прием сообщений, передаваемых с устройства в облако. Обратите внимание на то, что параметр `DateTime.Now` при создании объекта **EventHubReceiver** передается таким образом, чтобы получать только сообщения, отправленные после его запуска. Это удобно в тестовой среде, где можно увидеть текущий набор сообщений, но в рабочей среде код должен обрабатывать все сообщения. Дополнительные сведения см. в руководстве [Как обрабатывать сообщения, отправляемые с устройства в облако, с помощью центра IoT][lnk-process-d2c-tutorial].

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

## Создание приложения виртуального устройства

В этом разделе вам предстоит создать консольное приложение Windows, которое имитирует устройство, отправляющее сообщения с устройства в облако в центр IoT.

1. В Visual Studio добавьте классический настольный проект Windows на языке Visual C# с помощью шаблона проекта **Консольное приложение**. Убедитесь, что указана версия платформы .NET 4.5.1 или выше. Назовите проект **SimulatedDevice**.

   	![][10]

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

В этом руководстве описываются шаги по созданию клиентского устройства центра IoT. В качестве альтернативы можно использовать расширение [подключенной службы для центра Azure IoT][lnk-connected-service] (Visual Studio), чтобы добавить необходимый код в приложение клиентского устройства.


> [AZURE.NOTE] Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя][lnk-transient-faults].

## Запуск приложений

Теперь все готово к запуску приложений.

1.	В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Назначить запускаемым проектом**. Выберите **Несколько запускаемых проектов**, а затем выберите **Запуск** в качестве **действия** для проектов **ReadDeviceToCloudMessages** и **SimulatedDevice**.

   	![][41]

2.	Нажмите клавишу **F5**, чтобы запустить оба приложения. Вывод консоли приложения **SimulatedDevice** отображает сообщения, которые виртуальное устройство отправляет в центр IoT; вывод консоли приложения **ReadDeviceToCloudMessages** отображает сообщения, которые получает центр IoT.

   	![][42]

3. На плитке **Использование** на [портале Azure][lnk-portal] отображается количество сообщений, отправленных в центр:

    ![][43]


## Дальнейшие действия

В этом руководстве описана настройка нового центра IoT на портале и создание удостоверения устройства в реестре удостоверений центра. Это удостоверение устройства позволяет приложению виртуального устройства отправлять в центр сообщения, передаваемые из устройства в облако. Также описано создание приложения, которое отображает сообщения, полученные центром. Дальнейшее описание функций и сценариев центра IoT см. в следующих руководствах:

- [Отправка сообщений с облака на устройство в центре IoT][lnk-c2d-tutorial] — описывается, как отправлять сообщения на устройства и обрабатывать подтверждения о доставке от центра IoT.
- [Обработка сообщений, отправляемых с устройств в облако][lnk-process-d2c-tutorial] — описывается, как надежно обрабатывать данные телеметрии и интерактивные сообщения, поступающие от устройств.
- [Передача файлов с устройств][lnk-upload-tutorial] — описывается модель использования сообщений, отправляемых из облака на устройства, для упрощения передачи файлов с устройств.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6

<!---HONumber=AcomDC_0511_2016-->