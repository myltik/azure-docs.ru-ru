---
title: "Планирование заданий c помощью Центра Интернета вещей Azure (.NET или Node) | Документация Майкрософт"
description: "Планирование заданий с помощью Центра Интернета вещей Azure для вызова прямого метода на нескольких устройствах. Используйте пакет SDK для устройств Azure IoT для Node.js, чтобы реализовать приложение имитации устройства, и пакет SDK для служб Azure IoT для .NET, чтобы реализовать приложение-службу, которое выполняет задание."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: juanpere
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 85f87cb0b7de40c9425d7f752f968a529ad555fa
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="schedule-and-broadcast-jobs"></a>Планирование и трансляция заданий
[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

## <a name="introduction"></a>Введение
Центр Интернета вещей Azure — это полностью управляемая служба, которая позволяет внутреннему приложению создавать и отслеживать задания, осуществляющие планирование и обновление миллионов устройств.  Задания можно использовать для следующих действий:

* Обновление требуемых свойств
* Обновление тегов
* Вызов прямых методов

По сути, задание включает одно из этих действий, отслеживая ход его выполнения на наборе устройств (определяется запросом двойника устройства).  Например, с помощью задания внутреннее приложение может вызывать метод перезагрузки на 10 000 устройств, определенных запросом двойника устройства и запланированных в будущем.  Затем оно может отследить ход выполнения задания по мере получения и выполнения метода Reboot на каждом из этих устройств.

Дополнительные сведения о каждой из этих возможностей см. в следующих статьях:

* Двойники устройств и свойства: [Приступая к работе с двойниками устройств (предварительная версия)][lnk-get-started-twin] и [Руководство. Настройка устройств с помощью требуемых свойств (предварительная версия)][lnk-twin-props].
* Прямые методы: [Общие сведения о прямых методах и информация о вызове этих методов из Центра Интернета вещей][lnk-dev-methods] и [Использование прямых методов на устройстве Интернета вещей (Node.js)][lnk-c2d-methods].

В этом учебнике описаны следующие процедуры.

* Создание приложения виртуального устройства с прямым методом, который позволяет выполнить действие **lockDoor** посредством вызова из серверного приложения.
* Создание консольного приложения для .NET, которое с помощью задания вызывает в приложении имитации устройства прямой метод **lockDoor** и обновляет требуемые свойства с помощью задания устройства.

По завершении работы с этим руководством у вас будет консольное приложение устройства Node.js и консольное приложение серверной части .NET (C#).

**simDevice.js**, которое подключается к Центру Интернета вещей с удостоверением устройства и получает прямой метод **lockDoor**.

**ScheduleJob**, которое вызывает прямой метод в приложении виртуального устройства и обновляет требуемые свойства двойника устройства с помощью задания.

Для работы с этим учебником требуется:

* Visual Studio 2015 или Visual Studio 2017.
* Node.js 0.12.x или более поздней версии. В статье [Prepare your development environment][lnk-dev-setup] (Подготовка среды разработки) описывается, как установить Node.js для работы с этим руководством в операционной системе Windows или Linux.
* Активная учетная запись Azure. Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Планирование заданий для вызова прямого метода и обновления свойств двойника устройства
В этом разделе создается консольное приложение .NET (с помощью C#), которое инициирует удаленное действие **lockDoor** на устройстве с помощью прямого метода и обновляет свойства двойника устройства.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Назовите проект **ScheduleJob**.

    ![Новый проект классического приложения Windows на языке Visual C#][img-createapp]

1. В обозревателе решений щелкните правой кнопкой мыши проект **ScheduleJob** и выберите **Управление пакетами NuGet…**.
1. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. В результате выполняется скачивание и установка пакета NuGet [SDK для служб Интернета вещей Azure][lnk-nuget-service-sdk] и его зависимостей, а также добавляется соответствующая ссылка.

    ![Окно "Диспетчер пакетов NuGet"][img-servicenuget]
1. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;

1. Добавьте следующую инструкцию `using`, если она отсутствует в инструкциях по умолчанию.

        using System.Threading.Tasks;
        
1. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения Центра Интернета вещей, созданного в предыдущем разделе.
   
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        
1. Добавьте следующий метод в класс **Program** .
   
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
                
1. Добавьте следующий метод в класс **Program** .

        public static async Task StartMethodJob(string jobId)
        {
            CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

            JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
                "deviceId='myDeviceId'",
                directMethod,
                DateTime.Now,
                10);

            Console.WriteLine("Started Method Job");
        }

1. Добавьте следующий метод в класс **Program** .

        public static async Task StartTwinUpdateJob(string jobId)
        {
            var twin = new Twin();
            twin.Properties.Desired["Building"] = "43";
            twin.Properties.Desired["Floor"] = "3";
            twin.ETag = "*";

            JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
                "deviceId='myDeviceId'",
                twin,
                DateTime.Now,
                10);

            Console.WriteLine("Started Twin Update Job");
        }
 

1. Наконец, добавьте следующие строки в метод **Main** :
   
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

1. В обозревателе решений откройте **Задать автозагружаемые проекты...** и задайте значение **Запустить** для параметра **Действие** проекта **ScheduleJob**. Выполните сборку решения.

## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе вы создадите консольное приложение Node.js, которое отвечает на прямой метод, вызываемый из облака. Этот метод запускает перезагрузку имитации устройства и использует сообщаемые свойства для определения устройств и времени их последней перезагрузки в запросах двойников устройства.

1. Создайте пустую папку с именем **simDevice**.  В папке **simDevice** создайте файл package.json, используя следующую команду в командной строке.  Примите значения по умолчанию:
   
    ```
    npm init
    ```
1. Чтобы установить пакеты **azure-iot-device** и **azure-iot-device-mqtt**, в командной строке в папке **simDevice** выполните следующую команду.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. В текстовом редакторе создайте файл **simDevice.js** в папке **simDevice**.
1. Добавьте следующие инструкции require в начало файла **simDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
1. Добавьте переменную **connectionString**, чтобы создать с ее помощью экземпляр **клиента**. Замените заполнители значениями, подходящими для ваших настроек.
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
1. Добавьте следующую функцию для обработки метода **lockDoor**.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
1. Добавьте следующий код для регистрации обработчика для метода **lockDoor**.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
1. Сохраните и закройте файл **simDevice.js**.

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.
> 
> 

## <a name="run-the-apps"></a>Запуск приложений
Теперь все готово к запуску приложений.

1. В командной строке в папке **simDevice** выполните следующую команду, чтобы начать прослушивание прямого метода перезагрузки:
   
    ```
    node simDevice.js
    ```
1. Запустите консольное приложение C# **ScheduleJob**. Щелкните правой кнопкой мыши проект **ScheduleJob**, выберите **Отладка** и **Запустить новый экземпляр**.

1. Отобразятся выходные данные с устройства и серверных приложений.

    ![Выполнение приложений для планирования заданий][img-schedulejobs]

## <a name="next-steps"></a>Дальнейшие действия
В этом учебнике описано использование задания для планирования прямого метода на устройстве и обновления свойств двойника устройства.

Чтобы продолжить знакомство с Центром Интернета вещей и шаблонами управления устройствами, такими как удаленное обновление встроенного ПО, ознакомьтесь с [этим руководством][lnk-fwupdate].

Чтобы продолжить знакомство с Центром Интернета вещей, см. сведения в статье [Приступая к работе с архитектурой Edge Интернета вещей в Linux][lnk-iot-edge].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

