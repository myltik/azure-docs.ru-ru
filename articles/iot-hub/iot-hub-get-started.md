---
title: "Центр Интернета вещей Azure — подключение устройств Интернета вещей к облаку | Документация Майкрософт"
description: "Сведения о подключении устройств Интернета вещей к Центру Интернета вещей в Azure. Устройства могут отправлять данные телеметрии в Центр Интернета вещей, который, в свою очередь, может отслеживать эти устройства и управлять ими."
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
ms.date: 04/28/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: d3d74d44bdd7ce6b49ec58b70c7ddb8081e11f96
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Руководства по началу работы с Центром Интернета вещей Azure

С помощью Центра Интернета вещей и пакетов SDK для устройств Azure IoT можно создавать решения для Интернета вещей (IoT).

* Центр Интернета вещей — это полностью управляемая облачная служба, которая безопасно подключается к устройствам IoT, отслеживает их и управляет ими. С помощью пакетов SDK для устройств Azure IoT можно реализовывать устройства IoT.
* С помощью шлюза IoT можно реализовывать более сложные сценарии IoT, в которых необходимо учитывать такие факторы, как устаревшие устройства, затраты на пропускную способность, политики безопасности и конфиденциальности или обработку граничных данных. В этих сценариях с помощью Edge Интернета вещей Azure вы можете реализовать шлюз, который обеспечивает подключение устройств к Центру Интернета вещей.

## <a name="what-the-tutorials-cover"></a>Что описывается в руководствах

В этих руководствах представлены общие сведения о Центре Интернета вещей Azure и пакетах SDK для устройств. Также описаны распространенные сценарии с использованием IoT, в которых демонстрируются возможности Центра Интернета вещей. В руководствах также объясняется, как объединить Центр Интернета вещей с другими службами и средствами Azure для создания более мощных решений IoT. Для работы с этими руководствами вы можете использовать виртуальные или физические устройства IoT. Кроме того, вы узнаете, как использовать шлюз, чтобы подключить устройство к Центр Интернета вещей.

## <a name="setup-your-device-connect-iot-device-or-gateway-to-azure-iot-hub"></a>Настройка устройства. Подключение устройства или шлюза Интернета вещей к Центру Интернета вещей Azure

Чтобы приступить к работе, выберите физическое или виртуальное устройство.

| Устройство IoT                       | Язык программирования |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C], [Python][Pi_Py]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| Имитация устройства на компьютере          | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth]              |
| Онлайн-симулятор устройства         | [Raspberry Pi (Node.js)][Ol_Sim] |

Кроме того, можно использовать шлюз Edge Интернета вещей, чтобы устройство могло подключаться к Центру Интернета вещей.

| Устройство шлюза               | Язык программирования | Платформа         |
|------------------------------|----------------------|------------------|
| Intel NUC (модель DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| Виртуальное устройство            | C                    | [Linux][Sim_Lnx], [Windows][Sim_Win] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-node-get-started.md
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

