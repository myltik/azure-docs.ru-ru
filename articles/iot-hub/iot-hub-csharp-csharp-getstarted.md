---
title: "Приступая к работе с Центром Интернета вещей Azure (.NET) | Документация Майкрософт"
description: "Как отправлять сообщения, отправляемые с устройства в облако, с устройства в Центр Интернета вещей Azure, используя пакеты SDK для Центра Интернета вещей Azure для .NET. Вы создадите приложение имитации устройства для отправки сообщений, а также приложение службы для регистрации устройства в реестре удостоверений и чтения сообщений, отправляемых с устройства в облако, из Центра Интернета вещей."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 969e6bd55ca69e293f13b66f1a51f1d5fd1996b7
ms.lasthandoff: 03/06/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-net"></a>Подключение виртуального устройства к Центру Интернета вещей с помощью .NET
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

В конце этого руководства у вас будет три консольных приложения .NET:

* **CreateDeviceIdentity** — создает удостоверение устройства и соответствующий ключ безопасности для подключения к приложению виртуального устройства;
* **ReadDeviceToCloudMessages** — отображает данные телеметрии, отправленные приложением виртуального устройства;
* **SimulatedDevice** — подключается к Центру Интернета вещей с созданным ранее удостоверением устройства и один раз в секунду отправляет сообщения телеметрии с использованием протокола MQTT.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.
> 
> 

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Теперь у вас есть Центр Интернета вещей, а также имя узла и строка подключения Центра Интернета вещей, необходимые для завершения работы с этим руководством.

## <a name="create-a-device-identity"></a>Создание удостоверения устройства
В этом разделе объясняется, как написать консольное приложение .NET, создающее удостоверение устройства в реестре удостоверений в Центре Интернета вещей. Устройство может подключиться к Центру Интернета вещей, только если в реестре удостоверений есть соответствующая запись. Дополнительные сведения см. в разделе, посвященном реестру удостоверений, в [руководстве разработчика по Центру Интернета вещей Azure][lnk-devguide-identity]. При запуске этого консольного приложения создается уникальный идентификатор устройства и ключ, с помощью которых выполняется идентификация во время отправки сообщений из устройства в облако для центра IoT.

1. В Visual Studio добавьте в новое решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Присвойте проекту имя **CreateDeviceIdentity**, а решению — **IoTHubGetStarted**.
   
    ![Новый проект классического приложения Windows на языке Visual C#][10]
2. В обозревателе решений щелкните правой кнопкой мыши проект **CreateDeviceIdentity** и выберите пункт **Manage NuGet Packages** (Управление пакетами NuGet).
3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure][lnk-nuget-service-sdk] и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Окно "Диспетчер пакетов NuGet"][11]
4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;
5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения к Центру Интернета вещей, созданному в предыдущем разделе.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. Добавьте следующий метод в класс **Program** .
   
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
   
    Этот метод создает удостоверение устройства с идентификатором **myFirstDevice**. Если этот идентификатор устройства уже существует в реестре, код просто извлекает сведения о существующем устройстве. Затем приложение отображает первичный ключ для данного удостоверения. Этот ключ будет использоваться в приложении виртуального устройства для подключения к Центру Интернета вещей.
7. Наконец, добавьте следующие строки в метод **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();
8. Запустите это приложение и запишите ключ устройства.
   
    ![Ключ устройства, созданный с помощью приложения][12]

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только идентификаторы устройств, необходимые для безопасного доступа к Центру Интернета вещей. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок включения или выключения, который позволяет вам отключить доступ для отдельного устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве разработчика по Центру Интернета вещей][lnk-devguide-identity].
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Получение сообщений с устройства в облако
В этом разделе вы создадите консольное приложение .NET, которое считывает сообщения, передаваемые с устройства в облако из Центра Интернета вещей. Центр Интернета вещей предоставляет совместимую с [концентраторами событий Azure][lnk-event-hubs-overview] конечную точку для считывания сообщений, отправляемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве по [обработке сообщений, отправляемых с устройства в облако][lnk-process-d2c-tutorial], показано, как обрабатывать такие сообщения в больших количествах. Дополнительные сведения о способах обработки сообщений из концентраторов событий см. в руководстве [Приступая к работе с концентраторами событий][lnk-eventhubs-tutorial]. (Это руководство относится к конечным точкам, совместимым с концентраторами событий в Центре Интернета вещей.)

