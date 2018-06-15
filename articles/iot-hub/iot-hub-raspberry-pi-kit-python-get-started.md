---
title: Подключение Raspberry Pi (Python) к Центру Интернета вещей для передачи данных в облако | Документация Майкрософт
description: Узнайте, как подключить компьютер Raspberry Pi к Центру Интернета вещей Azure и передавать данные с этого компьютера в облако Azure.
author: rangv
manager: ''
keywords: Raspberry Pi и Центр Интернета вещей Azure, Raspberry Pi и Центр Интернета вещей, отправка данных с Raspberry Pi в облако, подключение Raspberry Pi к облаку
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 15b917d811dff2d70b638ab82c808bdc7fa1012b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635157"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-python"></a>Подключение Raspberry Pi к Центру Интернета вещей (Python)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

В этом учебнике описано, как начать работу с устройством Raspberry Pi под управлением Raspbian. Также вы узнаете, как можно легко подключать устройства к облаку с помощью [Центра Интернета вещей Azure](iot-hub-what-is-iot-hub.md). Примеры для Windows 10 IoT Базовая представлены в [Центре разработки для Windows](http://www.windowsondevices.com/).

Нет начального набора? Используйте [онлайн-симулятор Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Или купите новый комплект [здесь](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Что нужно сделать

* Создайте Центр Интернета вещей.
* Зарегистрируем устройство для Pi в Центре Интернета вещей.
* Настроим Raspberry Pi.
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

## <a name="set-up-raspberry-pi"></a>Настройка Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Установка операционной системы Raspbian на Pi

Подготовьте карту microSD для установки образа ОС Raspbian.

1. Скачайте ОС Raspbian.
   1. [Скачайте Raspbian Jessie with Desktop](https://www.raspberrypi.org/downloads/raspbian/) (ZIP-файл).
   1. Извлеките образ ОС Raspbian в папку на компьютере.
1. Установите ОС Raspbian на карту microSD.
   1. [Скачайте и установите служебную программу Etcher для записи данных на карты SD](https://etcher.io/).
   1. Запустите Etcher и выберите образ Raspbian, извлеченный на шаге 1.
   1. Выберите устройство для чтения карт microSD. Обратите внимание, что в программе Etcher уже может быть выбрано правильное устройство для чтения.
   1. Щелкните Flash (Переключиться), чтобы установить ОС Raspbian на карту microSD.
   1. По завершении установки удалите карту microSD из компьютера. Удалять карту microSD напрямую безопасно, так как программа Etcher автоматически извлекает или отключает карту microSD после завершения.
   1. Вставьте карту microSD в устройство Pi.

### <a name="enable-ssh-and-i2c"></a>Включение SSH и I2C

1. Подключите Pi к монитору, клавиатуре и мыши, запустите Pi, а затем войдите в Raspbian, используя `pi` в качестве имени пользователя и `raspberry` в качестве пароля.
1. Щелкните значок Raspberry и выберите **Preferences** (Параметры) > **Raspberry Pi Configuration** (Конфигурация Raspberry Pi).

   ![Меню параметров Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. На вкладке **Interfaces** (Интерфейсы) установите для параметров **I2C** и **SSH** значение **Enable** (Включить), а затем нажмите кнопку **ОК**. Этот шаг является необязательным, если у вас нет физических датчиков и вы будете использовать симулированные данные датчика.

   ![Включение I2C и SSH на Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Сведения о том, как включить SSH и I2C, можно найти в дополнительных справочных документах на сайтах [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) и [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Подключение датчика к Pi

Подключите светодиодный индикатор и датчик BME280 к Pi с помощью монтажной платы и оптоволоконных кабелей, как показано ниже. Если у вас нет датчика, [пропустите этот раздел](#connect-pi-to-the-network).

![Подключение Raspberry Pi и датчика](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

Датчик BME280 может собирать данные о температуре и влажности. Светодиодный индикатор мигает при обмене данными между устройством и облаком. 

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

### <a name="install-the-prerequisite-packages"></a>Установка пакетов необходимых компонентов

Используйте один из следующих SSH-клиентов для подключения к Raspberry Pi с главного компьютера.
   
   **Пользователи Windows**
   1. Скачайте и установите [PuTTY](http://www.putty.org/) для Windows. 
   1. Скопируйте IP-адрес устройства Pi и вставьте его в поле для имени узла (или для IP-адреса), а затем выберите тип подключения SSH.
   
   
   **Пользователи MAC и Ubuntu**
   
   Используйте SSH-клиент, встроенный в Ubuntu или macOS. Возможно, для подключения устройства Pi по протоколу SSH потребуется выполнить `ssh pi@<ip address of pi>`.
   > [!NOTE] 
   Имя пользователя по умолчанию — `pi`, а пароль — `raspberry`.


### <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Создайте клон примера приложения, выполнив следующую команду:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-python-raspberrypi-client-app.git
   ```
1. Откройте файл конфигурации, выполнив следующую команду:

   ```bash
   cd iot-hub-python-raspberrypi-client-app
   nano config.py
   ```

   В этом файле можно настроить 5 макросов. Первый из них — `MESSAGE_TIMESPAN`, который задает время (в миллисекундах) между отправкой двух сообщений в облако. Второй, `SIMULATED_DATA`, представляет собой логическое значение, которое определяет, будут ли использоваться смоделированные данные датчика. `I2C_ADDRESS` — I2C-адрес, к которому подключен датчик BME280. `GPIO_PIN_ADDRESS` — адрес объекта групповой политики для индикатора. И наконец, `BLINK_TIMESPAN` задает длительность включения индикатора в миллисекундах.

   Если у вас **нет датчика**, задайте для параметра `SIMULATED_DATA` значение `True`, чтобы пример приложения создал и использовал смоделированные данные датчика.

1. Сохраните изменения и закройте окно, нажав клавиши Control-O > ВВОД > Control-X.

### <a name="build-and-run-the-sample-application"></a>Создание и запуск примера приложения

1. Создайте пример приложения, выполнив следующую команду. Пакеты SDK для Azure IoT для Python являются оболочкой аналогичных пакетов для языка C. Поэтому, если вам нужно собрать библиотеки Python из исходного кода, вам потребуется скомпилировать библиотеки C.

   ```bash
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```
   > [!NOTE] 
   Вы можете указать требуемую версию, выполнив скрипт `sudo ./setup.sh [--python-version|-p] [2.7|3.4|3.5]`. Если запустить этот скрипт без параметра, он автоматически обнаружит установленную версию Python (методом перебора в следующей последовательности: 2.7 -> 3.4 -> 3.5). Следите за тем, чтобы версии Python во время сборки и выполнения совпадали. 
   
   > [!NOTE] 
   Процесс сборки клиентской библиотеки Python (iothub_client.so) на устройствах Linux, которые имеют менее 1 ГБ оперативной памяти, может "зависнуть" на уровне 98 % при сборке файла iothub_client_python.cpp, как показано ниже: `[ 98%] Building CXX object python/src/CMakeFiles/iothub_client_python.dir/iothub_client_python.cpp.o`. Если вы встретите такую проблему, проверьте потребление памяти на устройстве, выполнив в другом окне терминала команду `free -m command`. Если памяти для сборки файла iothub_client_python.cpp недостаточно, вы можете попробовать временно увеличить область буфера. Это увеличит объем доступной памяти и повысит шансы успешной сборки библиотеки пакета SDK для Python на стороне клиента.
   
1. Запустите пример приложения, выполнив следующую команду:

   ```bash
   python app.py '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Обязательно скопируйте и вставьте строку подключения устройства, заключив ее в одинарные кавычки. Если вы используете Python 3, можете выполнить команду `python3 app.py '<your Azure IoT hub device connection string>'`.


   Должны отобразиться следующие результаты, содержащие данные датчика и сообщения, которые отправляются в Центр Интернета вещей.

   ![Выходные данные — данные датчика, отправленные с Raspberry Pi в Центр Интернета вещей](media/iot-hub-raspberry-pi-kit-c-get-started/success.png
)

## <a name="next-steps"></a>Дополнительная информация

Вы запустили пример приложения, чтобы собрать данные датчика и отправить их в Центр Интернета вещей. Сведения о том, как просматривать сообщения, отправляемые устройством Raspberry Pi в Центр Интернета вещей, а также как отправлять сообщения на устройство Raspberry Pi в интерфейсе командной строки, см. в руководстве по [управлению обменом сообщениями между облаком и устройством с помощью обозревателя Центра Интернета вещей](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
