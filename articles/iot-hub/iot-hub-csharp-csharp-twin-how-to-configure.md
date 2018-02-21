---
title: "Использование свойств двойников устройств Центра Интернета вещей Azure (.NET/.NET) | Документация Майкрософт"
description: "Настройка устройств с помощью двойников устройств Центра Интернета вещей Azure. Используйте пакет SDK для устройств Azure IoT для .NET, чтобы реализовать приложение имитации устройства, и пакет SDK для служб Azure IoT для .NET, чтобы реализовать приложение-службу, которое изменит конфигурацию устройства с помощью двойника устройства."
services: iot-hub
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dkshir
ms.openlocfilehash: 0bb18f1b289dd01866842a5193437dabdb5fd20b
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Настройка устройств с помощью требуемых свойств
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

По завершении работы с этим руководством у вас будет два консольных приложения .NET:

* **SimulateDeviceConfiguration** — приложение имитации устройства, которое ожидает обновления требуемой конфигурации и сообщает о состоянии обновления имитации конфигурации.
* **SetDesiredConfigurationAndQuery** — внутреннее приложение, которое задает требуемую конфигурацию на устройстве и выполняет запрос к процессу обновления конфигурации.

> [!NOTE]
> Статья [IoT Hub SDKs][lnk-hub-sdks] (Пакеты SDK для Центра Интернета вещей) содержит сведения о разных пакетах SDK для Azure IoT, с помощью которых можно создать приложения для устройств и внутренние приложения.
> 
> 

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

Если вы выполнили указания руководства [Начало работы с двойниками устройств][lnk-twin-tutorial], то у вас уже есть Центр Интернета вещей и удостоверение устройства **myDeviceId**. В этом случае можно перейти к разделу [Создание приложения имитации устройства][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Создание приложения имитации устройства
В этом разделе вы создадите консольное приложение .NET, которое подключается к центру как **myDeviceId**, ожидает обновления требуемой конфигурации и сообщает об обновлениях в ходе обновления имитации конфигурации.

1. В Visual Studio создайте проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения**. Назовите проект **SimulateDeviceConfiguration**.
   
    ![Новое классическое приложение устройства Windows на языке Visual C#][img-createdeviceapp]

1. В обозревателе решений щелкните правой кнопкой мыши проект **SimulateDeviceConfiguration** и выберите пункт **Управление пакетами NuGet**.
1. В окне **Диспетчер пакетов NuGet** выберите **Обзор** и найдите **microsoft.azure.devices.client**. Выберите **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Client**, и примите условия использования. В результате выполняется скачивание и установка [пакета NuGet SDK для устройств Azure IoT][lnk-nuget-client-sdk] и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Клиентское приложение в окне "Диспетчер пакетов NuGet"][img-clientnuget]
1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения устройства, записанной в предыдущем разделе.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();

1. Добавьте следующий метод в класс **Program** .
 
        public static void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }
    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. Приведенный выше код инициализирует объект **Client**, а затем получает двойник устройства для **myDeviceId**.

