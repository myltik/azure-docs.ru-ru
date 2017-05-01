---
title: "Подключение Raspberry Pi (C) к Центру Интернета вещей для передачи данных в облако | Документация Майкрософт"
description: "Подключение компьютера Raspberry Pi к Центру Интернета вещей Azure для передачи данных с него в облако Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Raspberry Pi и Центр Интернета вещей Azure, Raspberry Pi и Центр Интернета вещей, отправка данных с Raspberry Pi в облако, подключение Raspberry Pi к облаку"
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/13/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 387dcace5be29de52b465bc53fa81a3dbf876390
ms.lasthandoff: 04/18/2017


---

# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Подключение Raspberry Pi к Центру Интернета вещей Azure (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

В этом учебнике описано, как начать работу с устройством Raspberry Pi под управлением Raspbian. Также вы узнаете, как можно легко подключать устройства к облаку с помощью [Центра Интернета вещей Azure](iot-hub-what-is-iot-hub.md). Примеры для Windows 10 IoT Базовая представлены в [Центре разработки для Windows](http://www.windowsondevices.com/).

## <a name="what-you-do"></a>В рамках этого руководства мы:

* Настроим Raspberry Pi.
* Создайте центр IoT.
* Зарегистрируем устройство для Pi в Центре Интернета вещей.
* Мы запустим пример приложения на Pi для отправки данных в Центр Интернета вещей.

Подключим Raspberry Pi к созданному Центру Интернета вещей. Затем запустим пример приложения на Pi, чтобы собрать данные о температуре и влажности, полученные с датчика BME280. После этого отправим данные с датчика в Центр Интернета вещей.

## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей Azure и получить строку подключения нового устройства.
* Как подключать Pi к датчику BME280.
* Как собирать данные датчика, запустив пример приложения на Pi.
* Как отправить данные датчика в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

![Необходимые элементы](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Плата Raspberry Pi 2 или Raspberry Pi 3.
* Активная подписка Azure. Если ее нет, можно создать [бесплатную пробную учетную запись Azure](https://azure.microsoft.com/free/) всего за несколько минут.
* Монитор, USB-клавиатура и мышь, подключенные к Pi.
* ПК или компьютер Mac под управлением Windows или Linux.
* Подключение к Интернету.
* Карта microSD емкостью 16 ГБ или больше.
* Адаптер USB-SD или карта microSD для записи образа операционной системы на карту microSD.
* Источник питания 5 В 2 A с кабелем Micro USB длиной примерно 1,8 метра.

Ниже приведены необязательные компоненты.

* Датчик температуры, давления и влажности Adafruit BME280 в сборе.
* Монтажная плата.
* 6 оптоволоконных кабелей с разъемом на одном конце и гнездом на другом.
* Светодиодный индикатор 10 мм с рассеянным освещением.


> [!NOTE] 
Эти компоненты необязательны, поскольку пример кода поддерживает использование смоделированных данных датчиков.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Настройка Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Установка операционной системы Raspbian на Pi

Подготовьте карту microSD для установки образа ОС Raspbian.

1. Скачайте ОС Raspbian.
   1. [Скачайте Raspbian Jessie with Pixel](https://www.raspberrypi.org/downloads/raspbian/) (ZIP-файл).
   1. Извлеките образ ОС Raspbian в папку на компьютере.
1. Установите ОС Raspbian на карту microSD.
   1. [Скачайте и установите служебную программу Etcher для записи данных на карты SD](https://etcher.io/).
   1. Запустите Etcher и выберите образ Raspbian, извлеченный на шаге 1.
   1. Выберите устройство для чтения карт microSD. Обратите внимание, что в программе Etcher уже может быть выбрано правильное устройство для чтения.
   1. Щелкните Flash (Переключиться), чтобы установить ОС Raspbian на карту microSD.
   1. По завершении установки удалите карту microSD из компьютера. Удалять карту microSD напрямую безопасно, так как программа Etcher автоматически извлекает или отключает карту microSD после завершения.
   1. Вставьте карту microSD в устройство Pi.

### <a name="enable-ssh-and-spi"></a>Включение SSH и SPI

1. Подключите Pi к монитору, клавиатуре и мыши, запустите Pi, а затем войдите в Raspbian, используя `pi` в качестве имени пользователя и `raspberry` в качестве пароля.
1. Щелкните значок Raspberry и выберите **Preferences** (Параметры) > **Raspberry Pi Configuration** (Конфигурация Raspberry Pi).

   ![Меню параметров Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. На вкладке **Interfaces** (Интерфейсы) установите для параметров **SPI** и **SSH** значение **Enable** (Включить), а затем нажмите кнопку **ОК**.

   ![Включение SPI и SSH на Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Сведения о том, как включить SSH и SPI, можно найти в дополнительных справочных документах на [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) и [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Подключение датчика к Pi

Подключите светодиодный индикатор и датчик BME280 к Pi с помощью монтажной платы и оптоволоконных кабелей, как показано ниже. Если у вас нет датчика, пропустите этот раздел.

![Подключение Raspberry Pi и датчика](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

Чтобы подключить выводы датчика, используйте следующие кабели:

| Начало (датчик и светодиодный индикатор)     | Конец (плата)            | Цвет кабеля   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (вывод 5G)         | GPIO 4 (вывод 7)         | Белый кабель   |
| LED GND (вывод 6G)         | GND (вывод 6)            | Черный кабель   |
| VDD (вывод 18F)            | 3.3V PWR (вывод 17)      | Белый кабель   |
| GND (вывод 20F)            | GND (вывод 20)           | Черный кабель   |
| SCK (вывод 21F)            | SPI0 SCLK (вывод 23)     | Оранжевый кабель  |
| SDO (вывод 22F)            | SPI0 MISO (вывод 21)     | Желтый кабель  |
| SDI (вывод 23F)            | SPI0 MOSI (вывод 19)     | Зеленый кабель   |
| CS (вывод 24F)             | SPI0 CS (вывод 24)       | Синий кабель    |

Щелкните, чтобы просмотреть [схему соответствия выводов Raspberry Pi 2 и 3](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) для справки.

После успешного подключения датчика BME280 к Raspberry Pi схема должна выглядеть так, как на изображении ниже.

![Подключенный компьютер Pi и датчик BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

Включите устройство Pi, используя кабель Micro USB и источник питания. Подключите Pi к проводной сети с помощью кабеля Ethernet или выполните [инструкции](https://www.raspberrypi.org/learning/software-guide/wifi/) от Raspberry Pi Foundation для подключения устройства Pi к беспроводной сети.

![Подключение к проводной сети](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Запуск примера приложения на Pi

### <a name="install-the-prerequisite-packages"></a>Установка пакетов необходимых компонентов

1. Используйте один из следующих SSH-клиентов для подключения к Raspberry Pi с главного компьютера.
    - [PuTTY](http://www.putty.org/) для Windows.
    - Встроенный SSH-клиент ОС Ubuntu или macOS.

1. Установите пакеты необходимых компонентов пакета SDK для устройств Microsoft Azure IoT для C и Cmake, выполнив следующие команды:

   ```bash
   grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
   sudo apt-get update
   sudo apt-get install -y azure-iot-sdk-c-dev cmake
   ```


### <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Создайте клон примера приложения, выполнив следующую команду:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app
   ```
1. Откройте файл конфигурации, выполнив следующую команду:

   ```bash
   cd iot-hub-c-raspberry-pi-clientapp
   nano config.json
   ```

   ![Файл конфигурации](media/iot-hub-raspberry-pi-kit-c-get-started/6_config-file.png)

   В этом файле можно настроить два макроса. Первый из них — `INTERVAL`, который определяет промежуток времени между отправкой двух сообщений в облако. Второй — `SIMULATED_DATA`, который представляет собой логическое значение, определяющее, будут ли использоваться смоделированные данные датчика.

   Если у вас **нет датчика**, задайте для параметра `SIMULATED_DATA` значение `1`, чтобы пример приложения создал и использовал смоделированные данные датчика.

1. Сохраните изменения и закройте окно, нажав клавиши Control-O > ВВОД > Control-X.

### <a name="build-and-run-the-sample-application"></a>Создание и запуск примера приложения

1. Создайте пример приложения, выполнив следующую команду:

   ```bash
   cmake . && make
   ```
   ![Выходные данные при создании](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Запустите пример приложения, выполнив следующую команду:

   ```bash
   sudo ./app '<device connection string>'
   ```

   > [!NOTE] 
   Обязательно скопируйте и вставьте строку подключения устройства, заключив ее в одинарные кавычки.


Должны отобразиться следующие результаты, содержащие данные датчика и сообщения, которые отправляются в Центр Интернета вещей.

![Выходные данные — данные датчика, отправленные с Raspberry Pi в Центр Интернета вещей](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Дальнейшие действия

Вы запустили пример приложения, чтобы собрать данные датчика и отправить их в Центр Интернета вещей.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

