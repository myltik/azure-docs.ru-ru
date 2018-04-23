---
title: Подключение Raspberry Pi (Node.js) к Центру Интернета вещей для передачи данных в облако | Документация Майкрософт
description: Узнайте, как подключить компьютер Raspberry Pi к Центру Интернета вещей Azure и передавать данные с этого компьютера в облако Azure.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: Raspberry Pi и Центр Интернета вещей Azure, Raspberry Pi и Центр Интернета вещей, отправка данных с Raspberry Pi в облако, подключение Raspberry Pi к облаку
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e7db2f78a5c1a942f64a2c0a40068fffe90749d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/20/2018
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Подключение Raspberry Pi к Центру Интернета вещей Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

В этом учебнике описано, как начать работу с устройством Raspberry Pi под управлением Raspbian. Также вы узнаете, как можно легко подключать устройства к облаку с помощью [Центра Интернета вещей Azure](iot-hub-what-is-iot-hub.md). Примеры для Windows 10 IoT Базовая представлены в [Центре разработки для Windows](http://www.windowsondevices.com/).

Нет начального набора? Используйте [онлайн-симулятор Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Или купите новый комплект [здесь](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>Что нужно сделать

* Создайте Центр Интернета вещей.
* Зарегистрируем устройство для Pi в Центре Интернета вещей.
* Настроим Raspberry Pi.
* Мы запустим пример приложения на Pi для отправки данных в Центр Интернета вещей.

## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей Azure и получить строку подключения нового устройства.
* Как подключать Pi к датчику BME280.
* Как собирать данные датчика, запустив пример приложения на Pi.
* Как отправить данные датчика в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

![Необходимые элементы](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

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
Если у вас нет дополнительных элементов, можно использовать имитацию датчиков.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Настройка Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Установка операционной системы Raspbian на Pi

Подготовьте карту microSD для установки образа ОС Raspbian.

1. Скачайте ОС Raspbian.
   1. [Скачайте Raspbian Stretch](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (ZIP-файл).

   > [!WARNING]
   > Используйте ссылку выше, чтобы скачать ZIP-файл образа `raspbian-2017-07-5`. Последняя версия образа Raspbian имеет некоторые известные проблемы с Wiring-Pi Node, которые могут привести к сбою при выполнении дальнейших шагов.
   1. Извлеките образ ОС Raspbian в папку на компьютере.

1. Установите ОС Raspbian на карту microSD.
   1. [Скачайте и установите служебную программу Etcher для записи данных на карты SD](https://etcher.io/).
   1. Запустите Etcher и выберите образ Raspbian, извлеченный на шаге 1.
   1. Выберите устройство для чтения карт microSD. В программе Etcher уже может быть выбрано правильное устройство для чтения.
   1. Щелкните Flash (Переключиться), чтобы установить ОС Raspbian на карту microSD.
   1. По завершении установки удалите карту microSD из компьютера. Удалять карту microSD напрямую безопасно, так как программа Etcher автоматически извлекает или отключает карту microSD после завершения.
   1. Вставьте карту microSD в устройство Pi.

### <a name="enable-ssh-and-i2c"></a>Включение SSH и I2C

1. Подключите Pi к монитору, клавиатуре и мыши. 
1. Запустите Pi, а затем войдите в Raspbian, используя `pi` в качестве имени пользователя и `raspberry` в качестве пароля.
1. Щелкните значок Raspberry и выберите **Preferences** (Параметры) > **Raspberry Pi Configuration** (Конфигурация Raspberry Pi).

   ![Меню параметров Raspbian](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. На вкладке **Interfaces** (Интерфейсы) установите для параметров **I2C** и **SSH** значение **Enable** (Включить), а затем нажмите кнопку **ОК**. Этот шаг является необязательным, если у вас нет физических датчиков и вы будете использовать симулированные данные датчика.

   ![Включение I2C и SSH на Raspberry Pi](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
Сведения о том, как включить SSH и I2C, можно найти в дополнительных справочных документах на [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) и [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Подключение датчика к Pi

Подключите светодиодный индикатор и датчик BME280 к Pi с помощью монтажной платы и оптоволоконных кабелей, как показано ниже. Если у вас нет датчика, [пропустите этот раздел](#connect-pi-to-the-network).

![Подключение Raspberry Pi и датчика](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

Датчик BME280 может собирать данные о температуре и влажности. Светодиодный индикатор мигает, когда устройство отправляет сообщение в облако. 

Чтобы подключить выводы датчика, используйте следующие кабели:

| Начало (датчик и светодиодный индикатор)     | Конец (плата)            | Цвет кабеля   |
| -----------------------  | ---------------------- | ------------: |
| VDD (вывод 5G)             | 3.3V PWR (вывод 1)       | Белый кабель   |
| GND (вывод 7G)             | GND (вывод 6)            | Коричневый кабель   |
| SDI (вывод 10G)            | I2C1 SDA (вывод 3)       | Красный кабель     |
| SCK (вывод 8G)             | I2C1 SCL (вывод 5)       | Оранжевый кабель  |
| LED VDD (вывод 18F)        | GPIO 24 (вывод 18)       | Белый кабель   |
| LED GND (вывод 17F)        | GND (вывод 20)           | Черный кабель   |

Щелкните, чтобы просмотреть [схему соответствия выводов Raspberry Pi 2 и 3](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) для справки.

После успешного подключения датчика BME280 к Raspberry Pi схема должна выглядеть так, как на изображении ниже.

![Подключенный компьютер Pi и датчик BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Подключение устройства Pi к сети

Включите устройство Pi, используя кабель Micro USB и источник питания. Подключите Pi к проводной сети с помощью кабеля Ethernet или выполните [инструкции](https://www.raspberrypi.org/learning/software-guide/wifi/) от Raspberry Pi Foundation для подключения устройства Pi к беспроводной сети. После успешного подключения Pi к сети необходимо запомнить [IP-адрес устройства Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Подключение к проводной сети](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Убедитесь, что плата Pi подключена к той же сети, что и компьютер. Например, если компьютер подключен к беспроводной сети, а плата Pi подключена к проводной сети, то IP-адрес может не отобразиться в выходных данных devdisco.

## <a name="run-a-sample-application-on-pi"></a>Запуск примера приложения на Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Клонирование примера приложения и установка пакетов необходимых компонентов

1. Используйте один из следующих SSH-клиентов для подключения к Raspberry Pi с главного компьютера:
   
   **Пользователи Windows**
   1. Скачайте и установите [PuTTY](http://www.putty.org/) для Windows. 
   1. Скопируйте IP-адрес устройства Pi в разделе с именем узла (или IP-адресом) и выберите SSH как тип подключения.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Пользователи MAC и Ubuntu**
   
   Используйте SSH-клиент, встроенный в Ubuntu или macOS. Возможно, для подключения устройства Pi по протоколу SSH потребуется выполнить `ssh pi@<ip address of pi>`.
   > [!NOTE] 
   Имя пользователя по умолчанию — `pi`, а пароль — `raspberry`.

1. Установите Node.js и NPM на устройстве Pi.
   
   Сначала проверьте версии Node.js. 
   
   ```bash
   node -v
   ```

   Если версия ниже, чем 4.x, или если платформа Node.js не установлена на Pi, установите последнюю версию.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

1. Клонируйте пример приложения.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. Установите все пакеты для примера, в том числе пакет SDK для устройств Azure IoT, библиотеку датчика BME280 и библиотеку Wiring Pi.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   В зависимости от сетевого подключения процесс установки может занять несколько минут.

### <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Откройте файл конфигурации, выполнив следующую команду:

   ```bash
   nano config.json
   ```

   ![Файл конфигурации](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   В этом файле можно настроить два элемента. Первый — `interval`. Он определяет время (в миллисекундах) между отправкой двух сообщений в облако. Второй — `simulatedData`. Он представляет логическое значение, определяющее, будут ли использоваться смоделированные данные датчика.

   Если у вас **нет датчика**, задайте для параметра `simulatedData` значение `true`, чтобы пример приложения создал и использовал смоделированные данные датчика.

1. Сохраните изменения и закройте окно, нажав клавиши CTRL+O > ВВОД > CTRL+X.

### <a name="run-the-sample-application"></a>Запуск примера приложения

Запустите пример приложения, выполнив следующую команду:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Обязательно скопируйте и вставьте строку подключения устройства, заключив ее в одинарные кавычки.


Должны отобразиться следующие результаты, содержащие данные датчика и сообщения, которые отправляются в Центр Интернета вещей.

![Выходные данные — данные датчика, отправленные с Raspberry Pi в Центр Интернета вещей](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>Дополнительная информация

Вы запустили пример приложения, чтобы собрать данные датчика и отправить их в Центр Интернета вещей. Сведения о том, как просматривать сообщения, отправляемые устройством Raspberry Pi в Центр Интернета вещей, а также как отправлять сообщения на устройство Raspberry Pi в интерфейсе командной строки, см. в руководстве по [управлению обменом сообщениями между облаком и устройством с помощью обозревателя Центра Интернета вещей](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
