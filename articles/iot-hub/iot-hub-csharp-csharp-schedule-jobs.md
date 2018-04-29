---
title: Планирование заданий c помощью Центра Интернета вещей Azure (.NET и .NET) | Документация Майкрософт
description: Планирование заданий с помощью Центра Интернета вещей Azure для вызова прямого метода на нескольких устройствах. Используйте пакеты SDK для устройств Azure IoT для .NET, чтобы реализовать приложения имитации устройства и приложение службы, на которых будет выполнено задание.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 012/16/2018
ms.author: v-masebo;dobett
ms.openlocfilehash: 76c8d3739b2af3c010cd80585c93c097fc9eb466
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Планирование и трансляция заданий (.NET и .NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Центр Интернета вещей Azure позволяет планировать и отслеживать задания по обновлению для миллионов устройств. Что можно сделать с помощью заданий?

* Обновление требуемых свойств
* Обновление тегов
* Вызов прямых методов

Задание выступает в роли оболочки для одного из этих действий и отслеживает его выполнение для определенного набора устройств в соответствии с запросом на двойнике устройства. Например, внутреннее приложение может использовать задание для вызова прямого метода перезапуска на 10 000 устройств. Вы можете определить набор устройств с помощью запроса на двойнике устройства и запланировать момент времени в будущем для выполнения задания. Задание отслеживает ход выполнения по мере того, как каждое из устройств получает вызов и выполняет прямой метод перезагрузки.

Дополнительные сведения об этих возможностях см. в указанных ниже статьях.

* Двойники устройств и свойства: [Приступая к работе с двойниками устройств (предварительная версия)][lnk-get-started-twin] и [Руководство. Настройка устройств с помощью требуемых свойств (предварительная версия)][lnk-twin-props].
* Прямые методы: [Общие сведения о прямых методах и информация о вызове этих методов из Центра Интернета вещей][lnk-dev-methods] и [Использование прямых методов на устройстве Интернета вещей (Node.js)][lnk-c2d-methods].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

В этом учебнике описаны следующие процедуры.

* Создание приложения для устройств, которое реализует прямой метод **LockDoor**, вызываемый внутренним приложением.
* Создание внутреннего приложения, которое создает задание для вызова прямого метода **LockDoor** на нескольких устройствах. Еще одно задание отправляет обновления требуемых свойств на несколько устройств.

По завершении работы с этим руководством у вас будет два консольных приложения .NET (C#):

**SimulateDeviceMethods**, которое подключается к Центру Интернета вещей и реализует прямой метод **LockDoor**.

**ScheduleJob** использует задания для вызова прямого метода **LockDoor** и для обновления на нескольких устройствах свойств, установленных на двойнике устройства.

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы создадите консольное приложение .NET, которое отвечает на вызов прямого метода, выполняемый серверной частью решения.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Присвойте проекту имя **SimulatedDeviceMethods**.
   
    ![Новое классическое приложение устройства Windows на языке Visual C#][img-createdeviceapp]
    
1. В обозревателе решений щелкните правой кнопкой мыши проект **SimulateDeviceMethods** и выберите пункт **Управление пакетами NuGet...**.

1. В окне **Диспетчер пакетов NuGet** выберите **Обзор** и найдите **microsoft.azure.devices.client**. Выберите **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Client**, и примите условия использования. В результате выполняется скачивание и установка [пакета NuGet SDK для устройств Azure IoT][lnk-nuget-client-sdk] и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Клиентское приложение в окне "Диспетчер пакетов NuGet"][img-clientnuget]

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения устройства, записанной в предыдущем разделе.

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;

1. Add the following to implement the direct method on the device:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }

1. Add the following to implement the device twins listener on the device:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. И наконец, добавьте этот код в метод **Main**, чтобы открыть подключение к Центру Интернета вещей и инициализировать прослушиватель метода:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. Сохраните результаты работы и создайте решение.         

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, повторную попытку подключения), как указано в статье MSDN [Обработка временного сбоя][lnk-transient-faults].
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Планирование заданий для вызова прямого метода и обновления свойств двойника устройства

В этом разделе вы создадите консольное приложение .NET на языке C#, которое использует задания для вызова прямого метода **LockDoor** и для обновления свойств на нескольких устройствах.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Назовите проект **ScheduleJob**.

    ![Новый проект классического приложения Windows на языке Visual C#][img-createapp]

1. В обозревателе решений щелкните правой кнопкой мыши проект **ScheduleJob** и выберите **Управление пакетами NuGet…**.

1. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure][lnk-nuget-service-sdk] и его зависимостей, а также добавляется соответствующая ссылка.

    ![Окно "Диспетчер пакетов NuGet"][img-servicenuget]

1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Добавьте следующую инструкцию `using`, если она отсутствует в инструкциях по умолчанию.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Добавьте следующие поля в класс **Program** . Замените заполнители строкой подключения Центра Интернета вещей, созданной в предыдущем разделе, и именем устройства.

    ```csharp
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Добавьте следующий метод в класс **Program** .

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Добавьте следующий метод в класс **Program** .

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Добавьте другой метод в класс **Program**.

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Дополнительные сведения о синтаксисе запросов см. в статье [Язык запросов Центра Интернета вещей для двойников устройств, заданий и маршрутизации сообщений][lnk-query].
    > 

1. Наконец, добавьте следующие строки в метод **Main** :

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Сохраните результаты работы и создайте решение. 


## <a name="run-the-apps"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В обозревателе решений Visual Studio щелкните решение правой кнопкой мыши и выберите пункт **Сборка**. **Несколько запускаемых проектов**. Убедитесь, что `SimulateDeviceMethods` находится в верхней части списка, за которым следует `ScheduleJob`. Установите значение **Запуск** для действий и щелкните **ОК**.

1. Запустите проекты, щелкнув **Запуск**, или перейдите к меню **Отладка** и щелкните **Начать отладку**.

1. Отобразятся выходные данные с устройства и серверных приложений.

    ![Выполнение приложений для планирования заданий][img-schedulejobs]


## <a name="next-steps"></a>Дополнительная информация

В этом учебнике описано использование задания для планирования прямого метода на устройстве и обновления свойств двойника устройства.

Чтобы продолжить знакомство с Центром Интернета вещей и шаблонами управления устройствами, такими как удаленное обновление встроенного ПО, ознакомьтесь с [этим руководством][lnk-fwupdate].

Сведения о развертывании решений на базе искусственного интеллекта на пограничных устройствах с помощью Azure IoT Edge см. в статье [Развертывание Azure IoT Edge на имитированном устройстве в Linux (предварительная версия)][lnk-iot-edge].

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: iot-hub-csharp-csharp-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-csharp-csharp-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-csharp-csharp-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language