1. Добавьте следующий метод в класс **Program**. Этот метод задает начальные значения телеметрии на локальном устройстве, а затем обновляет двойник устройства.

        public static async void InitTelemetry()
        {
            try
            {
                Console.WriteLine("Report initial telemetry config:");
                TwinCollection telemetryConfig = new TwinCollection();
                
                telemetryConfig["configId"] = "0";
                telemetryConfig["sendFrequency"] = "24h";
                reportedProperties["telemetryConfig"] = telemetryConfig;
                Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Добавьте следующий метод в класс **Program**. Это обратный вызов, который обнаружит изменение *требуемых свойств* в двойнике устройства.

        private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            try
            {
                Console.WriteLine("Desired property change:");
                Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                var currentTelemetryConfig = reportedProperties["telemetryConfig"];
                var desiredTelemetryConfig = desiredProperties["telemetryConfig"];

                if ((desiredTelemetryConfig != null) && (desiredTelemetryConfig["configId"] != currentTelemetryConfig["configId"]))
                {
                    Console.WriteLine("\nInitiating config change");
                    currentTelemetryConfig["status"] = "Pending";
                    currentTelemetryConfig["pendingConfig"] = desiredTelemetryConfig;

                    await Client.UpdateReportedPropertiesAsync(reportedProperties);

                    CompleteConfigChange();
                }
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    Этот метод обновляет сообщаемые свойства в объекте локального двойника устройства с помощью запроса на обновление конфигурации и устанавливает для состояния значение **Pending** (Ожидание), а затем обновляет двойник устройства в службе. После успешного обновления двойника устройства изменение конфигурации завершается вызовом метода `CompleteConfigChange`, описанного в следующем пункте.

1. Добавьте следующий метод в класс **Program**. Этот метод имитирует сброс устройства, затем обновляет сообщаемые локальные свойства, устанавливая для состояния значение **Success** (Успешно), и удаляет элемент **pendingConfig**. Затем он обновляет двойник устройства в службе. 

        public static async void CompleteConfigChange()
        {
            try
            {
                var currentTelemetryConfig = reportedProperties["telemetryConfig"];

                Console.WriteLine("\nSimulating device reset");
                await Task.Delay(30000); 

                Console.WriteLine("\nCompleting config change");
                currentTelemetryConfig["configId"] = currentTelemetryConfig["pendingConfig"]["configId"];
                currentTelemetryConfig["sendFrequency"] = currentTelemetryConfig["pendingConfig"]["sendFrequency"];
                currentTelemetryConfig["status"] = "Success";
                currentTelemetryConfig["pendingConfig"] = null;

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
                Console.WriteLine("Config change complete \nPress any key to exit.");
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Наконец, добавьте следующие строки в метод **Main**:

        try
        {
            InitClient();
            InitTelemetry();

            Console.WriteLine("Wait for desired telemetry...");
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
            Console.ReadKey();
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }

   > [!NOTE]
   > В этом руководстве не имитируется поведение при одновременном обновлении конфигураций. В некоторых случаях обновление конфигурации может пройти в соответствии с изменениями целевой конфигурации, в других может потребоваться поместить изменения в очередь, а в остальных они могут быть отклонены с ошибкой. Следует учитывать желаемое поведение для конкретной конфигурации и добавить соответствующую логику перед ее изменением.
   > 
   > 
1. Выполните сборку решения, а затем запустите приложение для устройства из Visual Studio нажатием клавиши **F5**. В консоли вывода должны появиться сообщения, указывающие, что имитация устройства получает двойник устройства, настраивает телеметрию и ожидает изменения требуемого свойства. Не отключайте приложение.

## <a name="create-the-service-app"></a>Создание приложения службы
В этом разделе вы создадите консольное приложение .NET, которое обновляет *требуемые свойства* двойника устройства, связанного с **myDeviceId**, с использованием нового объекта конфигурации телеметрии. Затем оно выполняет запрос к двойникам устройств, которые хранятся в Центре Интернета вещей, и показывает разницу между требуемой и сообщаемой конфигурацией устройства.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Назовите проект **SetDesiredConfigurationAndQuery**.
   
    ![Новый проект классического приложения Windows на языке Visual C#][img-createapp]
1. В обозревателе решений щелкните правой кнопкой мыши проект **SetDesiredConfigurationAndQuery** и выберите **Управление пакетами Nuget...**
1. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure][lnk-nuget-service-sdk] и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Окно "Диспетчер пакетов NuGet"][img-servicenuget]
1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения к Центру Интернета вещей, созданному в предыдущем разделе.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Добавьте следующий метод в класс **Program** .
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }
   
    Объект **Registry** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства из службы. Этот код инициализирует объект **Registry**, извлекает двойник устройства для **myDeviceId**, а затем обновляет его требуемые свойства, используя новый объект конфигурации телеметрии.
    Затем он каждые 10 секунд запрашивает данные двойников устройств, которые хранятся в Центре Интернета вещей, и выводит требуемые и сообщаемые конфигурации телеметрии. Чтобы узнать, как создавать эффективные отчеты на всех устройствах, см. статью [Справочник по языку запросов для двойников и заданий][lnk-query].
   
   > [!IMPORTANT]
   > Это приложение выполняет запрос к Центру Интернета вещей каждые 10 секунд в качестве примера. Используйте запросы для создания представляемых пользователям отчетов на многих устройствах, а не для обнаружения изменений. Если вашему решению требуется отправка уведомлений о событиях устройства в реальном времени, используйте [уведомления двойников][lnk-twin-notifications].
   > 
   > 
1. Наконец, добавьте следующие строки в метод **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. В обозревателе решений выберите **Задать автозагружаемые проекты...** и убедитесь, что для параметра **Действие** проекта **SetDesiredConfigurationAndQuery** задано значение **Запустить**. Выполните сборку решения.
1. Запустите приложении для устройства **SimulateDeviceConfiguration**, а затем запустите приложение-службу из Visual Studio, нажав клавишу **F5**. Состояние сообщаемой конфигурации изменится с **Pending** на **Success**, а значение частоты отправки изменится с 24 часов на 5 минут.

 ![Устройство успешно настроено][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Результаты запроса выводятся после операции сообщения устройства с задержкой (не более минуты). Таким образом обеспечивается работа инфраструктуры запросов в очень широком масштабе. Для получения согласованных представлений одного двойника устройства используйте метод **getDeviceTwin** в классе **Registry**.
   > 
   > 

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы установили требуемую конфигурацию в качестве *требуемых свойств* из серверной части решения и написали код приложения для устройства, которое обнаруживает изменения и имитирует многоэтапный процесс обновления, о состоянии которого сообщается в качестве сообщаемых свойств.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (учебник [Приступая к работе с Центром Интернета вещей Azure (Node)][lnk-iothub-getstarted]);
* запланировать или выполнить операции на больших наборах устройств (см. учебник [Планирование и трансляция заданий][lnk-schedule-jobs];
* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (учебник [Use direct methods][lnk-methods-tutorial] (Использование прямых методов)).

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/devicesdknuget.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
