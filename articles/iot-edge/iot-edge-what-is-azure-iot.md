---
title: "Решения Azure для Интернета вещей (IoT Edge) | Документация Майкрософт"
description: "Общие сведения об архитектуре примера решения Интернета вещей и о том, как оно связано с устройствами, службой Центра Интернета вещей Azure, пакетами SDK для устройств Azure IoT, пакетами SDK для служб Azure IoT и другими службами Azure."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 587b733106d511ec63d71f67a06e520324a3e594
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Дальнейшие действия

Azure IoT Edge — это служба Azure, которая позволяет выполнять аналитику и обработку данных на пограничных устройствах. С помощью IoT Edge вы можете расширить возможности устройств, используя контейнерный код, включающий логику, полученную непосредственно из используемых служб Azure, или собственный код для конкретного решения. Это позволяет устройствам:

* выполнять роль шлюза для агрегирования и обработки данных, поступающих с нескольких конечных устройств;
* обнаруживать аномалии и реагировать на изменения в среде, не дожидаясь инструкций из облака;
* свести к минимуму затраты на пропускную способность и хранение за счет предварительной обработки данных и отправки результатов. 

IoT Edge также включает облачный интерфейс, что позволяет удаленно управлять устройствами. Вы можете развертывать код, отслеживать состояние и обновлять его без физического доступа к устройствам. Вы можете удаленно управлять отдельными устройствами или создать развертывания, в которых задействованы большие наборы определенных устройств. Дополнительные сведения о см. в статье [Understand IoT Edge deployments for single devices or at scale - preview][lnk-deployment] (Основные сведения о развертываниях IoT Edge для отдельных устройств или в требуемом масштабе (предварительная версия)).

Дополнительные сведения о компонентах, которые обеспечивают работу IoT Edge, см. в статье о [принципах работы Azure IoT Edge][lnk-overview].

Если вы ранее не использовали Центр Интернета вещей Azure, начните со статьи [Общие сведения о службе Центра Интернета вещей Azure][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