> [!NOTE]
> Совместимая с концентраторами событий конечная точка для чтения сообщений, отправляемых с устройства в облако, всегда использует протокол AMQP.
> 
> 

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Присвойте проекту имя **ReadDeviceToCloudMessages**.
   
    ![Новый проект классического приложения Windows на языке Visual C#][10a]
2. В обозревателе решений щелкните правой кнопкой мыши проект **ReadDeviceToCloudMessages** и выберите **Manage NuGet Packages** (Управление пакетами NuGet).
3. В окне **Диспетчер пакетов NuGet** найдите пакет **WindowsAzure.ServiceBus**, щелкните **Установить** и примите условия использования. Эта процедура выполняет загрузку и установку [служебной шины Azure][lnk-servicebus-nuget] со всеми ее зависимостями, а также добавляет соответствующую ссылку. С помощью этого пакета приложение может подключаться к конечной точке, совместимой с концентратором событий, в центре IoT.
4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using Microsoft.ServiceBus.Messaging;
        using System.Threading;
5. Добавьте следующие поля в класс **Program** . Замените значения заполнителей строкой подключения к Центру Интернета вещей, созданному в разделе "Создание Центра Интернета вещей".
   
        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;
6. Добавьте следующий метод в класс **Program** .
   
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
   
    Этот метод использует экземпляр **EventHubReceiver** для получения сообщений из всех разделов Центра Интернета вещей, которые отвечают за прием сообщений, передаваемых с устройства в облако. Обратите внимание, что при создании объекта `DateTime.Now`EventHubReceiver** параметр ** передается так, чтобы получать только сообщения, отправленные после его запуска. Этот фильтр удобно использовать в тестовой среде для просмотра текущего набора сообщений. В рабочей среде убедитесь, что ваш код обрабатывает все сообщения. Дополнительные сведения см. в руководстве [по обработке сообщений Центра Интернета вещей, отправляемых с устройства в облако][lnk-process-d2c-tutorial].
7. Наконец, добавьте следующие строки в метод **Main** :
   
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

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы создаете консольное приложение .NET, которое имитирует устройство, отправляющее сообщения с устройства в облако в Центре Интернета вещей.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Назовите проект **SimulatedDevice**.
   
    ![Новый проект классического приложения Windows на языке Visual C#][10b]
2. В обозревателе решений щелкните правой кнопкой мыши проект **SimulatedDevice** и выберите **Manage NuGet Packages** (Управление пакетами NuGet).
3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **Microsoft.Azure.Devices.Client**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Client**, и примите условия использования. В результате выполняется скачивание и установка [пакета NuGet SDK для устройств Azure IoT][lnk-device-nuget] и его зависимостей, а также добавляется соответствующая ссылка.
4. Добавьте следующий оператор `using` в начало файла **Program.cs** .
   
        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
5. Добавьте следующие поля в класс **Program** . Замените значения заполнителей на имя узла Центра Интернета вещей, полученное в разделе "Создание Центра Интернета вещей", и ключ устройства, полученный в разделе "Создание удостоверения устройства".
   
        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";
6. Добавьте следующий метод в класс **Program** .
   
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
   
                await Task.Delay(1000);
            }
        }
   
    Этот метод отправляет новое сообщение с устройства в облако каждую секунду. Сообщение содержит объект сериализации JSON с идентификатором устройства и случайное число, что позволяет имитировать датчик скорости ветра.
7. Наконец, добавьте следующие строки в метод **Main** :
   
        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey), TransportType.Mqtt);
   
        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();
   
   По умолчанию метод **Create** создает экземпляр **DeviceClient**, который использует протокол AMQP для связи с Центром Интернета вещей. Для использования протокола MQTT или HTTP используйте переопределение метода **Create**, чтобы указать протокол. Если вы используете протокол HTTPS, вам также следует добавить в свой проект пакет NuGet **Microsoft.AspNet.WebApi.Client**, чтобы включить пространство имен **System.Net.Http.Formatting**.

В этом руководстве описываются шаги по созданию приложения имитации устройства Центра Интернета вещей. Вы также можете использовать расширение [подключенной службы для Центра Интернета вещей Azure][lnk-connected-service] (Visual Studio), чтобы добавить необходимый код в приложение устройства.

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.
> 
> 

## <a name="run-the-apps"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Назначить запускаемые проекты**. Выберите **Несколько запускаемых проектов**, а затем – **Запуск** в качестве действия для проектов **ReadDeviceToCloudMessages** и **SimulatedDevice**.
   
    ![Свойства запускаемого проекта][41]
2. Нажмите клавишу **F5** , чтобы запустить оба приложения. В выходных данных консоли для приложения **SimulatedDevice** будут отображаться сообщения, которые приложение виртуального устройства отправляет в Центр Интернета вещей. В выходных данных консоли для приложения **ReadDeviceToCloudMessages** будут отображаться сообщения, которые получает центр IoT.
   
    ![Выходные данные консоли для приложений][42]
3. На плитке **Использование** на [портале Azure][lnk-portal] отображается количество сообщений, отправленных в Центр Интернета вещей.
   
    ![Плитка "Использование" на портале Azure][43]

## <a name="next-steps"></a>Дальнейшие действия
С помощью этого руководства вы настроили Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений этого центра. Это удостоверение позволяет приложению виртуального устройства отправлять в Центр Интернета вещей сообщения, передаваемые из устройства в облако. Кроме того, мы создали приложение, которое отображает сообщения, полученные Центром Интернета вещей. 

Чтобы продолжить знакомство с центром IoT и изучить другие сценарии IoT, см. следующие ресурсы:

* [Подключение устройства к Azure IoT][lnk-connect-device]
* [How to get started with device management (Node)][lnk-device-management] (Начало работы с управлением устройствами (Node))
* [Пакет SDK для шлюза IoT (бета-версия): приступая к работе с Linux][lnk-gateway-SDK]

Сведения о том, как расширить решение для Интернета вещей и обрабатывать сообщения, отправляемые с устройства в облако в большом количестве, см. [здесь][lnk-process-d2c-tutorial].

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

