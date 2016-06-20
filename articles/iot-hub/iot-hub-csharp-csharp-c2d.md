<properties
	pageTitle="Отправка сообщений из облака на устройства с помощью центра IoT | Microsoft Azure"
	description="Следуйте инструкциям этого учебника, чтобы узнать, как отправлять сообщения с облака на устройства в центре IoT Azure с помощью C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/03/2016"
     ms.author="elioda"/>

# Учебник: как отправлять сообщения из облака на устройства с помощью центра IoT | Microsoft Azure

## Введение

Центр Azure IoT — полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью приложения. В учебнике [Приступая к работе с центром IoT] показано, как создать центр IoT, подготовить в нем удостоверение устройства и написать код виртуального устройства, которое отправляет сообщения с устройства в облако.

Этот учебник основан на разделе [Приступая к работе с центра IoT]. В нем показано, как отправить сообщение из облака на одно устройство, как запросить подтверждение доставки (*отзыв*) из центра IoT и получить его от серверной части облачного приложения.

Дополнительные сведения о сообщениях, отправляемых с облака на устройство, можно найти в [Руководстве разработчика по центру IoT][IoT Hub Developer Guide - C2D].

К завершению этого учебника вы запустите два консольных приложения Windows.

* **SimulatedDevice**, измененную версию приложения, созданного в учебнике [Приступая к работе с центром IoT]. Это приложение подключается к центру IoT и получает сообщения с облака на устройство.
* **SendCloudToDevice**, которое отправляет сообщение с облака в виртуальное устройство с помощью центра IoT и затем получает подтверждение его доставки.

> [AZURE.NOTE] Для центра IoT существуют пакеты SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые инструкции по подключению устройства к коду, приведенному в этом учебнике, и к центру Azure IoT в целом см. в [Центре разработчика для Azure IoT].

Для работы с этим руководством требуется:

+ Microsoft Visual Studio 2015;

+ Активная учетная запись Azure. <br/>Если у вас нет учетной записи, вы можете создать бесплатную учетную запись всего за несколько минут. Подробные сведения см. на странице [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdevelop%2Fiot%2Ftutorials%2Fc2d%2F target="\_blank").

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## Дальнейшие действия

В этом учебнике вы научились отправлять и получать сообщения с облака на устройство. Изучение функций и сценариев центра IoT можно продолжить с помощью следующих учебников:

- [Обработка сообщений, отправляемых с устройств в облако] — описывается, как надежно обрабатывать данные телеметрии и интерактивные сообщения, поступающие от устройств.
- [Передача файлов с устройств] — описывается модель использования сообщений, отправляемых из облака на устройства, для упрощения передачи файлов с устройств.

Дополнительная информация о центре IoT приведена в следующих статьях:

* [Обзор центра IoT]
* [Руководство разработчика по центру IoT]
* [Руководство по центру IoT]
* [Поддерживаемые платформы устройств и языки][Supported devices]
* [Центр разработчика IoT Azure]

<!-- Images. -->

<!-- Links -->

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Azure portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Обработка сообщений, отправляемых с устройств в облако]: iot-hub-csharp-csharp-process-d2c.md
[Передача файлов с устройств]: iot-hub-csharp-csharp-file-upload.md

[Обзор центра IoT]: iot-hub-what-is-iot-hub.md
[Руководство по центру IoT]: iot-hub-guidance.md
[Руководство разработчика по центру IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Приступая к работе с центра IoT]: iot-hub-csharp-csharp-getstarted.md
[Приступая к работе с центром IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Центр разработчика IoT Azure]: http://www.azure.com/develop/iot
[Центре разработчика для Azure IoT]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0608_2016-->