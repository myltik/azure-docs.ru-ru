---
title: "Подключение Arduino (C) к Интернету вещей Azure. Приступая к работе | Документация Майкрософт"
description: "Приступите к работе с Adafruit Feather M0 Wi-Fi, создайте Центр Интернета вещей Azure и подключите Adafruit Feather M0 Wi-Fi к Центру Интернета вещей"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Центр Интернета вещей Azure, начало работы с Интернетом вещей, руководство по Интернету вещей, Интернет вещей для Adafruit, начало работы с Arduino"
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 6789e6f1c094f8809163e29349c8ea54e1e97683
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-your-arduino-board-adafruit-feather-m0-wifi"></a>Приступая к работе с платой Arduino: Adafruit Feather M0 Wi-Fi

В этом руководстве вы начнете с того, что узнаете основы работы с платой Arduino. Также вы узнаете, как можно легко подключать устройства к облаку с помощью [Центра Интернета вещей Azure](iot-hub-what-is-iot-hub.md).

## <a name="lesson-1-configure-your-device"></a>Урок 1. Настройка устройства
![Урок 1. Сквозная схема][Lesson-1-end-to-end-diagram]

В ходе этого урока, вы настроите операционную систему для платы Arduino и среду разработки, а также развернете приложение на плате Arduino.

### <a name="configure-your-device"></a>Настройка устройства
Настройте плату Arduino для первого использования, собрав и подключив ее.

*Предполагаемое время выполнения: 5 минут*

Ознакомьтесь со статьей [Настройка устройства ][configure-your-device].

### <a name="get-the-tools"></a>Получить инструменты
Скачайте инструменты и программное обеспечение для сборки и развертывания первого приложения для платы Arduino.

*Предполагаемое время выполнения: 20 минут*

Ознакомьтесь со статьей [Get the tools][get-the-tools] (Получение инструментов).

### <a name="create-and-deploy-the-blink-application"></a>Создание и развертывание приложения для включения индикатора
Клонируйте пример приложения Arduino для включения индикатора c GitHub и разверните его с помощью инструмента Gulp на плате Arduino. В этом примере приложения светодиодный индикатор GPIO #13 будет мигать каждые две секунды.

*Предполагаемое время выполнения: 5 минут*

Ознакомьтесь со статьей [Создание и развертывание приложения для включения индикатора][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Урок 2. Создание Центра Интернета вещей
![Урок 2. Сквозная схема][lesson-2-end-to-end-diagram]

В ходе этого урока вы создадите бесплатную учетную запись Azure, подготовите Центр Интернета вещей Azure и создадите в нем устройство.

Прежде чем продолжать, завершите урок 1.

### <a name="get-the-azure-tools"></a>Получение средств Azure
Установите интерфейс командной строки Azure (Azure CLI).

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Get Azure tools][get-azure-tools] (Получение инструментов Azure).

### <a name="create-your-iot-hub-and-register-your-arduino-board"></a>Создание Центра Интернета вещей и регистрация платы Arduino
Создайте группу ресурсов, подготовьте Центр Интернета вещей Azure и добавьте в него первое устройство, используя Azure CLI.

*Предполагаемое время выполнения: 10 минут*

Перейдите к статье [Создание Центра Интернета вещей и регистрация платы Arduino][create-your-iot-hub-and-register-your-arduino-board].

## <a name="lesson-3-send-device-to-cloud-messages"></a>Урок 3. Отправка сообщений с устройства в облако
![Урок 3. Сквозная схема][lesson-3-end-to-end-diagram]

В ходе этого урока вы отправите сообщения с платы Arduino в Центр Интернета вещей. Также вы создадите приложение-функцию Azure, которое получает входящие сообщения из Центра Интернета вещей и записывает их в хранилище таблиц Azure.

Прежде чем продолжить, завершите уроки 1 и 2.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Создание учетной записи хранения Azure и приложения-функции Azure
С помощью шаблона Azure Resource Manager создайте приложение-функцию Azure и учетную запись хранения Azure.

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Создание приложения-функции Azure и учетной записи хранения Azure][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Запуск примера приложения для отправки сообщений с устройства в облако
Разверните и запустите пример приложения для платы Arduino, которое отправляет сообщения в Центр Интернета вещей.

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Run a sample application to send device-to-cloud messages][send-device-to-cloud-messages] (Запуск примера приложения для отправки сообщений с устройства в облако).

### <a name="read-messages-persisted-in-azure-storage"></a>Чтение сообщений, сохраненных в службе хранилища Azure
Отслеживайте сообщения, передаваемые с устройства в облако, по мере их записывания в хранилище Azure.

*Предполагаемое время выполнения: 5 минут*

Ознакомьтесь со статьей [Чтение сообщений, сохраненных в службе хранилища Azure][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Урок 4. Отправка сообщений из облака на устройство
![Урок 4. Сквозная схема][lesson-4-end-to-end-diagram]

В ходе этого урока демонстрируется способ отправки сообщений из Центра Интернета вещей на плату Arduino. C помощью этих сообщений вы будете управлять включением и выключением светодиодного индикатора GPIO #13 на плате. Для выполнения этой задачи подготовлен пример приложения.

Прежде чем продолжить, завершите уроки 1, 2 и 3.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Запуск примера приложения для получения сообщений из облака на устройство
Пример приложения из урока 4 выполняется на плате Arduino и отслеживает входящие сообщения от Центра Интернета вещей. Новая задача Gulp отправляет сообщения из Центра Интернета вещей на вашу плату Arduino для управления состоянием светодиодного индикатора.

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Запуск примера приложения для получения сообщений из облака на устройство][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Дополнительный раздел. Изменение режима включения и отключения светодиодного индикатора
Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора.

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Optional section: Change the on and off behavior of the LED][change-the-on-and-off-behavior-of-the-led] (Дополнительный раздел. Изменение режима включения и отключения светодиодного индикатора).

## <a name="troubleshooting"></a>Устранение неполадок
Если в ходе какого-либо урока у вас возникнут проблемы, то решение можно найти в статье [Troubleshooting][troubleshooting] (Устранение неполадок).

<!-- Images and links -->

[Lesson-1-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson1.png
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[lesson-2-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson2.png
[get-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[lesson-3-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson3.png
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
[lesson-4-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson4.png
[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
