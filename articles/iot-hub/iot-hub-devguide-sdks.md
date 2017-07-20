---
title: "Общие сведения о пакетах SDK для Azure IoT | Документация Майкрософт"
description: "Руководство разработчика. Сведения и ссылки на различные пакеты SDK для устройств и служб Azure IoT, которые можно использовать для создания приложений для устройств и внутренних приложений."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: bcbf4b9633f58293edb19aeb33dec6602ac4ec8f
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017


---
# <a name="understand-and-use-azure-iot-sdks"></a>Общие сведения о пакетах SDK для Azure IoT и их использование

Существуют три категории пакетов SDK для работы с Центром Интернета вещей.

* **Пакеты SDK для устройств** позволяют создавать приложения, работающие на ваших устройствах Интернета вещей. Эти приложения отправляют данные телеметрии в центр Интернета вещей и при необходимости получают из него сообщения.

* **Пакеты SDK для служб** позволяют управлять Центром Интернета вещей и при необходимости отправлять сообщения на устройства Интернета вещей.

* **Edge Интернета вещей Azure** позволяет создавать шлюзы, чтобы подключать устройства, не использующие ни один из поддерживаемых протоколов, или чтобы обрабатывать сообщения на границе, если это необходимо.

Пакеты SDK предоставляются для поддержки нескольких языков программирования.

## <a name="azure-iot-device-sdks"></a>Пакеты SDK для устройств Azure IoT

Пакеты SDK для устройств центра Microsoft Azure IoT содержат код, упрощающий построение устройств и приложений, которые подключаются к службам центра IoT и управляются с помощью этих служб.

В GitHub для скачивания доступны следующие пакеты SDK для устройств Azure IoT:

* [Пакет SDK для устройств Azure IoT для C][lnk-c-device-sdk], написанный в соответствии со стандартом ANSI C (C99) для обеспечения переносимости и совместимости с широким диапазоном платформ. Существуют две клиентские библиотеки устройств для C: низкоуровневая  **iothub_client**  и  **сериализатор**.
* [Пакет SDK для устройств Azure IoT для .NET][lnk-dotnet-device-sdk]
* [Пакет SDK для устройств Azure IoT для Java][lnk-java-device-sdk]
* [Пакет SDK для устройств Azure IoT для Node.js][lnk-node-device-sdk]
* [Пакет SDK для устройств Azure IoT для Python][lnk-python-device-sdk]

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
* [Пакет SDK для служб Azure IoT для Node.js][lnk-node-service-sdk]
* [Пакет SDK для служб Azure IoT для Java][lnk-java-service-sdk]
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
* [REST для Центра Интернета вещей][lnk-rest-ref]
* [Пакет SDK для устройств Azure IoT для C][lnk-c-ref]
* [Пакет SDK для устройств Azure IoT для Java][lnk-java-ref]
* [Пакет SDK для служб Azure IoT для Java][lnk-java-service-ref]
* [Пакет SDK для устройств Azure IoT для Node.js][lnk-node-ref]
* [Пакет SDK для служб Azure IoT для Node.js][lnk-node-service-ref]
* [Edge Интернета вещей Azure][lnk-gateway-ref]

## <a name="next-steps"></a>Дальнейшие действия

Другие справочные статьи в руководстве для разработчиков Центра Интернета вещей:

* [IoT Hub endpoints][lnk-devguide-endpoints] (Конечные точки Центра Интернета вещей)
* [Справочник по языку запросов Центра Интернета вещей для двойников устройств и заданий][lnk-devguide-query]
* [Quotas and throttling][lnk-devguide-quotas] (Квоты и регулирование)
* [Поддержка MQTT в центре IoT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
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
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

