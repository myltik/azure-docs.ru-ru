---
title: "Использование прямых методов Центра Интернета вещей Azure (.NET/.NET) | Документация Майкрософт"
description: "Использование прямых методов Центра Интернета вещей Azure. Пакет SDK для устройств Azure IoT для .NET позволяет реализовать приложение имитации устройства, которое содержит прямой метод. Пакет SDK для служб Azure IoT для .NET нужен для того, чтобы реализовать приложение-службу, вызывающее этот прямой метод."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: dkshir
ms.openlocfilehash: 9ce1fbebb6417c10618aa182e3c1d9ddf8132fb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnet"></a>Использование прямых методов (.NET/.NET)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

В этом руководстве мы разработаем два консольных приложения .NET.

* Серверное приложение **CallMethodOnDevice.sln**, которое вызывает метод в приложении имитации устройства и выводит ответ.
* Консольное приложение **TwinSimulatedDevice.js** для имитации устройства, которое подключается к Центру Интернета вещей с созданным ранее удостоверением устройства и отвечает на вызовы метода из облака.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.
> 
> 

Для работы с этим руководством необходимы указанные ниже компоненты.

* Visual Studio 2015 или Visual Studio 2017.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Если требуется создать удостоверение устройства программным способом, см. соответствующий подраздел в статье [Подключение виртуального устройства к Центру Интернета вещей с помощью .NET][lnk-device-identity-csharp].


## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы создадите консольное приложение .NET, которое отвечает на вызов метода, выполняемый серверной частью решения.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Присвойте проекту имя **SimulatedDeviceMethods**.
   
    ![Новое классическое приложение устройства Windows на языке Visual C#][img-createdeviceapp]
    
1. В обозревателе решений щелкните правой кнопкой мыши проект **SimulateDeviceMethods** и выберите пункт **Управление пакетами NuGet...**.
1. В окне **Диспетчер пакетов NuGet** выберите **Обзор** и найдите **microsoft.azure.devices.client**. Выберите **Установить**, чтобы установить пакет **Microsoft.Azure.Devices.Client**, и примите условия использования. В результате выполняется скачивание и установка [пакета NuGet SDK для устройств Azure IoT][lnk-nuget-client-sdk] и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Клиентское приложение в окне "Диспетчер пакетов NuGet"][img-clientnuget]
1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения устройства, записанной в предыдущем разделе.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Добавьте следующий код, чтобы реализовать прямой метод на устройстве:

        static Task<MethodResponse> WriteLineToConsole(MethodRequest methodRequest, object userContext)
        {
            Console.WriteLine();
            Console.WriteLine("\t{0}", methodRequest.DataAsJson);
            Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

            string result = "'Input was written to log.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

1. И наконец, добавьте этот код в метод **Main**, чтобы открыть подключение к Центру Интернета вещей и инициализировать прослушиватель метода:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "writeLine" method
            Client.SetMethodHandlerAsync("writeLine", WriteLineToConsole, null).Wait();
            Console.WriteLine("Waiting for direct method call\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "writeLine" handler
            Client.SetMethodHandlerAsync("writeLine", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Назначить запускаемые проекты**. Щелкните **Один запускаемый проект**, а затем в раскрывающемся меню выберите проект **SimulateDeviceMethods**.        

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, повторную попытку подключения), как указано в статье MSDN [Обработка временного сбоя][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Вызов прямого метода на устройстве
В этом разделе вы создадите консольное приложение .NET, которое вызывает метод в приложении виртуального устройства и выводит ответ.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Назовите проект **CallMethodOnDevice**.
   
    ![Новый проект классического приложения Windows на языке Visual C#][img-createserviceapp]
2. В обозревателе решений щелкните правой кнопкой мыши проект **CallMethodOnDevice** и выберите **Управление пакетами NuGet**.
3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure][lnk-nuget-service-sdk] и его зависимостей, а также добавляется соответствующая ссылка.
   
    ![Окно "Диспетчер пакетов NuGet"][img-servicenuget]

4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения к Центру Интернета вещей, созданному в предыдущем разделе.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Добавьте следующий метод в класс **Program** .
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    Этот метод вызывает прямой метод с именем `writeLine` на устройстве `myDeviceId`. Затем он записывает ответ, предоставленный устройством, в консоли. Обратите внимание, что можно указать значение времени ожидания для ответа устройства.
7. Наконец, добавьте следующие строки в метод **Main** :
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение и выберите пункт **Назначить запускаемые проекты**. Щелкните **Один запускаемый проект**, а затем в раскрывающемся меню выберите проект **CallMethodOnDevice**.

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. Запустите приложение для устройства .NET **SimulateDeviceMethods**. Оно будет ожидать вызовы методов от вашего Центра Интернета вещей. 

    ![Запуск приложения для устройства][img-deviceapprun]
1. Теперь, когда устройство подключено и ожидает вызовов методов, запустите приложение .NET **CallMethodOnDevice** для вызова метода в приложении виртуального устройства. В консоли отобразится ответ устройства.
   
    ![Запуск приложения службы][img-serviceapprun]
1. Устройство отреагирует на метод, выведя следующее сообщение:
   
    ![Вызов прямого метода на устройстве][img-directmethodinvoked]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы использовали удостоверение устройства, с помощью которого приложение имитации устройства может отвечать на методы, вызванные из облака. Вы также создали приложение, вызывающее методы на устройстве и выводящее ответ устройства. 

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [Начало работы с Центром Интернета вещей]
* [Планирование заданий на нескольких устройствах (предварительная версия)][lnk-devguide-jobs]

Дополнительные сведения о расширении решения Центра Интернета вещей и планировании вызовов методов на нескольких устройствах см. в учебнике [Планирование и трансляция заданий][lnk-tutorial-jobs].

<!-- Images. -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-direct-methods/device-app-nuget.png
[img-createserviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-service-app.png
[img-servicenuget]: ./media/iot-hub-csharp-csharp-direct-methods/service-app-nuget.png
[img-deviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-device-app.png
[img-serviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-service-app.png
[img-directmethodinvoked]: ./media/iot-hub-csharp-csharp-direct-methods/direct-method-invoked.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[Начало работы с Центром Интернета вещей]: iot-hub-node-node-getstarted.md
