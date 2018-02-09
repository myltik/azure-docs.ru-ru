---
title: "Приступая к работе с Центром Интернета вещей Azure (.NET) | Документация Майкрософт"
description: "Узнайте, как отправлять сообщения из устройства в облако в Центр Интернета вещей Azure с помощью пакетов SDK для Центра Интернета вещей для .NET. Создайте имитированное устройство и приложения службы для регистрации устройства, отправки сообщений и чтения сообщений из Центра Интернета вещей."
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
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ea8979f21fcc8a85e80db9b49377ba9a48836e2
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="connect-your-device-to-your-iot-hub-using-net"></a>Подключение устройства к Центру Интернета вещей с помощью .NET

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

В конце этого руководства у вас будет три консольных приложения .NET:

* **CreateDeviceIdentity** — создает удостоверение устройства и соответствующий ключ безопасности для подключения к приложению устройства.
* **ReadDeviceToCloudMessages** — отображает данные телеметрии, отправленные приложением устройства.
* **SimulatedDevice** — подключается к Центру Интернета вещей с созданным ранее удостоверением устройства и один раз в секунду отправляет сообщения телеметрии с использованием протокола MQTT.

Следуйте инструкциям из этого руководства, чтобы создать примеры приложений с нуля. Также вы можете [загрузить](https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app/archive/master.zip) или клонировать готовые решения Visual Studio с Github:

```cmd/sh
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Теперь у вас есть Центр Интернета вещей, а также имя узла и строка подключения Центра Интернета вещей, необходимые для завершения работы с этим руководством.

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>Получение сообщений с устройства в облако

В этом разделе вы создадите консольное приложение .NET, которое считывает сообщения, передаваемые с устройства в облако из Центра Интернета вещей. Центр Интернета вещей предоставляет совместимую с [концентраторами событий Azure][lnk-event-hubs-overview] конечную точку для считывания сообщений, отправляемых с устройства в облако. Для простоты в этом руководстве создается базовый модуль чтения, который не подходит для развертывания с высокой пропускной способностью. В руководстве по [обработке сообщений, отправляемых с устройства в облако][lnk-process-d2c-tutorial], показано, как обрабатывать такие сообщения в больших количествах. Дополнительные сведения о способах обработки сообщений из концентраторов событий см. в руководстве [Приступая к работе с концентраторами событий][lnk-eventhubs-tutorial]. (Это руководство относится к конечным точкам, совместимым с концентраторами событий в Центре Интернета вещей.)

> [!NOTE]
> Совместимая с концентраторами событий конечная точка для чтения сообщений, отправляемых с устройства в облако, всегда использует протокол AMQP.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Присвойте проекту имя **ReadDeviceToCloudMessages**.

    ![Новый проект классического приложения Windows на языке Visual C#][10a]

1. В обозревателе решений щелкните правой кнопкой мыши проект **ReadDeviceToCloudMessages** и выберите **Manage NuGet Packages** (Управление пакетами NuGet).

1. В окне **Диспетчер пакетов NuGet** найдите пакет **WindowsAzure.ServiceBus**, щелкните **Установить** и примите условия использования. Эта процедура выполняет загрузку и установку [служебной шины Azure][lnk-servicebus-nuget] со всеми ее зависимостями, а также добавляет соответствующую ссылку. С помощью этого пакета приложение может подключаться к конечной точке, совместимой с концентратором событий, в Центре Интернета вещей.

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```csharp
    using Microsoft.ServiceBus.Messaging;
    using System.Threading;
    ```

1. Добавьте следующие поля в класс **Program** . Замените значения заполнителей строкой подключения к Центру Интернета вещей, созданному в разделе "Создание центра Интернета вещей".

    ```csharp
    static string connectionString = "{iothub connection string}";
    static string iotHubD2cEndpoint = "messages/events";
    static EventHubClient eventHubClient;
    ```

1. Добавьте следующий метод в класс **Program** .

    ```csharp
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
    ```

    Этот метод использует экземпляр **EventHubReceiver** для получения сообщений из всех разделов Центра Интернета вещей, которые отвечают за прием сообщений, передаваемых с устройства в облако. Обратите внимание, что при создании объекта `DateTime.Now`EventHubReceiver **параметр**  передается так, чтобы получать только сообщения, отправленные после его запуска. Этот фильтр удобно использовать в тестовой среде для просмотра текущего набора сообщений. В рабочей среде убедитесь, что ваш код обрабатывает все сообщения. Дополнительные сведения см. в руководстве [по обработке сообщений Центра Интернета вещей, отправляемых с устройства в облако][lnk-process-d2c-tutorial].

1. Наконец, добавьте следующие строки в метод **Main** :

    ```csharp
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
    ```

## <a name="create-a-device-app"></a>Создание приложения устройства

В этом разделе вы создаете консольное приложение .NET, которое имитирует устройство, отправляющее сообщения с устройства в облако в Центре Интернета вещей.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Назовите проект **SimulatedDevice**.

    ![Новый проект классического приложения Windows на языке Visual C#][10b]

1. В обозревателе решений щелкните правой кнопкой мыши проект **SimulatedDevice** и выберите **Manage NuGet Packages** (Управление пакетами NuGet).

1. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **Microsoft.Azure.Devices.Client**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Client**, и примите условия использования. В результате выполняется скачивание и установка [пакета NuGet SDK для устройств Azure IoT][lnk-device-nuget] и его зависимостей, а также добавляется соответствующая ссылка.

1. Добавьте следующий оператор `using` в начало файла **Program.cs** .

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Newtonsoft.Json;
    ```

