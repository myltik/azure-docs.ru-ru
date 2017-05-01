---
title: "Подключение Feather M0 WiFi к Центру Интернета вещей Azure в облаке | Документация Майкрософт"
description: "В этой статье объясняется, как подключить устройство Arduino под названием Adafruit Feather M0 WiFi к Центру Интернета вещей Azure — облачной службе Майкрософт, которая помогает управлять ресурсами Интернета вещей."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: e2d44f821635ce9d91b67ecdc0653e2ba9c99b01
ms.lasthandoff: 04/13/2017


---

# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Подключение Adafruit Feather M0 WiFi к Центру Интернета вещей Azure в облаке
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Подключение между датчиком BME280, платой Feather M0 WiFi и Центром Интернета вещей](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

В этом руководстве вы начнете с того, что узнаете основы работы с платой Arduino. Также вы узнаете, как можно легко подключать устройства к облаку с помощью [Центра Интернета вещей Azure](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>В рамках этого руководства мы:

Подключим плату Adafruit Feather M0 WiFi к созданному Центру Интернета вещей. Затем запустим пример приложения на M0 WiFi, чтобы собрать данные о температуре и влажности, полученные с датчика BME280. После этого отправим данные с датчика в Центр Интернета вещей.



## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей и зарегистрировать устройство для платы Feather M0 WiFi.
* Как подключить Feather M0 WiFi к датчику и компьютеру.
* Как собирать данные датчиков, запустив пример приложения на Feather M0 WiFi.
* Как отправить данные датчиков в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

![Элементы, необходимые в рамках руководства](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Чтобы выполнить эту операцию, вам понадобятся следующие элементы из начального набора Feather M0 WiFi:

* плата Feather M0 WiFi;
* кабель micro-USB (тип A).

Кроме того, вам понадобятся следующие элементы среды разработки:

* ПК или компьютер Mac под управлением Windows или Ubuntu;
* беспроводная сеть, к которой подключается Feather M0 WiFi;
* подключение к Интернету для скачивания средства настройки;
* [Arduino IDE](https://www.arduino.cc/en/main/software) версии 1.6.8 или более новой (более ранние версии несовместимы с библиотекой Azure IoT).


Ниже приведены необязательные элементы, используемые в случае отсутствия датчика. Вы также можете использовать симулированные данные датчика.

* Датчик температуры и влажности BME280
* Монтажная плата
* Многомодовый оптоволоконный кабель с разъемами на обоих концах

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Подключение Feather M0 WiFi к датчику и компьютеру
В этом разделе рассказывается о том, как подключить датчики к плате. Затем устройство подключается к компьютеру для дальнейшего использования.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Подключение датчика температуры и влажности DHT22 к Feather M0 WiFi

Чтобы установить подключение, используйте монтажную плату и оптоволоконный кабель с разъемами на обоих концах, как показано ниже. Если у вас нет датчика, пропустите этот раздел, так как вместо него вы можете использовать симулированные данные.

![Справочные материалы по подключению](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Чтобы подключить выводы датчика, используйте следующие кабели:


| Начало (датчик)           | Конец (плата)            | Цвет кабеля   |
| -----------------------  | ---------------------- | ------------: |
| VDD (вывод 27A)            | 3V (вывод 3A)            | Красный кабель     |
| GND (вывод 29A)            | GND (вывод 6A)           | Черный кабель   |
| SCK (вывод 30A)            | SCK (вывод 12A)          | Желтый кабель  |
| SDO (вывод 31A)            | MI (вывод 14A)           | Белый кабель   |
| SDI (вывод 32A)            | M0 (вывод 13A)           | Синий кабель    |
| CS (вывод 33A)             | GPIO 5 (вывод 15J)       | Оранжевый кабель  |

Дополнительные сведения см. в статье [Adafruit BME280 Humidity + Barometric Pressure + Temperature Sensor Breakout](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) (Выходы датчика влажности, атмосферного давления и температуры Adafruit BME280) и [Adafruit Feather M0 WiFi Pinouts](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts) (Выводы платы Adafruit Feather M0 WiFi).



Теперь работающий датчик должен быть подключен к Feather M0 WiFi.

![Подключение DHT22 к Feather Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Подключение Feather M0 WiFi к компьютеру

Как показано далее, для подключения Feather M0 WiFi к компьютеру используется кабель Micro-USB типа A.

![Подключение Feather Huzzah к компьютеру](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Добавление разрешений для последовательного порта (только в Ubuntu)

При использовании Ubuntu убедитесь, что у вас есть разрешения для работы с USB-портом платы Feather M0 WiFi. Чтобы добавить разрешения для работы с последовательным портом, сделайте следующее:


1. В окне терминала выполните следующие команды:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Вы получите выходные данные одного из следующих типов:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   В выходных данных обратите внимание на параметр `uucp` или `dialout`. Это имя владельца группы USB-порта.

1. Добавьте пользователя в группу, выполнив следующую команду:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` — это имя владельца группы, полученное на предыдущем этапе. `<username>` — это имя пользователя Ubuntu.

1. Чтобы изменения стали видны, выйдите из Ubuntu и войдите снова.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Сбор данных датчиков и их отправка в Центр Интернета вещей

В этом разделе рассказывается о том, как развернуть и запустить пример приложения на плате Feather M0 WiFi. Пример приложения включает и отключает светодиодный индикатор на плате Feather M0 WiFi и отправляет данные о температуре и влажности, собранные с датчика BME280, в Центр Интернета вещей.

### <a name="get-the-sample-application-from-github-and-prepare-arduino-ide"></a>Получение примера приложения из GitHub и подготовка интегрированной среды разработки Arduino

Пример приложения находится на сайте GitHub. Клонируйте репозиторий, содержащий пример приложения с GitHub. Чтобы клонировать пример репозитория, сделайте следующее:

1. Откройте окно командной строки или терминала.
1. Перейдите к папке, в которую необходимо сохранить пример приложения.
1. Выполните следующую команду:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

Установите пакет для Feather M0 WiFi в интегрированной среде разработки Arduino:

1. Откройте папку, где хранится пример приложения.
1. Откройте файл app.ino в папке приложения в Arduino IDE.

   ![Открытие примера приложения в интегрированной среде разработки Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)

1. Щелкните **Инструменты** > **Платы** > **Менеджер плат**, а затем установите `Arduino SAMD Boards` версии `1.6.2` или более новой. 

   Диспетчер плат показывает, что установлена версия `Arduino SAMD Boards` `1.6.2` или более новая.

   ![Завершение установки пакета ESP8266](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

1. Щелкните **Инструменты** > **Платы** > **Adafruit M0 WiFi**.

1. Установите драйверы (только в Windows). Возможно, при подключении платы Feather понадобится установить драйвер. Щелкните [здесь](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) для загрузки его установщика.
   Выполните соответствующие действия, чтобы установить необходимые драйверы.

### <a name="install-necessary-libraries"></a>Установка необходимых библиотек

1. В Arduino IDE щелкните **Скетч** > **Подключить библиотеку** > **Управлять библиотеками**.
1. Выполните поиск приведенных ниже имен библиотек по очереди. Для каждой найденной библиотеки нажмите кнопку **Установить**.
   * `Adafruit_WINC1500`
   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-bme280-sensor"></a>Отсутствие датчика BME280

Если у вас нет датчика BME280, пример приложения может смоделировать данные о температуре и влажности. Чтобы настроить использование смоделированных данных в примере приложения, сделайте следующее:

1. Откройте файл `config.h` в папке `app`.
1. Найдите приведенную ниже строку кода и измените значение `false` на `true`.
   ```c
   define SIMULATED_DATA true
   ```
   ![Настройка использования смоделированных данных в примере приложения](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

1. Сохраните файл с помощью клавиш `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Развертывание примера приложения для Feather M0 WiFi

1. В интегрированной среде разработки Arduino щелкните **Tool** (Инструменты) > **Port** (Порт), а затем выберите последовательный порт для Feather M0 WiFi.
1. Чтобы создать и развернуть пример приложения для Feather M0 WiFi, щелкните **Sketch** (Эскиз) > **Upload** (Передать).

### <a name="enter-your-credentials"></a>Ввод учетных данных

После завершения загрузки введите учетные данные следующим образом:

1. В Arduino IDE щелкните **Инструменты** > **Монитор последовательного порта**.
1. В окне монитора последовательного порта обратите внимание на два раскрывающихся списка в нижнем правом углу.
1. В раскрывающемся списке слева выберите **No line ending** (Ничего не добавлять к отправляемой строке).
1. В раскрывающемся списке справа выберите **115200 baud** (115200 бод).
1. При появлении запроса в поле ввода, расположенном в верхней части окна монитора последовательного порта, введете приведенные ниже сведения, а затем нажмите кнопку **Отправить**.
   * Идентификатор SSID для подключения Wi-Fi.
   * Пароль Wi-Fi.
   * Строка подключения к устройству.

> [!Note]
> Учетные данные хранятся в EEPROM платы Feather M0 WiFi. При нажатии кнопки сброса на плате Feather M0 WiFi в примере приложения отобразится сообщение с запросом на удаление информации. Введите `Y` для удаления данных. Вам будет предложено предоставить их еще раз.

### <a name="verify-the-sample-application-is-running-successfully"></a>Проверка работоспособности примера приложения

Если в окне монитора последовательного порта отобразились приведенные ниже выходные данные и на плате Feather M0 WiFi мигает светодиодный индикатор, значит пример приложения запущен успешно.

![Окончательные выходные данные в интегрированной среде разработки Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Дальнейшие действия

Вы успешно подключили плату Feather M0 WiFi к Центру Интернета вещей и отправили в него собранные данные датчика. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


