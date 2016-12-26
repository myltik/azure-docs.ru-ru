---
title: "Приступая к работе с двойниками устройств | Документация Майкрософт"
description: "В этом руководстве описано, как использовать двойники устройств"
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 4b90b2529fa6d763ed3ce9c3b4da07afeb860f0e


---
# <a name="tutorial-get-started-with-device-twins"></a>Руководство по началу работы с двойниками устройств
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

По завершении работы с этим руководством у вас будет два консольных приложения: .NET и Node.js.

* **AddTagsAndQuery.sln** — это приложение .NET, предназначенное для запуска из серверной части. Оно добавляет теги и выполняет запросы к двойникам устройств.
* **TwinSimulatedDevice.js** — это приложение Node.js, которое имитирует устройство, подключающееся к Центру Интернета вещей с использованием удостоверения устройства, созданного ранее, и сообщает о его условии подключения.

> [!NOTE]
> Статья [Пакеты SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о разных пакетах SDK Azure IoT, с помощью которых можно создать приложения устройств и серверной части.
> 
> 

Для работы с этим учебником требуется:

* Microsoft Visual Studio 2015.
* Node.js версии 0.10.x или более поздней.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Создание приложения службы
В этом разделе вы создадите консольное приложение Node.js, которое добавляет метаданные расположения к двойнику устройства, связанному с **myDeviceId**. Затем оно выполняет запрос к двойникам устройств, хранящимся в Центре Интернета вещей, и выбирает устройства, которые находятся в США, а затем выбирает устройства, сообщившие о подключении по сети мобильной связи.

1. В Visual Studio добавьте в текущее решение проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения** . Назовите проект **AddTagsAndQuery**.
   
    ![Новый проект классического приложения Windows на языке Visual C#][img-createapp]
2. В обозревателе решений щелкните правой кнопкой мыши проект **AddTagsAndQuery**, а затем **Управление пакетами Nuget**.
3. В окне **Диспетчер пакетов NuGet** нажмите кнопку **Обзор**, найдите **microsoft.azure.devices**, щелкните **Установить**, чтобы установить пакет **Microsoft.Azure.Devices**, и примите условия использования. Эта процедура выполняет загрузку и установку пакета NuGet [SDK для Центра Интернета вещей Microsoft Azure][lnk-nuget-service-sdk], после чего добавляется ссылка на пакет и его зависимости.
   
    ![Окно "Диспетчер пакетов NuGet"][img-servicenuget]
4. Добавьте следующие инструкции `using` в начало файла **Program.cs** :
   
        using Microsoft.Azure.Devices;
5. Добавьте следующие поля в класс **Program** . Замените значение заполнителя строкой подключения для центра IoT, созданного в предыдущем разделе.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. Добавьте следующий метод в класс **Program** .
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    Класс **RegistryManager** предоставляет все методы, необходимые для взаимодействия с двойниками устройства из службы. Предыдущий код сначала инициализирует объект **RegistryManager**, затем извлекает двойник устройства для **MyDeviceId** и обновляет его теги, используя сведения о требуемом расположении.
   
    После обновления он выполняет два запроса: первый выбирает только двойники устройства, расположенные на фабрике **Redmond43**, а второй уточняет условия первого запроса и выбирает устройства, подключенные по сети мобильной связи.
   
    Обратите внимание, что при создании объекта **query** предыдущий код указывает максимальное количество возвращаемых документов. Объект **Query** содержит логическое свойство **HasMoreResults**, которое можно использовать для вызова методов **GetNextAsTwinAsync** несколько раз, чтобы получить все результаты. Метод **GetNextAsJson** доступен для результатов, которые не являются двойниками устройств, включая результаты статистических запросов.
7. Наконец, добавьте следующие строки в метод **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();
8. Запустите это приложение, и в результатах для запроса ко всем устройствам, расположенным на фабрике **Redmond43**, отобразится одно устройство, а для запроса с ограничением результатов устройствами, использующими сеть мобильной связи, не отобразится ничего.
   
    ![Результаты запроса в окне][img-addtagapp]

В следующем разделе вы создадите приложение устройства, которое сообщает сведения о подключении и изменяет результат запроса в предыдущем разделе.

## <a name="create-the-device-app"></a>Создание приложения устройства
В этом разделе вы создадите консольное приложение Node.js, которое подключается к центру как **myDeviceId** и обновляет сообщаемые свойства, добавив в них сведения о подключении по сети мобильной связи.

1. Создайте пустую папку с именем **reportconnectivity**. В папке **reportconnectivity** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```
2. В командной строке в папке **reportconnectivity** выполните следующую команду, чтобы установить пакет SDK для устройства **azure-iot-device** и пакет **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. В текстовом редакторе создайте файл **ReportConnectivity.js** в папке **reportconnectivity**.
4. Добавьте следующий код в файл **ReportConnectivity.js** и замените заполнитель **{строка подключения устройства}** на строку подключения, скопированную при создании удостоверения устройства **myDeviceId**:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. После инициализации объекта **Client** предыдущий код извлекает двойник устройства для **myDeviceId** и обновляет его сообщаемое свойство, используя сведения о подключении.
5. Запуск приложения устройства
   
        node ReportConnectivity.js
   
    Отобразится сообщение `twin state reported`.
6. Теперь, когда устройство сообщило сведения о подключении, оно должно появиться в обоих запросах. Запустите приложение .NET **AddTagsAndQuery**, чтобы возобновить выполнение запросов. На этот раз **myDeviceId** должен появиться в результатах обоих запросов.
   
    ![][img-addtagapp2]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы добавили метаданные устройства в качестве тегов из приложения серверной части и написали код для приложения виртуального устройства, чтобы сообщить сведения о подключении устройства в двойнике устройства. Вы также узнали, как запрашивать эти сведения, используя язык запросов на основе SQL Центра Интернета вещей.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (руководство [Приступая к работе с Центром Интернета вещей Azure (Node)][lnk-iothub-getstarted]);
* настроить устройства с помощью требуемых свойств двойника устройства (руководство по [настройке устройств с помощью требуемых устройств][lnk-twin-how-to-configure]);
* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (руководство по [использованию прямых методов][lnk-methods-tutorial]).

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md




<!--HONumber=Nov16_HO5-->


