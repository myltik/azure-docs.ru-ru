---
title: "Использование физического устройства с Edge Интернета вещей Azure | Документация Майкрософт"
description: "Сведения об использовании устройства Texas Instruments SensorTag для отправки данных в Центр Интернета вещей через шлюз Edge Интернета вещей, работающий на устройстве Raspberry Pi 3. Шлюз создается с использованием Edge Интернета вещей Azure."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 02962a91c739a53dfcf947bcc736e5c293b9384f
ms.contentlocale: ru-ru
ms.lasthandoff: 06/26/2017


---
# <a name="use-azure-iot-edge-on-a-raspberry-pi-to-forward-device-to-cloud-messages-to-iot-hub"></a>Использование Azure IoT Edge в Raspberry Pi для переадресации отправки сообщений с устройства в облако в Центр Интернета вещей

В этом пошаговом руководстве по созданию [примера устройства Bluetooth с низким энергопотреблением][lnk-ble-samplecode] показано, как использовать [Edge Интернета вещей Azure][lnk-sdk], чтобы:

* пересылать в Центр Интернета вещей данные телеметрии, передаваемые с устройства в облако;
* маршрутизировать команды из Центра Интернета вещей на физическое устройство.

В этом руководстве рассматриваются следующие темы.

* **Архитектура**: важные данные об архитектуре в примере устройства Bluetooth с низким энергопотреблением.
* **Сборка и запуск**: шаги, необходимые для сборки и запуска примера.

## <a name="architecture"></a>Архитектура

В пошаговом руководстве показано, как выполнять сборку и запускать шлюз Edge Интернета вещей на плате Raspberry Pi 3, работающей под управлением ОС Raspbian Linux. Шлюз создается с использованием Edge Интернета вещей. Для сбора данных температуры в этом примере используется устройство Bluetooth с низким энергопотреблением (BLE) Texas Instruments SensorTag.

При запуске шлюз Edge Интернета вещей выполняет следующие действия:

* Подключается к устройству SensorTag с помощью протокола низкого энергопотребления Bluetooth (BLE).
* Подключается к Центру Интернета вещей с помощью протокола HTTP.
* Пересылает данные телеметрии с устройства SensorTag в центр IoT.
* Маршрутизирует команды из центра IoT на устройство SensorTag.

Шлюз содержит следующие модули Edge Интернета вещей:

* *Модуль BLE* , который взаимодействует с устройством BLE для получения от него данных температуры и отправки на него команд.
* *Модуль BLE "Из облака на устройство"*, который преобразовывает сообщения JSON, поступающие из Центра Интернета вещей, в инструкции BLE для *модуля BLE*.
* *Модуль ведения журнала*, который регистрирует в локальном файле все сообщения шлюза.
* *Модуль сопоставления удостоверений* , который преобразовывает MAC-адреса устройств BLE и удостоверения устройств центра Azure IoT.
* *Модуль Центра Интернета вещей* , который передает в Центр Интернета вещей данные телеметрии и получает из него команды устройств.
* *Модуль принтера BLE* интерпретирует данные телеметрии из устройства BLE и печатает форматированные данные в консоль, чтобы включить устранение неполадок и отладку.

### <a name="how-data-flows-through-the-gateway"></a>Прохождение потока данных через шлюз

На следующей блок-схеме показано, как работает конвейер отправки данных телеметрии.

