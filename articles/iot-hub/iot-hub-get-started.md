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
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 64b09448149b7e1efd8b76663a27b6c7ec1eb3c7
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Руководства по началу работы с Центром Интернета вещей Azure

С помощью Центра Интернета вещей и пакетов SDK для устройств Azure IoT можно создавать решения для Интернета вещей (IoT).

* Центр Интернета вещей — это полностью управляемая облачная служба, которая безопасно подключается к устройствам IoT, отслеживает их и управляет ими. С помощью пакетов SDK для устройств Azure IoT можно реализовывать устройства IoT.
* С помощью шлюза IoT можно реализовывать более сложные сценарии IoT, в которых необходимо учитывать такие факторы, как устаревшие устройства, затраты на пропускную способность, политики безопасности и конфиденциальности или обработку граничных данных. В этих сценариях с помощью Edge Интернета вещей Azure вы можете реализовать шлюз, который обеспечивает подключение устройств к Центру Интернета вещей.

## <a name="what-the-tutorials-cover"></a>Что описывается в руководствах

В этих руководствах представлены общие сведения о Центре Интернета вещей Azure и пакетах SDK для устройств. Также описаны распространенные сценарии с использованием IoT, в которых демонстрируются возможности Центра Интернета вещей. В руководствах также объясняется, как объединить Центр Интернета вещей с другими службами и средствами Azure для создания более мощных решений IoT. Для работы с этими руководствами вы можете использовать виртуальные или физические устройства IoT. Кроме того, вы узнаете, как использовать шлюз, чтобы подключить устройство к Центр Интернета вещей.

## <a name="device-setup-scenario-connect-iot-device-or-gateway-to-azure-iot-hub"></a>Сценарий с настройкой устройства: подключение устройства IoT или шлюза к Центру Интернета вещей Azure

Чтобы приступить к работе, выберите физическое или виртуальное устройство.

| Устройство IoT                       | Язык программирования |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| Виртуальное устройство                | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth]              |

Кроме того, можно использовать шлюз Edge Интернета вещей, чтобы устройство могло подключаться к Центру Интернета вещей.

| Устройство шлюза               | Язык программирования | Платформа         |
|------------------------------|----------------------|------------------|
| Intel NUC (модель DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| Виртуальное устройство            | C                    | [Linux][Sim_Lnx], [Windows][Sim_Win] |

## <a name="extended-iot-scenarios-use-other-azure-services-and-tools"></a>Расширенные сценарии IoT: использование других служб и средств Azure

Подключив устройство к Центру Интернета вещей, вы можете реализовать дополнительные сценарии с использованием других средств и служб Azure:

| Сценарий                                    | Служба или средство Azure              |
|---------------------------------------------|------------------------------------|
| [Управление сообщениями Центра Интернета вещей][Mg_IoT_Hub_Msg]                    | Средство iothub-explorer               |
| [Управление устройством IoT][Mg_IoT_Dv]               | Средство iothub-explorer               |
| [Сохранение сообщений Центра Интернета вещей в службе хранилища Azure][Sv_IoT_Msg_Stor]                      | Хранилище таблиц Azure               |
| [Визуализации данных датчика][Vis_Data]             | Microsoft Power BI, веб-приложения Azure |
| [Прогноз погоды на основе данных датчика][Weather_Forecast] | Машинное обучение Azure             |
| [Автоматическое обнаружение аномалий и реагирование на них][Anomaly_Detect]    | Azure Logic Apps                   |

## <a name="next-steps"></a>Дальнейшие действия

Завершив работу с этими руководствами, вы можете продолжить изучать возможности Центра Интернета вещей. Дополнительные сведения см. в [руководстве для разработчика][lnk-dev-guide]. Другие руководства см. в [этом практическом руководстве][lnk-how-to].


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
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
[Mg_IoT_Hub_Msg]: iot-hub-explorer-cloud-device-messaging.md
[Mg_IoT_Dv]: iot-hub-device-management-iothub-explorer.md
[Sv_IoT_Msg_Stor]: iot-hub-store-data-in-azure-table-storage.md
[Vis_Data]: iot-hub-live-data-visualization-in-power-bi.md
[Weather_Forecast]: iot-hub-weather-forecast-machine-learning.md
[Anomaly_Detect]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-dev-guide]: iot-hub-devguide.md
[lnk-how-to]: iot-hub-how-to.md
