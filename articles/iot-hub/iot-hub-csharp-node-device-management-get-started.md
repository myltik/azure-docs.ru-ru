---
title: "Приступая к работе с функцией управления устройствами Центра Интернета вещей Azure (.NET или Node) | Документация Майкрософт"
description: "Использование функции управления устройствами в Центре Интернета вещей Azure для начала удаленной перезагрузки устройства. Используйте пакет SDK для устройств Azure IoT для Node.js, чтобы реализовать приложение имитации устройства, включающее прямой метод, и пакет SDK для служб Azure IoT для .NET, чтобы реализовать приложение-службу, вызывающее прямой метод."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: f03b8d192255a3c93284f3c5e898f68a1234644f
ms.lasthandoff: 03/07/2017


---
# <a name="get-started-with-device-management-netnode"></a>Приступая к работе с управлением устройствами (.NET или Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]
## <a name="introduction"></a>Введение
Внутренние приложения могут использовать в Центре Интернета вещей Azure примитивы, а именно двойники устройств и прямые методы, чтобы удаленно запускать и отслеживать на устройствах действия по управлению ими.  В этой статье содержатся рекомендации и код, которые можно использовать для взаимодействия внутренних приложений с устройствами с целью запуска и отслеживания процесса удаленной перезагрузки устройства с помощью Центра Интернета вещей.

Чтобы удаленно запустить и отслеживать на устройствах действия по управлению этими устройствами из облачного серверного приложения, используйте примитивы Центра Интернета вещей, такие как [двойники устройств][lnk-devtwin] и [прямые методы][lnk-c2dmethod]. В этом учебнике показано, как серверное приложение и устройство взаимодействуют для запуска и отслеживания процесса удаленной перезагрузки устройства из Центра Интернета вещей.

Прямой метод используется для запуска действий по управлению устройствами (таких как перезагрузка, сброс к параметрам по умолчанию и обновление встроенного ПО) из внутреннего приложения в облаке. Устройство отвечает за следующие действия:

* Обработка запроса метода, отправленного из Центра Интернета вещей.
* Инициализация соответствующего действия на определенном устройстве.
* Предоставление сведений об обновлении состояния в Центр Интернета вещей посредством сообщаемых свойств.

Вы можете воспользоваться серверным приложением в облаке, чтобы выполнить запросы двойника устройства для получения сведений о ходе выполнения ваших действий по управлению устройствами.

В этом учебнике описаны следующие процедуры.

* Создание экземпляра Центра Интернета вещей на портале Azure и удостоверения устройства в экземпляре Центра Интернета вещей.
* Создание приложения для имитации устройства с прямым методом, который позволяет выполнить перезагрузку устройства. Прямые методы вызываются из облака.
* Создание консольного приложения для .NET, вызывающего прямой метод перезагрузки в приложении имитации устройства с помощью Центра Интернета вещей.

