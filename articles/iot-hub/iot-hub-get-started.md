---
title: Центр Интернета вещей Azure — подключение устройств Интернета вещей к облаку | Документация Майкрософт
description: Сведения о подключении плат и начальных наборов для Интернета вещей к Центру Интернета вещей Azure. Устройства могут отправлять данные телеметрии в Центр Интернета вещей, который, в свою очередь, может отслеживать эти устройства и управлять ими.
author: dominicbetts
manager: timlt
keywords: Руководство по работе с Центром Интернета вещей Azure
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 76f427204f0ad31196ce8b995b9e4ed9676209ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634708"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Руководства по началу работы с Центром Интернета вещей и реальными устройствами

С помощью Центра Интернета вещей и пакетов SDK для устройств Azure IoT можно создавать решения для Интернета вещей.

* Центр Интернета вещей — это полностью управляемая облачная служба, которая безопасно подключается к устройствам Интернета вещей, отслеживает их и управляет ими. С помощью пакетов SDK для устройств Azure IoT можно реализовывать устройства IoT.
* С помощью шлюза Интернета вещей можно реализовать более сложные сценарии, связанные с Интернетом вещей. Например, такие, в которых необходимо учитывать следующие факторы: устаревшие устройства, затраты на пропускную способность, политики безопасности и конфиденциальности или обработка граничных данных. В этих сценариях с помощью [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) можно реализовать шлюз, который обеспечивает подключение устройств к Центру Интернета вещей.

## <a name="what-the-how-to-articles-cover"></a>Что входит в статьи с инструкциями

В этих статьях представлены общие сведения о Центре Интернета вещей Azure и пакетах SDK для устройств. Также описаны распространенные сценарии с использованием Интернета вещей, в которых демонстрируются возможности Центра Интернета вещей. В статьях также объясняется, как объединить Центр Интернета вещей с другими службами и средствами Azure для создания более мощных решений Интернета вещей. В рамках статей используются реальные устройства Интернета вещей

## <a name="set-up-your-device"></a>Настройка устройства

Подключите устройство или шлюз Интернета вещей к Центру Интернета вещей:

| Устройство IoT                       | Язык программирования |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino в VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Онлайн-симулятор устройства         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
