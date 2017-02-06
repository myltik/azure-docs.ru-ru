---
title: "Использование физического устройства с пакетом SDK для шлюза Azure IoT | Документация Майкрософт"
description: "Сведения об использовании устройства Texas Instruments SensorTag для отправки данных в Центр Интернета вещей через шлюз, работающий на устройстве Raspberry Pi 3. Шлюз собран с использованием пакета SDK для шлюза Azure IoT."
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
ms.date: 11/14/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 05c82a87e839a0a95e7050092d6f6867e76fb316


---
# <a name="use-the-azure-iot-gateway-sdk-to-send-device-to-cloud-messages-with-a-physical-device-linux"></a>Воспользуйтесь пакетом SDK для шлюза Azure IoT для отправка сообщений с устройства в облако с помощью физического устройства (Linux).
В этом пошаговом руководстве по использованию [примера устройства Bluetooth с низким энергопотреблением][lnk-ble-samplecode] показано, как использовать [пакет SDK для шлюза Azure IoT][lnk-sdk] для пересылки данных телеметрии, передаваемых из устройства в облако, с физических устройств в Центр Интернета вещей, и как передавать команды из Центра Интернета вещей на физические устройства.

В этом руководстве рассматриваются следующие темы.

* **Архитектура**: важные данные об архитектуре в примере устройства Bluetooth с низким энергопотреблением.
* **Сборка и запуск**: шаги, необходимые для сборки и запуска примера.

## <a name="architecture"></a>Архитектура
В пошаговом руководстве показано, как выполнять сборку и запускать шлюз Интернета вещей на плате Raspberry Pi 3, работающей под управлением ОС Raspbian Linux. Шлюз собран с использованием пакета SDK для шлюза IoT. Для сбора данных температуры в этом примере используется устройство Bluetooth с низким энергопотреблением (BLE) Texas Instruments SensorTag.

При запуске шлюз выполняет следующие действия:

* Подключается к устройству SensorTag с помощью протокола низкого энергопотребления Bluetooth (BLE).
* Подключается к Центру Интернета вещей с помощью протокола HTTP.
* Пересылает данные телеметрии с устройства SensorTag в центр IoT.
* Маршрутизирует команды из центра IoT на устройство SensorTag.

Шлюз содержит следующие модули:

* *Модуль BLE* , который взаимодействует с устройством BLE для получения от него данных температуры и отправки на него команд.
* *Модуль BLE "Из облака на устройство"*, который преобразовывает сообщения JSON, поступающие из облака, в инструкции BLE для *модуля BLE*.
* *Модуль ведения журнала*, который регистрирует в локальном файле все сообщения шлюза.
* *Модуль сопоставления удостоверений* , который преобразовывает MAC-адреса устройств BLE и удостоверения устройств центра Azure IoT.
* *Модуль Центра Интернета вещей* , который передает в Центр Интернета вещей данные телеметрии и получает из него команды устройств.
* *Модуль принтера BLE* интерпретирует данные телеметрии из устройства BLE и печатает форматированные данные в консоль, чтобы включить устранение неполадок и отладку.

### <a name="how-data-flows-through-the-gateway"></a>Как происходит поток данных через шлюз
На следующей блок-схеме показано, как работает конвейер отправки данных телеметрии.

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Элемент телеметрии на пути из устройства BLE в Центр Интернета вещей выполняет следующие шаги:

1. Устройство BLE создает пример данных температуры и отправляет его через Bluetooth на модуль BLE в шлюзе.
2. Модуль BLE получает этот пример и публикует его в брокер вместе с MAC-адресом устройства.
3. Модуль сопоставления удостоверений извлекает это сообщение и использует внутреннюю таблицу для преобразования MAC-адреса устройства в удостоверение устройства Центра Интернета вещей (идентификатор и ключ устройства). Затем он публикует новое сообщение, содержащее пример данных температуры, MAC-адрес, идентификатор и ключ устройства.
4. Модуль Центра Интернета вещей получает это новое сообщение (созданное модулем сопоставления удостоверений) и публикует его в Центре Интернета вещей.
5. Модуль ведения журнала регистрирует в локальном файле все сообщения из брокера.

На следующей блок-схеме показано, как работает конвейер обработки данных команд устройств.

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Модуль Центра Интернета вещей периодически опрашивает Центр Интернета вещей на наличие новых командных сообщений.
2. Когда модуль Центра Интернета вещей получает новое командное сообщение, он публикует его в брокер.
3. Модуль сопоставления удостоверений извлекает это командное сообщение и использует внутреннюю таблицу для преобразования идентификатора устройства Центра Интернета вещей в MAC-адрес устройства. Затем он публикует новое сообщение, содержащее MAC-адрес целевого устройства в схеме сопоставления свойств сообщения.
4. Модуль BLE "Из облака на устройство" извлекает это сообщение и преобразовывает его в правильную инструкцию BLE для модуля BLE. Затем он публикует новое сообщение.
5. Модуль BLE извлекает это сообщение и выполняет инструкцию ввода-вывода, взаимодействуя с устройством BLE.
6. Модуль ведения журнала регистрирует в журнале все сообщения из брокера в файл на диске.