По завершении работы с этим руководством у вас будет консольное приложение устройства Node.js и консольное приложение серверной части .NET (C#).

**dmpatterns_getstarted_device.js**, которое подключается к вашему центру Интернета вещей с ранее созданным идентификатором устройства, получает прямой метод перезагрузки, имитирует физическую перезагрузку и сообщает о времени последней перезагрузки.

**TriggerReboot**, которое вызывает прямой метод в приложении виртуального устройства, выводит ответ и отображает обновленные сообщаемые свойства.

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Node.js версии 0.12.x или более поздней. <br/>  В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Node.js для работы с этим учебником в ОС Windows или Linux.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Активация удаленной перезагрузки на устройстве с помощью прямого метода
В этом разделе вы создадите консольное приложение .NET (с помощью C#), которое инициирует удаленное обновление устройства с помощью прямого метода. Приложение использует запросы двойника устройства для определения времени последней перезагрузки этого устройства.

1. В Visual Studio добавьте в новое решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)**. Убедитесь, что указана версия платформы .NET 4.5.1 или более поздняя версия. Назовите проект **TriggerReboot**.

    ![Новый проект классического приложения Windows на языке Visual C#][img-createapp]

2. В обозревателе решений щелкните правой кнопкой мыши проект **TriggerReboot** и выберите **Управление пакетами NuGet**.
3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure][lnk-nuget-service-sdk] и его зависимостей, а также добавляется соответствующая ссылка.

    ![Окно "Диспетчер пакетов NuGet"][img-servicenuget]
4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения Центра Интернета вещей, созданного в предыдущем разделе, и целевого устройства.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. Добавьте следующий метод в класс **Program**.  Этот код получает двойник устройства для перезагрузки устройства и выводит сообщаемые свойства.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Добавьте следующий метод в класс **Program**.  Этот код инициирует удаленную перезагрузку на устройстве с помощью прямого метода.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Наконец, добавьте следующие строки в метод **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Выполните сборку решения.

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы сделаете следующее:

* создадите консольное приложение Node.js, отвечающее на прямой метод, вызываемый из облака;
* запустите перезагрузку имитации устройства;
* используете сообщаемые свойства в запросах двойника устройства для определения устройств и времени последней перезагрузки.

1. Создайте пустую папку с именем **manageddevice**.  В папке **manageddevice** создайте файл package.json, используя следующую команду в командной строке.  Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **manageddevice** выполните следующую команду, чтобы установить пакет SDK для устройств **azure-iot-device** и пакет **azure-iot-device-mqtt**.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. В текстовом редакторе создайте файл **dmpatterns_getstarted_device.js** в папке **manageddevice**.
4. Добавьте следующие инструкции require в начале файла **dmpatterns_getstarted_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Добавьте переменную **connectionString**, чтобы создать с ее помощью экземпляр **клиента**.  Замените connection string строкой подключения своего устройства.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Добавьте следующую функцию, чтобы реализовать прямой метод на устройстве:
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Добавьте следующий код, чтобы открыть подключение к Центру Интернета вещей и запустить прослушиватель прямого метода:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Сохраните и закройте файл **dmpatterns_getstarted_device.js**.
   
> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.


## <a name="run-the-apps"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке **manageddevice** выполните следующую команду, чтобы начать прослушивание прямого метода перезагрузки.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Запустите консольное приложение C# **TriggerReboot**. Щелкните правой кнопкой мыши проект **TriggerReboot**, выберите **Отладка**, а затем щелкните **Запустить новый экземпляр**.

3. В консоли появится ответ устройства на прямой метод.

## <a name="customize-and-extend-the-device-management-actions"></a>Настройка и расширенные возможности действий по управлению устройствами
Решения Интернета вещей позволяют расширить определенный набор шаблонов управления устройствами или добавить пользовательские шаблоны. Для этого используются примитивы — двойники устройств и методы передачи из облака на устройство. Примеры других действий по управлению устройствами: сброс к параметрам по умолчанию, обновление встроенного ПО, обновление программного обеспечения, управление питанием, управление сетями и подключениями и шифрование данных.

## <a name="device-maintenance-windows"></a>Периоды обслуживания устройств
Как правило устройства настраиваются на выполнение действий по очереди, что позволяет свести к минимуму прерывания в работе и время простоя.  Периоды обслуживания устройств — это распространенный шаблон для определения времени, когда выполняется обновление конфигурации устройства. Ваши серверные решения могут использовать необходимые свойства двойника устройства для определения и активации на устройстве политики, которая задает период обслуживания. Когда устройство получает политику периода обслуживания, оно может использовать сообщаемые свойства двойника устройства для предоставления сведений о состоянии политики. Затем серверное приложение может использовать запросы двойника устройства для подтверждения соответствия устройств и каждой политики.

## <a name="next-steps"></a>Дальнейшие действия
В этом учебнике использовался прямой метод для активации удаленной перезагрузки устройства. Здесь использовались сообщаемые свойства для передачи с устройства сведений о последней перезагрузке, а также выполняли запрос к двойнику устройства для поиска значения времени последней перезагрузки устройства из облака.

Чтобы продолжить знакомство с Центром Интернета вещей и шаблонами управления устройствами, такими как удаленное обновление встроенного ПО, см. следующие материалы:

[Учебник. Обновление встроенного ПО][lnk-fwupdate]

Дополнительные сведения о расширении решения Центра Интернета вещей и планировании вызовов методов на нескольких устройствах см. в учебнике [Планирование и трансляция заданий][lnk-tutorial-jobs].

Чтобы продолжить знакомство с Центром Интернета вещей, см. статью [Приступая к работе с пакетом SDK для шлюза Центра Интернета вещей Azure (Linux)][lnk-gateway-SDK].

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
