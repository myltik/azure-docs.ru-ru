---
title: "Решения Azure для Интернета вещей (IoT Suite) | Документация Майкрософт"
description: "Общие сведения об Интернете вещей в Azure, включая пример архитектуры решения, а также о его связи с набором Azure IoT Suite и предварительно настроенных решениях."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 1c8703d64ff45e589a7ce93f1f2176681e1bf331
ms.contentlocale: ru-ru
ms.lasthandoff: 04/25/2017

---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Набор Azure IoT Suite
Набор Microsoft Azure IoT — это решение корпоративного уровня, которое позволяет быстро приступить к работе с помощью набора расширяемых предварительно настроенных решений. Эти решения подходят для таких распространенных сценариев Интернета вещей, как [Удаленный мониторинг][lnk-preconfigured-solutions], [Прогнозное обслуживание][lnk-predictive-maintenance] и [Подключенная фабрика][lnk-connected-factory]. Эти решения представляют собой реализацию архитектуры решений IoT, описанной в этой статье.

Предварительно настроенные решения являются полными, работающими, комплексными решениями, которые включают в себя следующее:

- виртуальные устройства, необходимые для начала работы;
- предварительно настроенные службы Azure, такие как [Центр Интернета вещей Azure][Azure IoT Hub], [концентраторы событий Azure][Azure Event Hubs], [Azure Stream Analytics][Azure Stream Analytics], [машинное обучение Azure][Azure Machine Learning] и [служба хранилища Azure][Azure storage];
- консоли управления определенного решения.

Предварительно настроенные решения содержат проверенный, готовый к работе код, который можно настраивать и расширять для реализации определенных сценариев IoT.

Возможно, вас также интересует служба [Центра Интернета вещей Azure][Azure IoT Hub], которую используют многие из предварительно настроенных решений. [Центр Интернета вещей Azure][Azure IoT Hub] обеспечивает безопасный и надежный двунаправленный обмен данными между устройствами и облаком, которые используются в архитектуре предварительно настроенных решений.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с этими статьями, чтобы узнать больше об IoT Suite и предварительно настроенных решениях.

* [Что такое Azure IoT Suite?][lnk-whatissuite]
* [Что такое предварительно настроенные решения Azure IoT Suite?][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md
