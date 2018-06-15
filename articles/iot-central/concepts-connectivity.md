---
title: Подключение устройства в Azure IoT Central | Документация Майкрософт
description: В этой статье описаны ключевые понятия, связанные с подключением устройства в Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dc9fe144c2258f33ce59c61ce63c15835cc3fa53
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628340"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Подключение устройства в Azure IoT Central

В этой статье описаны ключевые понятия, связанные с подключением устройства в Microsoft Azure IoT Central.

Каждое устройство, подключаемое к приложению Azure IoT Central, подключается к [конечным точкам](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints), предоставленным Центром Интернета вещей, используемым этим приложением. Центр Интернета вещей обеспечивает масштабируемые, безопасные и надежные подключения из подключенных устройств.

## <a name="sdk-support"></a>Поддержка пакетов SDK

Пакеты SDK для устройств Azure предлагают самый простой способ реализации кода на устройствах, подключаемых к приложению Azure IoT Central. Доступны следующие пакеты SDK для устройств:

- [Пакет SDK для Azure IoT для C](https://github.com/azure/azure-iot-sdk-c).
- [Пакет SDK для Azure IoT для Python](https://github.com/azure/azure-iot-sdk-python).
- [Пакет SDK для Azure IoT для Node.js](https://github.com/azure/azure-iot-sdk-node).
- [Пакет SDK для Azure IoT для Java](https://github.com/azure/azure-iot-sdk-java).
- [Пакет SDK для Azure IoT для .NET](https://github.com/azure/azure-iot-sdk-csharp).

Каждое устройство подключается с помощью уникальной строки подключения, идентифицирующей устройство. Устройство может подключаться только к тому Центру Интернета вещей, в котором оно зарегистрировано. При создании реального устройства в приложении Azure IoT Central приложение создает строку подключения для использования.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Функции пакета SDK и возможность подключения Центра Интернета вещей

Для всего обмена данными устройства с Центром Интернета вещей используются следующие варианты подключения Центра Интернета вещей:

- [передача сообщений с устройства в облако](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c);
- [Двойники устройств](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

В следующей таблице содержатся сведения о том, каким образом возможности устройства Azure IoT Central сопоставляются с возможностями Центра Интернета вещей.

| Azure IoT Central | Центр Интернета вещей Azure |
| ----------- | ------- |
| Измерения. Телеметрия | Передача сообщений с устройства в облако |
| Свойства устройства | Сообщаемые свойства двойника устройства |
| Параметры | Требуемые и передаваемые свойства двойника устройства |

Чтобы узнать больше об использовании пакетов SDK для устройств, см. пример кода в одной из следующих статей:

- [Подключение универсального клиентского приложения к приложению Azure IoT Central (Node.js)](howto-connect-nodejs.md)
- [Подключение Raspberry Pi к приложению Azure IoT Central (Python)](howto-connect-raspberry-pi-python.md)
- [Подключение устройства MXChip IoT DevKit к приложению Azure IoT Central](howto-connect-devkit.md)

В следующем видео представлен обзор подключения реального устройства к приложению Azure IoT Central.

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>Протоколы

Пакеты SDK для устройства поддерживают следующие сетевые протоколы для подключения к Центру Интернета вещей.

- MQTT
- AMQP
- HTTPS

Дополнительные сведения об этих разных протоколах и рекомендации по их выбору см. в статье [Справочник — выбор протокола связи](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Если ваше устройство не может использовать любой из поддерживаемых протоколов, вы можете использовать Azure IoT Edge для преобразования протокола. IoT Edge поддерживает другие сценарии интеллектуальной обработки на пограничном устройстве, позволяющие снизить нагрузку на пограничное устройство из приложения Azure IoT Central.

## <a name="security"></a>Безопасность

Все данные, передаваемые между устройствами и Azure IoT Central, зашифрованы. Центр Интернета вещей выполняет проверку подлинности каждого запроса с устройства, которое подключается к любой конечной точке Центра Интернета вещей, обращенной к устройству. Чтобы избежать обмена учетными данными по сети, для проверки подлинности устройство использует подписанные маркеры. Дополнительные сведения см. в статье [Управление доступом к Центру Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Сейчас устройства, подключающиеся к Azure IoT Central, должны использовать маркеры SAS. Использование сертификатов X.509 не поддерживается.

## <a name="next-steps"></a>Дополнительная информация

После изучения возможности подключения устройств в Azure IoT Central вы можете перейти к следующим шагам:

- [Подготовка и подключение устройства DevKit](howto-connect-devkit.md)
- [Подготовка и подключение Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Подключение универсального клиентского приложения к приложению Azure IoT Central (Node.js)](howto-connect-nodejs.md)
