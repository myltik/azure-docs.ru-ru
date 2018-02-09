---
title: "Центр Интернета вещей Azure — подключение устройств Интернета вещей к облаку | Документация Майкрософт"
description: "Сведения о подключении плат и начальных наборов для Интернета вещей к Центру Интернета вещей Azure. Устройства могут отправлять данные телеметрии в Центр Интернета вещей, который, в свою очередь, может отслеживать эти устройства и управлять ими."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: "Руководство по работе с Центром Интернета вещей Azure"
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 34742208e9189eb31310b58770ee4a22e33f56d5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-iot-hub-get-started-tutorials"></a>Руководства по началу работы с Центром Интернета вещей Azure

С помощью Центра Интернета вещей и пакетов SDK для устройств Azure IoT можно создавать решения для Интернета вещей.

* Центр Интернета вещей — это полностью управляемая облачная служба, которая безопасно подключается к устройствам Интернета вещей, отслеживает их и управляет ими. С помощью пакетов SDK для устройств Azure IoT можно реализовывать устройства IoT.
* С помощью шлюза Интернета вещей можно реализовать более сложные сценарии, связанные с Интернетом вещей. Например, такие, в которых необходимо учитывать следующие факторы: устаревшие устройства, затраты на пропускную способность, политики безопасности и конфиденциальности или обработка граничных данных. В этих сценариях с помощью [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) можно реализовать шлюз, который обеспечивает подключение устройств к Центру Интернета вещей.

## <a name="what-the-tutorials-cover"></a>Что описывается в руководствах

В этих руководствах представлены общие сведения о Центре Интернета вещей Azure и пакетах SDK для устройств. Также описаны распространенные сценарии с использованием Интернета вещей, в которых демонстрируются возможности Центра Интернета вещей. В руководствах также объясняется, как объединить Центр Интернета вещей с другими службами и средствами Azure для создания более мощных решений Интернета вещей. Для работы с этими руководствами вы можете использовать виртуальные или физические устройства Интернета вещей. Кроме того, вы узнаете, как использовать шлюз, чтобы подключить устройство к Центр Интернета вещей.

## <a name="set-up-your-device"></a>Настройка устройства

Подключите устройство или шлюз Интернета вещей к Центру Интернета вещей. Чтобы приступить к работе, выберите физическое или виртуальное устройство.

| Устройство IoT                       | Язык программирования |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino в VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Имитация устройства на компьютере           | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth] |
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
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
