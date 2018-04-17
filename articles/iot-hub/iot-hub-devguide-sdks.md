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
ms.openlocfilehash: aec2126369f45a89050dbd8b2d3cae7e00ccb8ed
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="understand-and-use-azure-iot-sdks"></a>Общие сведения о пакетах SDK для Azure IoT и их использование

Есть три категории пакетов средств разработки программного обеспечения для работы с Центром Интернета вещей:

* **Пакеты SDK для устройств** позволяют создавать приложения, работающие на ваших устройствах Интернета вещей. Эти приложения отправляют данные телеметрии в центр Интернета вещей и при необходимости получают из него сообщения.

* **Пакеты SDK для служб** позволяют управлять Центром Интернета вещей и при необходимости отправлять сообщения на устройства Интернета вещей.

* **Azure IoT Edge** позволяет создавать шлюзы для устройств, которые не используют ни один из поддерживаемых протоколов. Шлюзы также могут обрабатывать сообщения на периферии.

Пакеты SDK предоставляются для поддержки нескольких языков программирования.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Пакеты SDK для устройств Azure IoT

Пакеты SDK для устройств Центра Интернета вещей Microsoft Azure содержат код, упрощающий построение устройств и приложений, которые подключаются к службам Центра Интернета вещей и управляются с помощью этих служб.

В GitHub для скачивания доступны следующие пакеты SDK для устройств Azure IoT:

* [Пакет SDK для устройств Azure IoT для .NET][lnk-dotnet-device-sdk]
* [Пакет SDK для устройств Azure IoT для Java][lnk-java-device-sdk]
* [Пакет SDK для устройств Azure IoT для Node.js][lnk-node-device-sdk]
* [Пакет SDK для устройств Azure IoT для Python][lnk-python-device-sdk]
* [Пакет SDK для устройств Azure IoT для C][lnk-c-device-sdk], написанный в соответствии со стандартом ANSI C (C99) для обеспечения переносимости и совместимости с широким диапазоном платформ. Существуют две клиентские библиотеки устройств для C: низкоуровневая **iothub_client** и  **сериализатор**.

> [!NOTE]
> Сведения об установке двоичных файлов и зависимостей на компьютере для разработки с помощью диспетчера пакетов, зависящего от языка или платформы, см. в файле сведений в репозиториях GitHub.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Платформа ОС и совместимость оборудования

Дополнительные сведения о совместимости пакета SDK с определенными устройствами см. в [каталоге устройств, сертифицированных по программе Microsoft Azure Certified for IoT][lnk-certified].

## <a name="azure-iot-service-sdks"></a>Пакеты SDK для служб Azure IoT

Пакеты SDK для службы Интернета вещей Azure содержат код, который облегчает создание приложений, взаимодействующих непосредственно с Центром Интернета вещей, для управления устройствами и безопасностью.

В GitHub для скачивания доступны следующие пакеты SDK для служб Azure IoT:

* [Пакет SDK для служб Azure IoT для .NET][lnk-dotnet-service-sdk]
* [Пакет SDK для служб Azure IoT для Java][lnk-java-service-sdk]
* [Пакет SDK для служб Azure IoT для Node.js][lnk-node-service-sdk]
* [Пакет SDK для службы Интернета вещей Azure для Python][lnk-python-service-sdk]
* [Пакет SDK для служб Azure IoT для .NET][lnk-c-service-sdk].

> [!NOTE]
> Сведения об установке двоичных файлов и зависимостей на компьютере для разработки с помощью диспетчера пакетов, зависящего от языка или платформы, см. в файле сведений в репозиториях GitHub.

## <a name="azure-iot-edge"></a>Edge Интернета вещей Azure

Edge Интернета вещей Azure содержит инфраструктуру и модули, предназначенные для создания решений шлюза Интернета вещей. Чтобы создавать шлюзы, адаптированные к любому комплексному сценарию, вы можете расширить Edge Интернета вещей.

Вы можете скачать [Edge Интернета вещей Azure][lnk-iot-edge] с GitHub.

## <a name="online-api-reference-documentation"></a>Справочная документация по API в Интернете

Ниже приведен список ссылок на электронную справочную документацию по API для библиотек шлюзов, служб и устройств Интернета вещей Azure.

* [Интернет вещей (IoT) .NET][lnk-dotnet-ref]
* [Пакет SDK для устройств Azure IoT для Java][lnk-java-ref]
* [Пакет SDK для служб Azure IoT для Java][lnk-java-service-ref]
* [Пакет SDK для устройств Azure IoT для Node.js][lnk-node-ref]
* [Пакет SDK для служб Azure IoT для Node.js][lnk-node-service-ref]
* [Пакет SDK для устройств Azure IoT для C][lnk-c-ref]
* [REST для Центра Интернета вещей][lnk-rest-ref]
* [Edge Интернета вещей Azure][lnk-gateway-ref]

## <a name="next-steps"></a>Дополнительная информация

Другие справочные статьи в руководстве разработчика для Центра Интернета вещей:

* [IoT Hub endpoints][lnk-devguide-endpoints] (Конечные точки Центра Интернета вещей)
* [Справочник по языку запросов Центра Интернета вещей для двойников устройств и заданий][lnk-devguide-query]
* [Quotas and throttling][lnk-devguide-quotas] (Квоты и регулирование)
* [Поддержка MQTT в Центре Интернета вещей][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
