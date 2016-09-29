<properties
 pageTitle="Список пакетов SDK для центра Azure IoT | Microsoft Azure"
 description="Информация и ссылки на различные пакеты SDK для устройств и служб центра IoT Azure."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/13/2016"
 ms.author="dobett"/>

# Пакеты SDK для центра IoT

Эта статья содержит сведения о различных [пакетах SDK для Microsoft Azure IoT][], а также ссылки на дополнительные ресурсы.

## Пакеты SDK для устройств центра IoT

Пакеты SDK для устройств центра Microsoft Azure IoT содержат код, упрощающий построение устройств и приложений, которые подключаются к службам центра IoT и управляются с помощью этих служб.

В GitHub для скачивания доступны следующие пакеты SDK для устройств IoT:

- [Пакет SDK для устройств Azure IoT для C][], написанный в соответствии со стандартом ANSI C (C99) для обеспечения переносимости и совместимости с широким диапазоном платформ.
- [Пакет SDK для устройств Azure IoT для .NET][]
- [Пакет SDK для устройств Azure IoT для Java][]
- [Пакет SDK для устройств Azure IoT для Node.js][]
- [Пакет SDK для устройств Microsoft Azure IoT для Python 2.7][]

### Платформы ОС и аппаратная совместимость

Дополнительные сведения о совместимости с конкретными устройствами см. в следующих статьях.

- [Совместимость платформ ОС и оборудования с пакетами SDK для устройств][OS Platforms and hardware compatibility]
- [Программа сертификации Microsoft Azure для IoT][].

## Пакеты SDK для службы центра IoT

Пакеты SDK для службы Microsoft Azure IoT содержат код, который облегчает создание приложений, взаимодействующих непосредственно с центром IoT, для управления устройствами и безопасностью.

В GitHub для скачивания доступны следующие пакеты SDK для служб IoT.

- [Пакет SDK для службы Azure IoT для Node.js][]
- [Пакет SDK для службы Azure IoT для Java][]

## Пакет SDK для шлюза IoT Azure

Этот пакет SDK для шлюза IoT Azure содержит инфраструктуру и модули, предназначенные для создания решений шлюза IoT. Вы можете расширить пакет SDK, чтобы создавать шлюзы, адаптированные к любому комплексному сценарию.

[Пакет SDK для шлюза IoT Azure][] можно скачать из GitHub.

## Справочная документация по API в Интернете

Ниже приведен список ссылок на электронную справочную документацию по API для библиотек шлюзов, служб и устройств Azure IoT.

- ["Интернет вещей" (IoT) .NET][]
- [REST для центра IoT][]
- [Пакет SDK для устройств Microsoft Azure IoT для C][]
- [Пакет SDK для устройств Microsoft Azure IoT для Java][]
- [Пакет SDK для службы Microsoft Azure IoT для Java][]
- [Пакет SDK для устройств Microsoft Azure IoT для Node.js][]
- [Пакет SDK для службы Microsoft Azure IoT для Node.js][]
- [Пакет SDK для шлюза IoT Azure][]

## Дальнейшие действия

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

- [Разработка решения][lnk-design]
- [Обзор управления устройствами центра IoT с помощью примера пользовательского интерфейса][lnk-dmui]
- [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]
- [Управление центрами IoT через портал Azure][lnk-portal]

[пакетах SDK для Microsoft Azure IoT]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[Пакет SDK для устройств Azure IoT для C]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[Пакет SDK для устройств Azure IoT для .NET]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[Пакет SDK для устройств Azure IoT для Java]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[Пакет SDK для службы Azure IoT для Java]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[Пакет SDK для устройств Azure IoT для Node.js]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[Пакет SDK для службы Azure IoT для Node.js]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[Пакет SDK для устройств Microsoft Azure IoT для Python 2.7]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[OS Platforms and hardware compatibility]: iot-hub-tested-configurations.md
[Программа сертификации Microsoft Azure для IoT]: iot-hub-tested-configurations.md#microsoft-azure-certified-for-iot
[Пакет SDK для шлюза IoT Azure]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

["Интернет вещей" (IoT) .NET]: https://msdn.microsoft.com/library/mt488521.aspx
[Пакет SDK для устройств Microsoft Azure IoT для C]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[Пакет SDK для устройств Microsoft Azure IoT для Java]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[Пакет SDK для устройств Microsoft Azure IoT для Node.js]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.14/index.html
[REST для центра IoT]: https://msdn.microsoft.com/library/mt548492.aspx
[Пакет SDK для службы Microsoft Azure IoT для Java]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[Пакет SDK для службы Microsoft Azure IoT для Node.js]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.16/index.html
[Пакет SDK для шлюза IoT Azure]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->