![Конвейер шлюза отправки данных телеметрии](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

Элемент телеметрии на пути из устройства BLE в Центр Интернета вещей выполняет следующие шаги:

1. Устройство BLE создает пример данных температуры и отправляет его через Bluetooth на модуль BLE в шлюзе.
1. Модуль BLE получает этот пример и публикует его в брокер вместе с MAC-адресом устройства.
1. Модуль сопоставления удостоверений извлекает это сообщение и использует внутреннюю таблицу для преобразования MAC-адреса устройства в удостоверение устройства Центра Интернета вещей. Удостоверение устройства Центра Интернета вещей состоит из идентификатора и ключа устройства.
1. Модуль сопоставления удостоверений публикует новое сообщение, содержащее пример данных температуры, MAC-адрес, идентификатор и ключ устройства.
1. Модуль Центра Интернета вещей получает это новое сообщение (созданное модулем сопоставления удостоверений) и публикует его в Центре Интернета вещей.
1. Модуль ведения журнала регистрирует в локальном файле все сообщения из брокера.

На следующей блок-схеме показано, как работает конвейер обработки данных команд устройств.

![Конвейер шлюза команд устройства](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. Модуль Центра Интернета вещей периодически опрашивает Центр Интернета вещей на наличие новых командных сообщений.
1. Когда модуль Центра Интернета вещей получает новое командное сообщение, он публикует его в брокер.
1. Модуль сопоставления удостоверений извлекает это командное сообщение и использует внутреннюю таблицу для преобразования идентификатора устройства Центра Интернета вещей в MAC-адрес устройства. Затем он публикует новое сообщение, содержащее MAC-адрес целевого устройства в схеме сопоставления свойств сообщения.
1. Модуль BLE "Из облака на устройство" извлекает это сообщение и преобразовывает его в правильную инструкцию BLE для модуля BLE. Затем он публикует новое сообщение.
1. Модуль BLE извлекает это сообщение и выполняет инструкцию ввода-вывода, взаимодействуя с устройством BLE.
1. Модуль ведения журнала регистрирует в журнале все сообщения из брокера в файл на диске.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством требуется активная подписка Azure.

> [!NOTE]
> Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в статье о [бесплатной пробной версии Azure][lnk-free-trial].

На настольном компьютере необходимо установить клиент SSH, чтобы иметь удаленный доступ к командной строке Raspberry Pi.

- Windows не предоставляет клиент SSH. Мы советуем использовать [PuTTY](http://www.putty.org/).
- Большинство дистрибутивов Linux и Mac OS содержат служебную программу командной строки SSH. Дополнительные сведения см. в статье [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (Подключение по протоколу SSH с помощью Linux или Mac OS).

## <a name="prepare-your-hardware"></a>Подготовка оборудования

В этом учебнике предполагается, что вы используете устройство [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html), подключенное к плате Raspberry Pi 3 под управлением ОС Raspbian.

### <a name="install-raspbian"></a>Установка Raspbian

Воспользуйтесь одним из приведенных ниже вариантов установки Raspbian на устройстве Raspberry Pi 3.

* Чтобы установить последнюю версию Raspbian, воспользуйтесь графическим пользовательским интерфейсом [NOOBS][lnk-noobs].
* [Скачайте][lnk-raspbian] вручную и запишите новейший образ операционной системы Raspbian на SD-карту.

### <a name="sign-in-and-access-the-terminal"></a>Вход и доступ к терминалу

Существует два варианта получения доступа к среде терминала на Raspberry Pi.

* Если клавиатура и монитор подключены к Raspberry Pi, вы можете использовать графический пользовательский интерфейс Raspbian, чтобы получить доступ к окну терминала.

* Получите доступ к командной строке на устройстве Raspberry Pi с настольного компьютера, используя SSH.

#### <a name="use-a-terminal-window-in-the-gui"></a>Использование окна терминала в графическом пользовательском интерфейсе

По умолчанию в качестве учетных данных для Raspbian используется имя пользователя **pi** и пароль **raspberry**. На панели задач в графическом пользовательском интерфейсе можно запустить служебную программу **Terminal** с помощью значка в виде монитора.

#### <a name="sign-in-with-ssh"></a>Вход с помощью SSH

Используйте SSH, чтобы получить доступ к Raspberry Pi с помощью командной строки. Сведения о настройке SSH на устройстве Raspberry Pi и о подключении из [Windows][lnk-ssh-windows], [Linux и Mac OS][lnk-ssh-linux] см. в руководстве по [SSH (SECURE SHELL)][lnk-pi-ssh].

Войдите, используя имя пользователя **pi** и пароль **raspberry**.

### <a name="install-bluez-537"></a>Установка BlueZ 5.37

Модули BLE взаимодействуют с оборудованием Bluetooth через стек BlueZ. Чтобы модули работали корректно, необходимо установить BlueZ версии 5.37. Эти инструкции помогут вам установить правильную версию BlueZ.

1. Остановите работу текущей управляющей программы Bluetooth:

    ```sh
    sudo systemctl stop bluetooth
    ```

1. Установите зависимости BlueZ:

    ```sh
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```

1. Скачайте исходный код BlueZ с сайта bluez.org:

    ```sh
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```

1. Распакуйте исходный код:

    ```sh
    tar -xvf bluez-5.37.tar.xz
    ```

1. Смените каталоги на вновь созданную папку:

    ```sh
    cd bluez-5.37
    ```

1. Настройте код BlueZ для выполнения сборки:

    ```sh
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```

1. Выполните сборку BlueZ:

    ```sh
    make
    ```

1. По завершении сборки установите BlueZ:

    ```sh
    sudo make install
    ```

1. В файле `/lib/systemd/system/bluetooth.service` измените конфигурацию службы systemd для Bluetooth, чтобы она указывала на новую управляющую программу Bluetooth. Замените строку ExecStart следующим текстом:

    ```conf
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Подключение устройства Raspberry Pi 3 к устройству SensorTag

Перед запуском примера необходимо убедиться, что плата Raspberry Pi 3 имеет возможность подключаться к устройству SensorTag.

1. Убедитесь, что служебная программа `rfkill` установлена:

    ```sh
    sudo apt-get install rfkill
    ```

1. Разблокируйте Bluetooth на устройстве Raspberry Pi 3 и убедитесь, что номер версии — **5.37**:

    ```sh
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```

1. Чтобы войти в интерактивную оболочку Bluetooth, запустите службу Bluetooth и выполните команду **bluetoothctl**:

    ```sh
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Введите команду **power on** , чтобы включить питание контроллера Bluetooth. Команда вернет результат следующего вида:

    ```sh
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. В интерактивной оболочке Bluetooth введите команду **scan on** для поиска устройств Bluetooth. Команда вернет результат следующего вида:

    ```sh
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. Сделайте устройство SensorTag доступным для обнаружения, нажав маленькую кнопку (должен загореться зеленый светодиодный индикатор). Плата Raspberry Pi 3 должна обнаружить устройство SensorTag:

    ```sh
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    В этом примере можно увидеть, что устройство SensorTag имеет MAC-адрес **A0:E6:F8:B5:F6:00**.

1. Отключите поиск, введя команду **scan off**.

    ```sh
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. Подключитесь к устройству SensorTag, используя его MAC-адрес. Для этого введите команду **connect \<MAC-адрес\>**. Для большей ясности следующий пример выходных данных приведен в сокращенном виде.

    ```sh
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```

    > Список характеристик GATT устройства можно получить повторно, используя команду **list-attributes**.

1. Теперь можно отключиться от устройства с помощью команды **disconnect** и выйти из оболочки Bluetooth с помощью команды **quit**:

    ```sh
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Теперь все готово для запуска примера Edge Интернета вещей BLE на устройстве Raspberry Pi 3.

## <a name="run-the-iot-edge-ble-sample"></a>Запуск примера Edge Интернета вещей BLE

Чтобы запустить пример шлюза Edge Интернета вещей BLE, необходимо выполнить три задачи:

* Настроить два примера устройств в центре IoT.
* Создать Edge Интернета вещей на устройстве Raspberry Pi 3.
* Настроить и запустить пример BLE на устройстве Raspberry Pi 3.

Во время записи IoT Edge поддерживает модули BLE только в шлюзах, запущенных на Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Настройка двух примеров устройств в центре IoT

* [Создайте Центр Интернета вещей][lnk-create-hub] в подписке Azure (для выполнения указаний данного пошагового руководства необходимо имя центра). Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* Добавьте в Центр Интернета вещей одно устройство с именем **SensorTag_01**, а затем запишите его идентификатор и ключ устройства. Чтобы добавить устройство в Центр Интернета вещей, созданный на предыдущем шаге, и получить его ключ, можно использовать такие инструменты, как [обозреватель устройств или iothub-explorer][lnk-explorer-tools]. Сопоставление этого устройства с устройством SensorTag будет выполнено при настройке шлюза.

### <a name="build-azure-iot-edge-on-your-raspberry-pi-3"></a>Создание Edge Интернета вещей Azure на устройстве Raspberry Pi 3

Установите зависимые компоненты Edge Интернета вещей Azure:

```sh
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```

Выполните следующие команды, чтобы клонировать Edge Интернета вещей и все его подмодули в корневой каталог:

```sh
cd ~
git clone https://github.com/Azure/iot-edge.git
```

Когда репозиторий Edge Интернета вещей полностью скопирован на устройство Raspberry Pi 3, можно выполнить его сборку, выполнив следующую команду из папки, в которой содержится пакет SDK:

```sh
cd ~/iot-edge
./tools/build.sh  --disable-native-remote-modules
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Настройка и запуск примера BLE на устройстве Raspberry Pi 3

Для начальной загрузки и запуска примера необходимо настроить каждый модуль Edge Интернета вещей, который участвует в шлюзе. Необходимая конфигурация предоставляется в файле JSON. Вам следует настроить все пять участвующих модулей Edge Интернета вещей. В репозитории есть пример JSON-файла с именем **gateway\_sample.json**, на основе которого вы можете создать собственный файл конфигурации. Этот файл находится в папке **samples/ble_gateway/src** в локальной копии репозитория Edge Интернета вещей.

В следующих разделах описывается, как изменить этот файл конфигурации для примера BLE. Кроме того, предполагается, что репозиторий Edge Интернета вещей находится в папке **/home/pi/iot-edge/** на устройстве Raspberry Pi 3. Если репозиторий находится в другом месте, измените соответствующим образом пути.

#### <a name="logger-configuration"></a>Конфигурация средства ведения журнала

Убедившись, что репозиторий шлюза находится в папке **/home/pi/iot-edge/**, настройте модуль ведения журнала следующим образом:

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>Конфигурация модуля BLE

В примере конфигурации для устройства BLE предполагается, что используется устройство Texas Instruments SensorTag. Любое стандартное устройство BLE, которое может функционировать как периферийное устройство GATT, должно работать, но для этого может потребоваться обновление идентификаторов характеристик и данных GATT. Добавьте MAC-адрес устройства SensorTag:

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

Если устройство SensorTag не используется, просмотрите документацию устройства BLE, чтобы определить, следует ли обновить идентификаторы характеристик GATT и значения данных.

#### <a name="iot-hub-module"></a>Модуль Центра Интернета вещей

Добавьте имя центра IoT. Значением суффикса обычно является **azure-devices.net**:

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Конфигурация модуля сопоставления удостоверений

Добавьте MAC-адрес устройства SensorTag, а также идентификатор и ключ устройства **SensorTag_01**, которое вы добавили в Центр Интернета вещей:

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Конфигурация модуля принтера BLE

```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>Конфигурация модуля BLEC2D

```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>Конфигурация маршрутизации

Указанные ниже параметры конфигурации гарантируют, что маршрутизация между модулями Edge Интернета вещей будет выполняться следующим образом:

* Модуль **ведения журнала** получает и регистрирует все сообщения.
* Модуль **SensorTag** отправляет сообщения в модуль **mapping** и модуль **принтера BLE**.
* Модуль **mapping** отправляет в модуль **IoTHub** сообщения, которые затем будут отправляться в ваш Центр Интернета вещей.
* Модуль **IoTHub** отправляет сообщения обратно в модуль **mapping**.
* Модуль **mapping** отправляет сообщения в модуль **BLEC2D**.
* Модуль **BLEC2D** отправляет сообщения обратно в модуль **SensorTag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

Для запуска примера передайте в двоичный файл **ble\_gateway** путь к файлу конфигурации JSON в качестве параметра. В следующей команде предполагается, что вы используете файл конфигурации **gateway_sample.json**. Выполните эту команду из папки **iot-edge** на устройстве Raspberry Pi:

```sh
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Перед запуском примера может потребоваться нажать маленькую кнопку на устройстве SensorTag, чтобы сделать его доступным для обнаружения.

При запуске примера можно использовать такие инструменты, как [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) или [обозреватель Центра Интернета вещей](https://github.com/Azure/iothub-explorer), для мониторинга сообщений, которые шлюз Edge Интернета вещей перенаправляет с устройства SensorTag. Например, с помощью средства iothub-explorer вы можете отслеживать сообщения, отправляемые с устройства в облако, с помощью следующей команды:

```sh
iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
```

## <a name="send-cloud-to-device-messages"></a>Отправка сообщений из облака на устройство

Модуль BLE также поддерживает отправку команд из Центра Интернета вещей на устройство. Для отправки сообщений JSON, которые модуль шлюза BLE направляет на устройство BLE, можно использовать [обозреватель устройств](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) или [обозреватель Центра Интернета вещей (iothub-explorer)](https://github.com/Azure/iothub-explorer).

При использовании устройства Texas Instruments SensorTag можно включить красный или зеленый светодиодные индикаторы или звуковой сигнал, отправив команды из Центра Интернета вещей. Прежде чем отправлять команды из Центра Интернета вещей, необходимо сначала последовательно отправить два сообщения JSON, приведенные ниже. Затем можно будет отправить команды для включения светодиодных индикаторов или звукового сигнала.

1. Выполните сброс всех светодиодных индикаторов и звуковых сигналов (отключите их).

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. Настройте операции ввода-вывода как "удаленные".

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

Теперь можно будет отправить приведенные ниже команды для включения светодиодных индикаторов или звукового сигнала на устройстве SensorTag.

* Включение красного светодиодного индикатора.

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* Включение зеленого светодиодного индикатора.

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* Включение звукового сигнала.

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите подробнее изучить возможности Edge Интернета вещей и поэкспериментировать с примерами кода, см. следующие руководства и ресурсы для разработчиков.

* [Azure IoT Edge][lnk-sdk] (Edge Интернета вещей Azure).

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md

