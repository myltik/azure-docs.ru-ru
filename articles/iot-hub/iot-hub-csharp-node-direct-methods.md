---
title: "Использование прямых методов (C#) Центра Интернета вещей Azure | Документация Майкрософт"
description: "В этом руководстве описано использование прямых методов"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: f2af421140f4f6640578a372993c2553f8c590a4


---
# <a name="tutorial-use-direct-methods-c"></a>Руководство по использованию прямых методов (C#)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

По завершении работы с этим руководством у вас будет два консольных приложения: .NET и Node.js.

* **CallMethodOnDevice.sln** — это приложение .NET, предназначенное для запуска из серверной части, которое вызывает метод в приложении виртуального устройства и отображает ответ.
* **TwinSimulatedDevice.js** — это приложение Node.js, имитирующее устройство, которое подключается к Центру Интернета вещей с созданным ранее удостоверением устройства и отвечает на метод, вызванный облаком.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.
> 
> 

Для работы с этим учебником требуется:

* Microsoft Visual Studio 2015.
* Node.js версии 0.10.x или более поздней.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы создадите консольное приложение Node.js, отвечающее на метод, вызванный из серверной части.

1. Создайте пустую папку с именем **simulateddevice**. В папке **simulateddevice** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. Чтобы установить **azure-iot-device** и пакеты **azure-iot-device-amqp**, в командной строке в папке **simulateddevice** выполните следующую команду:
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. В текстовом редакторе создайте файл **SimulatedDevice.js** в папке **simulateddevice**.
4. Добавьте следующие инструкции `require` в начало файла **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Добавьте переменную **connectionString** , чтобы создать с ее помощью клиент устройства. Замените **{строка подключения устройства}** на строку подключения, созданную в разделе *Создание удостоверения устройства*:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Чтобы реализовать прямой метод на устройстве, добавьте следующую функцию:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Откройте подключение к Центру Интернета вещей и начните инициализацию прослушивателя метода.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Сохраните и закройте файл **SimulatedDevice.js** .

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повтора (например, повторную попытку подключения), как указано в статье MSDN [Обработка временного сбоя][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Вызов прямого метода на устройстве
В этом разделе вы создадите консольное приложение .NET, которое вызывает метод в приложении виртуального устройства и выводит ответ.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Назовите проект **CallMethodOnDevice**.
   
    ![Новый проект классического приложения Windows на языке Visual C#][10]
2. В обозревателе решений щелкните правой кнопкой мыши проект **CallMethodOnDevice** и выберите **Управление пакетами NuGet**.
3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. Эта процедура выполняет загрузку и установку пакета NuGet [SDK для Центра Интернета вещей Microsoft Azure][lnk-nuget-service-sdk], после чего добавляется ссылка на пакет и его зависимости.
   
    ![Окно "Диспетчер пакетов NuGet"][11]

4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения для центра IoT, созданного в предыдущем разделе.
   
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

## <a name="run-the-applications"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке **simulateddevice** выполните следующую команду, чтобы начать прослушивать вызовы из Центра Интернета вещей:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. Теперь, когда устройство подключено и ожидает вызовов методов, запустите приложение .NET **CallMethodOnDevice** для вызова метода в приложении виртуального устройства. В консоли отобразится ответ устройства.
   
    ![][8]
3. Устройство отреагирует на метод и выведет сообщение, а приложение, вызвавшее метод, выведет ответ устройства:
   
    ![][9]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы использовали удостоверение устройства, с помощью которого приложение имитации устройства может отвечать на методы, вызванные из облака. Вы также создали приложение, вызывающее методы на устройстве и выводящее ответ устройства. 

Чтобы продолжить знакомство с центром IoT и изучить другие сценарии IoT, см. следующие ресурсы:

* [Начало работы с Центром Интернета вещей]
* [Планирование заданий на нескольких устройствах (предварительная версия)][lnk-devguide-jobs]

Дополнительные сведения о расширении решения Центра Интернета вещей и планировании вызовов методов на нескольких устройствах см. в руководстве [Schedule and broadcast jobs (Node)][lnk-tutorial-jobs] (Планирование и трансляция заданий (Node)).

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/create-identity-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Начало работы с Центром Интернета вещей]: iot-hub-node-node-getstarted.md



<!--HONumber=Nov16_HO5-->