## <a name="prepare-your-hardware"></a>Подготовка оборудования
В этом учебнике предполагается, что вы используете устройство [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html), подключенное к плате Raspberry Pi 3 под управлением ОС Raspbian.

### <a name="install-raspbian"></a>Установка Raspbian
Воспользуйтесь одним из приведенных ниже вариантов установки Raspbian на устройстве Raspberry Pi 3. 

* Установите последнюю версию Raspbian, используя [NOOBS][lnk-noobs], графический пользовательский интерфейс. 
* Вручную [скачайте][lnk-raspbian] и запишите последний образ операционной системы Raspbian на SD-карту. 

### <a name="install-bluez-537"></a>Установка BlueZ 5.37
Модули BLE взаимодействуют с оборудованием Bluetooth через стек BlueZ. Чтобы модули работали корректно, необходимо установить BlueZ версии 5.37. Эти инструкции помогут вам установить правильную версию BlueZ.

1. Остановите работу текущей управляющей программы Bluetooth:
   
    ```
    sudo systemctl stop bluetooth
    ```
2. Установите зависимости BlueZ: 
   
    ```
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```
3. Скачайте исходный код BlueZ с сайта bluez.org: 
   
    ```
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```
4. Распакуйте исходный код:
   
    ```
    tar -xvf bluez-5.37.tar.xz
    ```
5. Смените каталог на вновь созданную папку:
   
    ```
    cd bluez-5.37
    ```
6. Настройте код BlueZ для выполнения сборки:
   
    ```
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```
7. Выполните сборку BlueZ:
   
    ```
    make
    ```
8. По завершении сборки установите BlueZ:
   
    ```
    sudo make install
    ```
9. В файле `/lib/systemd/system/bluetooth.service` измените конфигурацию службы systemd для Bluetooth, чтобы она указывала на новую управляющую программу Bluetooth. Замените строку ExecStart следующим текстом: 
    
    ```
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Подключение устройства Raspberry Pi 3 к устройству SensorTag
Перед запуском примера необходимо убедиться, что плата Raspberry Pi 3 имеет возможность подключаться к устройству SensorTag.


1. Убедитесь, что служебная программа `rfkill` установлена:
   
    ```
    sudo apt-get install rfkill
    ```
2. Разблокируйте Bluetooth на устройстве Raspberry Pi 3 и убедитесь, что номер версии — **5.37**:
   
    ```
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```
3. Запустите службу Bluetooth и выполните команду **bluetoothctl**, чтобы войти в интерактивную оболочку Bluetooth: 
   
    ```
    sudo systemctl start bluetooth
    bluetoothctl
    ```
4. Введите команду **power on** , чтобы включить питание контроллера Bluetooth. Должен отобразиться примерно такой результат:
   
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```
5. Оставаясь в интерактивной оболочке Bluetooth, введите команду **scan on** для поиска устройств Bluetooth. Должен отобразиться примерно такой результат:
   
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```
6. Сделайте устройство SensorTag доступным для обнаружения, нажав маленькую кнопку (должен загореться зеленый светодиодный индикатор). Плата Raspberry Pi 3 должна обнаружить устройство SensorTag:
   
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
   
    В этом примере можно увидеть, что устройство SensorTag имеет MAC-адрес **A0:E6:F8:B5:F6:00**.
7. Отключите поиск, введя команду **scan off** .
   
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```
8. Подключитесь к устройству SensorTag с помощью его MAC-адреса, введя команду **connect \<MAC-адрес>**. Обратите внимание, что приведенный ниже пример выходных данных является сокращенным:
   
    ```
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
   
    > Обратите внимание, что список характеристик GATT устройства можно получить повторно, используя команду **list-attributes**.
9. Теперь можно отключиться от устройства с помощью команды **disconnect** и выйти из оболочки Bluetooth с помощью команды **quit**:
   
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Теперь все готово для запуска примера шлюза BLE на устройстве Raspberry Pi 3.

## <a name="run-the-ble-gateway-sample"></a>Запуск примера шлюза BLE
Чтобы запустить пример шлюза BLE, необходимо выполнить три задачи:

* Настроить два примера устройств в центре IoT.
* Выполнить сборку пакета SDK для шлюза Интернета вещей на устройстве Raspberry Pi 3.
* Настроить и запустить пример BLE на устройстве Raspberry Pi 3.

На момент написания данной статьи пакет SDK для шлюза Интернета вещей поддерживал только шлюзы, использующие модули BLE на ОС Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Настройка двух примеров устройств в центре IoT
* [Создайте Центр Интернета вещей][lnk-create-hub] в своей подписке Azure (для выполнения указаний данного пошагового руководства необходимо имя центра). Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.
* Добавьте в Центр Интернета вещей одно устройство с именем **SensorTag_01**, а затем запишите его идентификатор и ключ устройства. Чтобы добавить устройство в Центр Интернета вещей, созданный на предыдущем шаге, и получить его ключ, можно использовать такие инструменты, как [обозреватель устройств или iothub-explorer][lnk-explorer-tools]. Сопоставление этого устройства с устройством SensorTag будет выполнено при настройке шлюза.

### <a name="build-the-azure-iot-gateway-sdk-on-your-raspberry-pi-3"></a>Сборка пакета SDK для шлюза Azure IoT на устройстве Raspberry Pi 3

Установите зависимости пакета SDK для шлюза Azure IoT:

``` 
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```
Используйте следующие команды, чтобы клонировать пакет SDK для шлюза Интернета вещей и все его подмодули в корневой каталог:

```
cd ~
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
cd azure-iot-gateway-sdk
git submodule update --init --recursive
```

Когда репозиторий пакета SDK для шлюза Интернета вещей полностью скопирован на устройство Raspberry Pi 3, можно выполнить его сборку, используя следующую команду из папки, в которой содержится пакет SDK:

```
./tools/build.sh --skip-unittests
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Настройка и запуск примера BLE на устройстве Raspberry Pi 3
Для начальной загрузки и запуска примера необходимо настроить каждый модуль, который участвует в шлюзе. Необходимая конфигурация предоставляется в файле JSON, и требуется настроить все пять участвующих модулей. В репозитории есть пример JSON-файла с именем **gateway_sample.json**, на основе которого вы можете создать собственный файл конфигурации. Этот файл находится в папке **samples/ble_gateway/src** в локальной копии репозитория с пакетом SDK для шлюза Интернета вещей.

