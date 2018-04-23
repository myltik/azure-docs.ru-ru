---
title: Общие сведения о пакетах SDK для Azure IoT | Документация Майкрософт
description: Руководство разработчика. Сведения и ссылки на различные пакеты SDK для устройств и служб Azure IoT, которые можно использовать для создания приложений для устройств и внутренних приложений.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71d02e3f8bf0757d7ea0622e76b95acda1fefc95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Понимание и использование пакетов SDK для Центра Интернета вещей Azure

Есть две категории пакетов средств разработки (SDK) для работы с Центром Интернета вещей:

* **Пакеты SDK для устройств** позволяют создавать приложения, работающие на ваших устройствах Интернета вещей. Эти приложения отправляют данные телеметрии в центр Интернета вещей и при необходимости получают из него сообщения, задания, методы или обновления двойников.

* **Пакеты SDK для служб** дают возможность управлять Центром Интернета вещей, а также отправлять сообщения, планировать задания, вызывать прямые методы и обновлять нужные свойства на устройствах Интернета вещей.

[Здесь][lnk-benefits-blog] описаны преимущества разработки с использованием пакетов SDK для Azure IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Пакеты SDK для устройств Azure IoT

Пакеты SDK для устройств Центра Интернета вещей Microsoft Azure содержат код, упрощающий построение устройств и приложений, которые подключаются к службам Центра Интернета вещей и управляются с помощью этих служб.

Пакет SDK для устройств Azure IoT для .NET: 
* установка из [Nuget][lnk-nuget-csharp-device];
* [исходный код][lnk-dotnet-sdk];
* [справочник по API][lnk-dotnet-ref].

Пакет SDK для Центра интернета вещей для C, написанный в соответствии со стандартом ANSI C (C99) для обеспечения переносимости и совместимости с широким диапазоном платформ:
* установка с помощью [apt-get, MBED, Arduino IDE или Nuget][lnk-c-package];
* [исходный код][lnk-c-sdk];
* [справочник по API][lnk-c-ref];

Пакет SDK для устройств Azure IoT для Java: 
* добавление в проект [Maven][lnk-maven-device];
* [исходный код][lnk-java-sdk];
* [справочник по API][lnk-java-ref].

Пакет SDK для устройств Azure IoT для Node.js: 
* установка из [npm][lnk-npm-device];
* [исходный код][lnk-node-sdk];
* [справочник по API][lnk-node-ref].

Пакет SDK для устройств Azure IoT для Python: 
* установка из [pip][lnk-pip-device];
* [исходный код][lnk-python-sdk].

> [!NOTE]
> Сведения об установке двоичных файлов и зависимостей на компьютере для разработки с помощью диспетчера пакетов, зависящего от языка или платформы, см. в файле сведений в репозиториях GitHub.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Платформа ОС и совместимость оборудования

Дополнительные сведения о совместимости пакета SDK с определенными устройствами см. в [каталоге устройств, сертифицированных по программе Microsoft Azure Certified for IoT][lnk-certified] или в описании для конкретного репозитория.

## <a name="azure-iot-service-sdks"></a>Пакеты SDK для служб Azure IoT

Пакеты SDK для службы Интернета вещей Azure содержат код, который облегчает создание приложений, взаимодействующих непосредственно с Центром Интернета вещей, для управления устройствами и безопасностью.

Пакет SDK для службы Центра Интернета вещей для .NET:
* скачивание из [Nuget][lnk-nuget-csharp-service];
* [исходный код][lnk-dotnet-sdk];
* [справочник по API][lnk-dotnet-service-ref].

Пакет SDK для службы Центра Интернета вещей для Java: 
* добавление в проект [Maven][lnk-maven-service];
* [исходный код][lnk-java-sdk];
* [справочник по API][lnk-java-service-ref].

Пакет SDK для службы Центра Интернета вещей для Node.js: 
* скачивание из [npm][lnk-npm-service];
* [исходный код][lnk-node-sdk];
* [справочник по API][lnk-node-service-ref].

Пакет SDK для службы Центра Интернета вещей для Пакет SDK для Python: 
* скачивание из [pip][lnk-pip-service];
* [исходный код][lnk-python-sdk].

Пакет SDK для службы Центра Интернета вещей для C: 
* скачивание с помощью [apt-get, MBED, Arduino IDE или Nuget][lnk-c-package];
* [исходный код][lnk-c-sdk].

> [!NOTE]
> Сведения об установке двоичных файлов и зависимостей на компьютере для разработки с помощью диспетчера пакетов, зависящего от языка или платформы, см. в файле сведений в репозиториях GitHub.


## <a name="next-steps"></a>Дополнительная информация

Другие справочные статьи в руководстве разработчика для Центра Интернета вещей:

* [IoT Hub endpoints][lnk-devguide-endpoints] (Конечные точки Центра Интернета вещей)
* [Справочник по языку запросов Центра Интернета вещей для двойников устройств и заданий][lnk-devguide-query]
* [Quotas and throttling][lnk-devguide-quotas] (Квоты и регулирование)
* [Поддержка MQTT в Центре Интернета вещей][lnk-devguide-mqtt]
* [Справочник по REST API Центра Интернета вещей][lnk-rest-ref]

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
