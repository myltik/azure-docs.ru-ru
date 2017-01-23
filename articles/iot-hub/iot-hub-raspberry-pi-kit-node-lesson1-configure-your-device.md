---
title: "Настройка устройства | Документация Майкрософт"
description: "Настройте устройство Raspberry Pi 3 для первого использования и установите бесплатную ОС Raspbian, которая оптимизирована для оборудования Raspberry Pi."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "установить raspbian, скачать raspbian, как установить raspbian, настройка raspbian, установка raspbian на raspberry pi, установка ОС на raspberry pi, установка sd-карты raspberry pi, подключение raspberry pi, подключиться к raspberry pi, подключения raspberry pi"
ms.assetid: 43f7c2cf-f1a5-4dd5-93f0-7e546c6dc91e
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 68647c83f30fddd69aa920cae6f407f2728e14c8


---
# <a name="configure-your-device"></a>Настройка устройства
## <a name="what-you-will-do"></a>Выполняемая задача
Настройка устройства Pi для первого использования и установка операционной системы Raspbian. Raspbian — это бесплатная операционная система, которая оптимизирована для оборудования Raspberry Pi. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:

* Как установить ОС Raspbian на устройство Pi.
* Как подключить питание к устройству Pi с помощью USB-кабеля.
* Как подключить устройство Pi к сети с помощью кабеля Ethernet или беспроводной сети.
* Как добавить на монтажную плату светодиодный индикатор и подключить его к устройству Pi.

## <a name="what-you-will-need"></a>Необходимые условия
Чтобы выполнить эту операцию, вам понадобятся следующие элементы из начального набора Raspberry Pi 3:

* плата Raspberry Pi 3;
* карта microSD на 16 ГБ;
* источник питания 5 В 2 A с кабелем Micro USB длиной примерно 1,8 метра;
* монтажная плата;
* соединительные провода;
* резистор 560 Ом;
* светодиодный индикатор 10 мм с рассеянным освещением;
* кабель Ethernet.

![Предметы в начальном наборе](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

Кроме того, вам понадобится следующее:

* Проводное или беспроводное подключение к устройству Pi.
* Адаптер USB-SD или карта miniSD для записи образа операционной системы на карту microSD.
* Компьютер под управлением Windows, Mac или Linux. С помощью компьютера ОС Raspbian устанавливается на карту microSD.
* Подключение к Интернету для скачивания необходимых инструментов и программного обеспечения.

## <a name="install-raspbian-on-the-microsd-card"></a>Установка ОС Raspbian на карту microSD
Подготовьте карту microSD для установки образа ОС Raspbian.

1. Скачайте ОС Raspbian.
   1. [Скачайте](https://www.raspberrypi.org/downloads/raspbian/) ZIP-файл с Raspbian Jessie with PIXEL.
   2. Извлеките образ ОС Raspbian в папку на компьютере.
2. Установите ОС Raspbian на карту microSD.
   1. [Скачайте](https://www.etcher.io) и установите служебную программу Etcher для записи карт SD.
   2. Запустите Etcher и выберите образ Raspbian, извлеченный на шаге 1.
   3. Выберите устройство для чтения карт microSD.
      Обратите внимание, что в программе Etcher уже может быть выбрано правильное устройство для чтения.
   4. Щелкните **Flash** (Переключиться), чтобы установить ОС Raspbian на карту microSD.
   5. По завершении установки удалите карту microSD из компьютера.
      Удалять карту microSD напрямую безопасно, так как программа Etcher автоматически извлекает или отключает карту microSD после завершения.
   6. Вставьте карту microSD в устройство Pi.

![Вставка карты SD](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="turn-on-pi"></a>Включение устройства Pi
Включите устройство Pi, используя кабель Micro USB и источник питания.

![Включение](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [!NOTE]
> Важно использовать источник питания из набора с силой тока не менее 2 А, чтобы на устройство Raspberry подавалась энергия, которой достаточно для правильной работы.

## <a name="connect-raspberry-pi-3-to-the-network"></a>Подключение устройства Raspberry Pi 3 к сети
Устройство Pi можно подключить к проводной или беспроводной сети. Убедитесь, что плата Pi подключена к той же сети, что и компьютер. Например, можно подключить плату Pi к тому же коммутатору, к которому подключен компьютер.

### <a name="connect-to-a-wired-network"></a>Подключение к проводной сети
Подключите устройство Pi к проводной сети с помощью кабеля Ethernet. Если соединение установлено, то на устройстве Pi загорятся два светодиодных индикатора.

![Подключение с помощью кабеля Ethernet](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="connect-to-a-wireless-network"></a>Подключение к беспроводной сети
Следуйте [инструкциям](https://www.raspberrypi.org/learning/software-guide/wifi/) от Raspberry Pi Foundation для подключения устройства Pi к беспроводной сети. Для выполнения этих инструкций необходимо сначала подключить к устройству Pi монитор и клавиатуру.

## <a name="connect-the-led-to-pi"></a>Подключение светодиодного индикатора к устройству Pi
Для этого используйте [монтажную плату](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard), соединительные провода, светодиодный индикатор и резистор. Их необходимо подключить к портам [ввода-вывода общего назначения](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) устройства Pi.

![Монтажная плата, светодиодный индикатор и резистор](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. Подключите более короткую ножку индикатора к **GPIO GND (штырь 6)**.
2. Подключите более длинную ножку индикатора к резистору.
3. Подключите другую ножку резистора к **GPIO 4 (штырь 7)**.

Обратите внимание, что полярность индикатора приоритетнее. Этот параметр полярности известен как "Активный низкий".

![Подключение выводов](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

Поздравляем! Вы успешно настроили устройство Pi.

## <a name="summary"></a>Сводка
В этой статье вы узнали, как настроить устройство Pi, установив ОС Raspbian, подключив устройство к сети, а также подключив светодиодный индикатор к устройству. Обратите внимание, что светодиодный индикатор еще не светится. Следующая задача — установить необходимые инструменты и программное обеспечение в рамках подготовки к запуску примера приложения на устройстве Pi.

![Оборудование готово](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>Дальнейшие действия
[Получение инструментов](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)




<!--HONumber=Dec16_HO2-->