1. Добавьте следующие поля в класс **Program** . Замените `{iot hub hostname}` на имя узла Центра Интернета вещей, полученное в разделе "Создание Центра Интернета вещей". Замените `{device key}` на ключ устройства, полученный в разделе "Создание удостоверение устройства".

    ```csharp
    static DeviceClient deviceClient;
    static string iotHubUri = "{iot hub hostname}";
    static string deviceKey = "{device key}";
    ```

1. Добавьте следующий метод в класс **Program** .

    ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
    ```

    Этот метод отправляет новое сообщение с устройства в облако каждую секунду. Сообщение содержит объект сериализации JSON с идентификатором устройства и случайные числа, что позволяет имитировать датчик температуры и влажности.

1. Наконец, добавьте следующие строки в метод **Main** :

    ```csharp
    Console.WriteLine("Simulated device\n");
    deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey ("myFirstDevice", deviceKey), TransportType.Mqtt);
    deviceClient.ProductInfo = "HappyPath_Simulated-CSharp";
    SendDeviceToCloudMessagesAsync();
    Console.ReadLine();
    ```

    По умолчанию метод **Create** в приложении .NET Framework создает экземпляр **DeviceClient**, который использует протокол AMQP для связи с Центром Интернета вещей. Для использования протокола MQTT или HTTPS используйте переопределение метода **Create**, чтобы указать протокол. Клиенты UWP и PCL по умолчанию используют протокол HTTPS. Если вы используете протокол HTTPS, вам также следует добавить в свой проект пакет NuGet **Microsoft.AspNet.WebApi.Client**, чтобы включить пространство имен **System.Net.Http.Formatting**.

В этом руководстве описываются шаги по созданию приложения устройства Центра Интернета вещей. Вы также можете использовать расширение [подключенной службы для Центра Интернета вещей Azure][lnk-connected-service] (Visual Studio), чтобы добавить необходимый код в приложение устройства.

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.

## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Назначить запускаемые проекты**. Выберите **Несколько запускаемых проектов**, а затем – **Запуск** в качестве действия для проектов **ReadDeviceToCloudMessages** и **SimulatedDevice**.

    ![Свойства запускаемого проекта][41]

1. Нажмите клавишу **F5** , чтобы запустить оба приложения. В выходных данных консоли для приложения **SimulatedDevice** будут отображаться сообщения, которые приложение устройства отправляет в Центр Интернета вещей. В выходных данных консоли для приложения **ReadDeviceToCloudMessages** будут отображаться сообщения, которые получает Центр Интернета вещей.

    ![Выходные данные консоли для приложений][42]

1. На плитке **Использование** на [портале Azure][lnk-portal] отображается количество сообщений, отправленных в Центр Интернета вещей.

    ![Плитка "Использование" на портале Azure][43]

## <a name="next-steps"></a>Дополнительная информация

С помощью этого руководства вы настроили Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений этого Центра. Это удостоверение позволяет приложению устройства отправлять в Центр Интернета вещей сообщения, передаваемые из устройства в облако. Кроме того, мы создали приложение, которое отображает сообщения, полученные Центром Интернета вещей.

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [Подключение устройства к Azure IoT][lnk-connect-device]
* [How to get started with device management (Node)][lnk-device-management] (Начало работы с управлением устройствами (Node))
* [Explore Azure IoT Edge architecture on Linux][lnk-iot-edge] (Приступая к работе с архитектурой Azure IoT Edge в Linux)

Сведения о том, как расширить решение для Интернета вещей и обрабатывать сообщения, отправляемые с устройства в облако в большом количестве, см. [здесь][lnk-process-d2c-tutorial].

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