В следующих разделах описывается, как изменить этот файл конфигурации для примера BLE. Кроме того, предполагается, что репозиторий с пакетом SDK для шлюза Интернета вещей находится в папке **/home/pi/azure-iot-gateway-sdk/** на устройстве Raspberry Pi 3. Если репозиторий находится в другом месте, необходимо соответствующим образом изменить пути.

#### <a name="logger-configuration"></a>Конфигурация средства ведения журнала
Убедившись, что репозиторий шлюза находится в папке **/home/pi/azure-iot-gateway-sdk/**, настройте модуль ведения журнала следующим образом:

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
В примере конфигурации для устройства BLE предполагается, что используется устройство Texas Instruments SensorTag. Любое стандартное устройство BLE, которое может функционировать как периферийное устройство GATT, должно работать, но для этого необходимо обновить идентификаторы характеристик и данные GATT (для инструкций записи). Добавьте MAC-адрес устройства SensorTag: 

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
      "macAddress": "AA:BB:CC:DD:EE:FF",
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
Следующие параметры конфигурации гарантируют выполнение таких действий:

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

Для запуска примера передайте путь к файлу конфигурации JSON в двоичный файл **ble_gateway**. Если использовался файл **gateway_sample.json**, то команда будет выглядеть так, как показано ниже. Выполните эту команду из каталога azure-iot-gateway-sdk:

```
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Перед запуском примера может потребоваться нажать маленькую кнопку на устройстве SensorTag, чтобы сделать его доступным для обнаружения.

При запуске примера можно использовать такие инструменты, как [обозреватель устройств или iothub-explorer][lnk-explorer-tools], для мониторинга сообщений, которые шлюз перенаправляет с устройства SensorTag.

## <a name="send-cloud-to-device-messages"></a>Отправка сообщений из облака на устройство
Модуль BLE также поддерживает отправку инструкций из Центра Интернета вещей на устройство. Для отправки сообщений JSON, которые модуль шлюза BLE передает на устройство BLE, можно использовать [обозреватель устройств](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) или инструмент [iothub-explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer).
При использовании устройства Texas Instruments SensorTag можно включить красный или зеленый светодиодные индикаторы или звуковой сигнал, отправив команды из Центра Интернета вещей. Для этого необходимо сначала последовательно отправить два сообщения JSON, приведенные ниже. Затем можно будет отправить команды для включения светодиодных индикаторов или звукового сигнала.

1. Выполните сброс всех светодиодных индикаторов и звуковых сигналов (отключите их):
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```
2. Настройте операции ввода-вывода как "удаленные":
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Включить красный светодиодный индикатор:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Включить зеленый светодиодный индикатор:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```
* Включить звуковой сигнал:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите подробнее изучить возможности пакета SDK для шлюза Интернета вещей и поэкспериментировать с примерами кода, то см. следующие учебники и ресурсы для разработчиков:

* [Пакет SDK для шлюза Azure IoT][lnk-sdk]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Dec16_HO2-->


