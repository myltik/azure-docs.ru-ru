---
title: "Подключение Arduino (C) к Интернету вещей Azure. Урок 1. Настройка устройства | Документация Майкрософт"
description: "Настройте Adafruit Feather M0 WiFi для первого использования."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino, настройка, подключение Arduino к компьютеру, настройка Arduino, плата Arduino"
ms.assetid: f5b334f0-a148-41aa-b374-ce7b9f5b305a
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3789c07cd6142ba5b6a58a67813c0ead6b87bd76


---
# <a name="configure-your-device"></a>Настройка устройства
## <a name="what-you-will-do"></a>Выполняемая задача
Настройте плату Adafruit Feather M0 WiFi Arduino для первого использования, собрав и подключив ее. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-need"></a>Необходимые элементы
Для этого вам понадобятся следующие компоненты из начального набора Adafruit Feather M0 WiFi:

* плата Adafruit Feather M0 WiFi;
* кабель micro-USB (тип B-A).

![набор][kit]

Кроме того, вам понадобится следующее:

* Компьютер под управлением Windows, Mac или Linux.
* Беспроводное подключение, чтобы подключиться к плате Arduino.
* Подключение к Интернету для скачивания инструмента настройки.

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:

* Как собрать плату Arduino и подключить ее для использования в следующем уроке.
* Как добавить разрешения для последовательного порта в Ubuntu.

## <a name="connect-your-arduino-board-to-your-computer"></a>Подключение платы Arduino к компьютеру

1. Подключите кабель micro-USB к верхнему порту micro-USB.

   ![Верхний порт micro-USB][top-micro-usb-port]

2. Другой конец кабеля USB подключите к компьютеру.

   ![Подключение USB к компьютеру][computer-usb]

## <a name="add-serial-port-permissions-on-ubuntu"></a>Добавление разрешений для последовательного порта в Ubuntu

Этот раздел можно пропустить, если вы используете Windows или macOS. Для Ubuntu выполните следующие действия, чтобы убедиться, что у обычного пользователя Linux есть разрешения для работы с USB-портом платы Arduino.

1. Для обычного пользователя из терминала

   ```bash
   ls -l /dev/ttyUSB*
   # Or
   ls -l /dev/ttyACM*
   ```

   отобразится примерно такой текст:

   ```bash
   crw-rw---- 1 root uucp 188, 0 5 apr 23.01 ttyUSB0
   # Or
   crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyACM0
   ```

   Вместо&0; может быть указано другое число или вы можете получить несколько записей. В первом случае нам требуются данные `uucp`, а во втором — `dialout`. Это владелец группы файла.

2. Добавьте пользователя в группу:

   ```bash
   sudo usermod -a -G group-name username
   ```

   Где `group-name` — это данные, вычисленные в первом шаге, а `username` — это имя пользователя Linux.

3. Необходимо выйти из системы и снова войти в нее, чтобы изменения вступили в силу и настройка была завершена.

## <a name="summary"></a>Сводка
Из этой статьи вы узнали, как настроить плату Arduino. Следующая задача — установить необходимые инструменты и программное обеспечение в рамках подготовки к запуску примера приложения на плате Arduino.

![Оборудование готово][hardware-is-ready]

## <a name="next-steps"></a>Дальнейшие действия
[Get the tools][get-the-tools] (Получение инструментов)
<!-- Images and links -->

[kit]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/kit.png
[top-micro-usb-port]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/top_usbport.jpg
[computer-usb]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/computer_usb.jpg
[hardware-is-ready]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/hardware_ready.jpg
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md


<!--HONumber=Jan17_HO4-->


