---
title: Обновление встроенного ПО устройства c помощью Центра Интернета вещей Azure (.NET и .NET) | Документация Майкрософт
description: Запуск обновления встроенного ПО устройства с помощью функции управления устройствами в Центре Интернета вещей. С помощью пакета SDK для устройств Azure IoT для .NET можно реализовать приложение имитации устройства и пакет SDK для служб Azure IoT для .NET, чтобы реализовать приложение-службу, которое запустит процесс обновления встроенного ПО.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr;dobett
ms.openlocfilehash: 50bb2ada9cf848bdcfb4f958272ff918996bf411
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Использование функции управления устройствами для начала обновления встроенного ПО устройства (.NET и .NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Введение
В руководстве по [началу управления устройством][lnk-dm-getstarted] вы узнали, как использовать [двойник устройства][lnk-devtwin] и [прямые методы][lnk-c2dmethod] для удаленной перезагрузки устройства. В этом руководстве используются те же примитивы Центра Интернета вещей, а также содержатся рекомендации по комплексному обновлению имитации встроенного ПО.  Этот шаблон используется в реализации обновления встроенного ПО для [примера реализации устройства Raspberry Pi][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

В этом учебнике описаны следующие процедуры.

* Создание консольного приложения для .NET, вызывающего прямой метод **firmwareUpdate** в приложении имитации устройства с помощью Центра Интернета вещей.
* Создайте приложение виртуального устройства, которое реализует прямой метод **firmwareUpdate**. Этот метод запускает многоэтапный процесс, который сначала ожидает скачивания образа встроенного ПО, а затем скачивает его и применяет. В ходе выполнения каждого этапа обновления устройство использует сообщаемые свойства для продолжения процесса.

По завершении работы с этим руководством у вас будет консольное приложение устройства .NET (C#) и консольное приложение серверной части .NET (C#).

* **SimulatedDeviceFwUpdate**, которое подключается к Центру Интернета вещей с использованием удостоверения устройства, созданного ранее, получает прямой метод **firmwareUpdate**, запускает процесс с несколькими состояниями для имитации обновления встроенного ПО, состоящий из нескольких этапов (ожидание скачивания образа, скачивание нового образа и применение образа).

* **TriggerFWUpdate**, которое использует пакет SDK службы для удаленного вызова прямого метода **firmwareUpdate** на имитации устройства, выводит ответ и периодически (каждые 500 миллисекунд) отображает обновленные сообщаемые свойства.

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

Следуйте указаниям статьи [Руководство по началу работы с управлением устройствами](iot-hub-csharp-csharp-device-management-get-started.md), чтобы создать Центр Интернета вещей и получить строку подключения Центра Интернета вещей.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Активация удаленного обновления встроенного ПО на устройстве с помощью прямого метода
В этом разделе вы создадите консольное приложение .NET (с помощью C#), которое инициирует удаленное обновление встроенного ПО на устройстве. Приложение использует прямой метод для запуска обновления, а также применяет запросы двойников устройства для периодического получения состояния обновления активного встроенного ПО.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Назовите проект **TriggerFWUpdate**.

    ![Новый проект классического приложения Windows на языке Visual C#][img-createserviceapp]

2. В обозревателе решений щелкните правой кнопкой мыши проект **TriggerFWUpdate** и выберите **Управление пакетами NuGet**.
3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure][lnk-nuget-service-sdk] и его зависимостей, а также добавляется соответствующая ссылка.

    ![Окно "Диспетчер пакетов NuGet"][img-servicenuget]
4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Добавьте следующие поля в класс **Program** . Замените несколько значений заполнителей строкой подключения Центра Интернета вещей, созданного в предыдущем разделе, и идентификатором устройства.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Добавьте следующий метод в класс **Program**. Этот метод опрашивает двойник устройства для получения обновленного состояния каждые 500 миллисекунд. Он выполняет запись в консоль, только если состояние действительно изменилось. Для этого образца, чтобы предотвратить использование дополнительных сообщений Центра Интернета вещей в вашей подписке, опрос останавливается, когда устройство имеет состояние **applyComplete** или состояние ошибки.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Добавьте следующий метод в класс **Program** .

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Наконец, добавьте следующие строки в метод **Main** . При этом создается диспетчер реестра для считывания двойника устройства, запускается задача опроса в рабочем потоке, а затем запускается обновление встроенного ПО.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Выполните сборку решения.

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе выполняются следующие действия:

* создадите консольное приложение .NET, отвечающее на прямой метод, вызываемый из облака;
* смоделируете обновление встроенного ПО, запущенное внутренней службой с помощью прямого метода;
* используете сообщаемые свойства в запросах двойника устройства для определения устройств и времени последнего обновления встроенного ПО.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Присвойте проекту имя **SimulateDeviceFWUpdate**.
   
    ![Новое классическое приложение устройства Windows на языке Visual C#][img-createdeviceapp]
    
2. В обозревателе решений щелкните правой кнопкой мыши проект **SimulateDeviceFWUpdate** и выберите **Управление пакетами NuGet**.
3. В окне **Диспетчер пакетов NuGet** выберите **Обзор** и найдите **microsoft.azure.devices.client**. Выберите **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Client**, и примите условия использования. В результате выполняется скачивание и установка [пакета NuGet SDK для устройств Azure IoT][lnk-nuget-client-sdk] и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Клиентское приложение в окне "Диспетчер пакетов NuGet"][img-clientnuget]
4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения устройства, записанной в разделе **Создание удостоверения устройства**.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Добавьте следующий метод, чтобы снова отправить отчет о состоянии в облако с помощью двойника устройства: 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Добавьте следующий метод для моделирования скачивания образа встроенного ПО:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Добавьте следующий метод для моделирования применения образа встроенного ПО к устройству:
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Добавьте указанный ниже метод для моделирования ожидания скачивания образа встроенного ПО. Обновите состояние на **waiting** и очистите остальные свойства обновления встроенного ПО на двойнике. Эти свойства очищаются, чтобы удалить все имеющиеся значения из предыдущих обновлений встроенного ПО. Это необходимо, так как сообщаемые свойства отправляются как операция PATCH (разностные значения), а не как операция PUT (полный набор свойств, которые заменяют все предыдущие значения). Как правило, устройства информируются о доступном обновлении, а политика, определяемая администратором, активирует скачивание и применения обновления на устройстве. В этой функции применяется логика включения политики. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Добавьте указанный ниже метод, чтобы выполнить скачивание. Он обновляет состояние на **downloading** с помощью двойника устройства, вызывает метод имитации скачивания и сообщает о состоянии **downloadComplete** или **downloadFailed** с помощью двойника в зависимости от результатов операции скачивания. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Добавьте указанный ниже метод, чтобы применить образ. Он обновляет состояние на **applying** с помощью двойника устройства, вызывает метод имитации применения и меняет состояние на **applyComplete** или **applyFailed** с помощью двойника в зависимости от результатов операции применения. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Добавьте следующий метод для последовательного перехода операции обновления встроенного ПО от состояния ожидания скачивания образа до применения его к устройству:
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Добавьте указанный ниже метод, чтобы обработать прямой метод **updateFirmware** из облака. Он извлекает URL-адрес для обновления встроенного ПО из полезных данных сообщения и передает его в задачу **doUpdate**, которая запускается в другом потоке ThreadPool. Затем он немедленно возвращает ответ метода в облако.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Этот метод активирует смоделированное обновление для запуска в качестве **задачи**, а затем немедленно отвечает на вызов метода, сообщая службе, что обновление встроенного ПО началось. Сведения о состоянии и завершении обновления будут отправлены в службу с помощью сообщаемых свойств двойника устройств. Мы отвечаем на вызов метода во время запуска обновления, а не после его завершения, так как:
> * Реальный процесс обновления, скорее всего, длится дольше, чем время ожидания вызова метода.
> * Для реального процесса обновления, скорее всего, потребуется перезагрузка, которая повторно запустит это приложение. При этом объект **MetodRequest** будет отключен. (Тем не менее обновление сообщаемых свойств допускается даже после перезагрузки.) 

14. И наконец, добавьте этот код в метод **Main**, чтобы открыть подключение к Центру Интернета вещей и инициализировать прослушиватель метода:
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Выполните сборку решения.       

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.


## <a name="run-the-apps"></a>Запуск приложений
Теперь все готово к запуску приложений.
1. Запустите приложение для устройства .NET **SimulatedDeviceFWUpdate**.  Щелкните правой кнопкой мыши проект **SimulatedDeviceFWUpdate**, выберите **Отладка**, а затем щелкните **Запустить новый экземпляр**. Оно будет ожидать вызовы методов от вашего Центра Интернета вещей. 

2. Когда устройство подключено и ожидает вызовов методов, запустите приложение .NET **TriggerFWUpdate** для вызова метода обновления встроенного ПО в приложении виртуального устройства. Щелкните правой кнопкой мыши проект **TriggerFWUpdate**, выберите **Отладка**, а затем щелкните **Запустить новый экземпляр**. Должна отобразиться последовательность обновления, записанная в консоли **SimulatedDeviceFWUpdate**, а также последовательность, сообщаемая с помощью сообщаемых свойств устройства в консоли **TriggerFWUpdate**. Обратите внимание, что этот процесс занимает несколько секунд. 
   
    ![Запуск приложений служб и устройств][img-combinedrun]


## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы использовали прямой метод для активации удаленного обновления встроенного ПО на устройстве. Также вы использовали переданные свойства для отслеживания хода обновления встроенного ПО.

Дополнительные сведения о расширении решения Центра Интернета вещей и планировании вызовов методов на нескольких устройствах см. в учебнике [Планирование и трансляция заданий][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/