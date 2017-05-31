---
title: "Подключение устройства Raspberry Pi к Azure IoT Suite | Документация Майкрософт"
description: "Руководства по использованию начального набора Microsoft Azure IoT с устройствами Raspberry Pi 3 и решения для удаленного мониторинга Azure IoT Suite с помощью Node.js или C. Вы можете выбрать руководство, в котором моделируются данные телеметрии, используются датчики или включается возможность удаленного обновления встроенного ПО."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 5efb78c2c514bd0b21be842ddee5465fe4e4dcf9
ms.contentlocale: ru-ru
ms.lasthandoff: 05/04/2017


---
# <a name="connect-your-microsoft-azure-iot-raspberry-pi-3-starter-kit-to-the-remote-monitoring-solution"></a>Подключение начального набора Microsoft Azure IoT для устройств Raspberry Pi 3 к решению для удаленного мониторинга

Из этого руководства вы узнаете, как подключить устройство Raspberry Pi 3 к решению для удаленного мониторинга. Выберите руководство на основе предпочитаемого вами языка программирования и имеющегося у вас датчика, который можно использовать с устройством Raspberry Pi.

## <a name="the-tutorials"></a>Руководства

| Учебник | Примечания | Языки |
| -------- | ----- | --------- |
| Смоделированные данные телеметрии (базовый уровень)| Моделирование данных датчика. Использование автономного устройства Raspberry Pi. | [C][lnk-c-simulator], [Node.js][lnk-node-simulator] |
| Физический датчик (средний уровень) | Использование данных, полученных с датчика BME280, подключенного к устройству Raspberry Pi. | [C][lnk-c-basic], [Node.js][lnk-node-basic] |
| Реализация обновления встроенного ПО (продвинутый уровень)| Использование данных, полученных с датчика BME280, подключенного к устройству Raspberry Pi. Включение функции удаленного обновления встроенного ПО на устройстве Raspberry Pi. | [C][lnk-c-advanced], [Node.js][lnk-node-advanced] |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры и документацию по Azure IoT можно найти в [Центре разработчиков Azure IoT](https://azure.microsoft.com/develop/iot/).

[lnk-node-simulator]: iot-suite-raspberry-pi-kit-node-get-started-simulator.md
[lnk-node-basic]: iot-suite-raspberry-pi-kit-node-get-started-basic.md
[lnk-node-advanced]: iot-suite-raspberry-pi-kit-node-get-started-advanced.md
[lnk-c-simulator]: iot-suite-raspberry-pi-kit-c-get-started-simulator.md
[lnk-c-basic]: iot-suite-raspberry-pi-kit-c-get-started-basic.md
[lnk-c-advanced]: iot-suite-raspberry-pi-kit-c-get-started-advanced.md
