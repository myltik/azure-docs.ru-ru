---
title: "Начало работы с Центром Интернета вещей Azure | Документация Майкрософт"
description: "Сведения по началу работы со службой Центра Интернета вещей"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d7360c3d41a0e4cf0d182e510d6bc2fccd915dd1
ms.openlocfilehash: 51ac0c0d3a91070fc8f5b3892409af838e91068e
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-azure-iot-hub-or-azure-iot-gateway-sdk"></a>Начало работы с Центром Интернета вещей Azure или пакетом SDK для шлюза Центра Интернета вещей Azure

Вы можете выбрать одно из нескольких руководств, чтобы приступить к работе со службой Центра Интернета вещей или пакетом SDK для шлюза.

## <a name="iot-hub"></a>Центр IoT

Центр IoT Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств Интернета вещей (IoT) и серверной частью решения.

Чтобы начать работу со службой Центра Интернета вещей, можно:

- Выполнить действия в руководстве, где используется виртуальное устройство, выполняемое на компьютере разработки. Выберите руководство по началу работы в соответствии с требуемым языком программирования: [.NET][lnk-dotnet], [Java][lnk-java] или [Node.js][lnk-nodejs].

- Выполнить действия в руководстве, где используется физическое устройство. Выберите руководство по началу работы в соответствии с требуемой аппаратной платформой: [Raspberry Pi][lnk-rasp-pi], [Intel Edison][lnk-edison] или [Arduino][lnk-arduino]. Эти руководства содержат сведения о получении аппаратных устройств.

- Ознакомиться со сведениями об использовании языка C для разработки устройств Центра Интернета вещей в статье [Пакет SDK для устройств Azure IoT для C][lnk-c-intro].

## <a name="gateway-sdk"></a>Пакет SDK для шлюза

Вы можете использовать пакет SDK для шлюза, чтобы создать настраиваемый полевой шлюз. Шлюз может выполнять аналитику, принимать срочные решения, позволяющие уменьшить задержку, предоставлять службы управления устройствами, применять ограничения для безопасности и конфиденциальности, а также преобразовывать протоколы.

Чтобы начать работу с пакетом SDK для шлюза, можно:

- Выполнить действия в руководстве, где используется виртуальный шлюз, выполняемый на компьютере разработки. Вы можете выбрать руководство по началу работы для [Linux][lnk-linux] или [Windows][lnk-windows].

- Выполнить действия в руководстве, где используется физическое устройство. Вы можете выбрать руководство по началу работы, где используется [виртуальное устройство с Intel NUC (Next Unit of Computing)][lnk-gateway-sim] или [устройство SensorTag с Intel NUC][lnk-gateway-tag].

## <a name="next-steps"></a>Дальнейшие действия

По окончании работы с руководствами по началу работы можно узнать о дополнительных возможностях Центра Интернета вещей и пакета SDK для шлюза в [руководствах разработчика][lnk-devguide] и [практических руководствах][lnk-howto].

[lnk-dotnet]: ./iot-hub-csharp-csharp-getstarted.md
[lnk-java]: ./iot-hub-java-java-getstarted.md
[lnk-nodejs]: ./iot-hub-node-node-getstarted.md
[lnk-c-intro]: ./iot-hub-device-sdk-c-intro.md
[lnk-rasp-pi]: ./iot-hub-raspberry-pi-kit-node-get-started.md
[lnk-edison]: ./iot-hub-intel-edison-kit-node-get-started.md
[lnk-arduino]: ./iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[lnk-linux]: ./iot-hub-linux-gateway-sdk-get-started.md
[lnk-windows]: ./iot-hub-windows-gateway-sdk-get-started.md
[lnk-gateway-sim]: ./iot-hub-gateway-kit-c-sim-get-started.md
[lnk-gateway-tag]: ./iot-hub-gateway-kit-c-get-started.md
[lnk-devguide]: ./iot-hub-devguide.md
[lnk-howto]: ./iot-hub-how-to.md

