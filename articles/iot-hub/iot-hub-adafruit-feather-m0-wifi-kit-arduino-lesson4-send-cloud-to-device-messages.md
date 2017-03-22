---
title: "Подключение Arduino (C) к Интернету вещей Azure. Урок 4. Передача из облака на устройство | Документация Майкрософт"
description: "Пример приложения выполняется на устройстве Adafruit Feather M0 WiFi и отслеживает входящие сообщения от Центра Интернета вещей. Новая задача Gulp отправляет из Центра Интернета вещей сообщения на устройство Adafruit Feather M0 WiFi для управления состоянием индикатора."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "управление светодиодным индикатором с помощью Arduino с веб-интерфейса, управление светодиодным индикатором с помощью Arduino через веб-интерфейс"
ms.assetid: a0bf53fb-29fb-485f-ba4a-6c715057b1a2
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 4f9feb665ac93028023df86889ca53a64ac2ec0e
ms.openlocfilehash: 63113841ca836681232e0aa43b15b444f8bb92e2
ms.lasthandoff: 01/28/2017


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Запуск примера приложения для получения сообщений из облака на устройство
В этой статье на плате Adafruit Feather M0 WiFi Arduino развертывается пример приложения.

Пример приложения отслеживает входящие сообщения от Центра Интернета вещей. Также вы можете запустить на компьютере задачу Gulp для отправки сообщений из Центра Интернета вещей на плату Arduino. При получении сообщений пример приложения включает и отключает светодиодный индикатор. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-you-will-do"></a>Выполняемая задача
* Подключите пример приложения к Центру Интернета вещей.
* Развернете и запустите пример приложения.
* Отправка сообщения из Центра Интернета вещей на плату Arduino для включения и отключения светодиодного индикатора.

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:
* Как отслеживать входящие сообщения от Центра Интернета вещей.
* Как отправлять сообщения, передаваемые из облака на устройство, из Центра Интернета вещей на плату Arduino.

## <a name="what-you-need"></a>Необходимые элементы
* Плата Arduino, настроенная для работы. Чтобы узнать, как настроить плату Arduino, ознакомьтесь с разделом [Настройка устройства][configure-your-device].
* Центр Интернета вещей, созданный в вашей подписке Azure. Создание Центра Интернета вещей описано в статье [Создание Центра Интернета вещей и регистрация Intel Edison][create-your-azure-iot-hub].

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Подключение примера приложения к Центру Интернета вещей

1. Перейдите в папку репозитория `iot-hub-c-feather-m0-getting-started`.

   Откройте пример приложения в Visual Studio Code, выполнив следующие команды:

   ```bash
   cd Lesson4
   code .
   ```

   Обратите внимание на файл `app.ino`, созданный во вложенной папке `app`. Файл `app.ino` содержит основной код для отслеживания входящих сообщений из Центра Интернета вещей. Функция `blinkLED` включает и отключает светодиодный индикатор.

   ![Структура репозитория в примере приложения][repo-structure]

2. Получите последовательный порт устройства, используя интерфейс командной строки обнаружения устройств.

   ```bash
   devdisco list --usb
   ```

   Отобразятся следующие выходные данные, и вы сможете найти COM-порт вашей платы Arduino.

   ![Обнаружение устройства][device-discovery]

3. Откройте файл `config.json` в папке урока и введите значение найденного номера COM-порта.

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Для COM-порта на платформе Windows он имеет формат `COM1, COM2, ...`. На платформе MacOS или Ubuntu он будет начинаться с `/dev/`.

4. Запустите файл конфигурации, выполнив приведенную ниже команду.

   ```bash
   # For Windows command prompt
   npm install
   gulp init
   gulp install-tools
   ```

5. В файле `config-arduino.json` выполните следующие замены.

   Если вы выполнили действия, описанные в статье [Создание приложения-функции Azure и учетной записи хранения Azure][create-an-azure-function-app-and-storage-account], на этом компьютере, то все конфигурации наследуются, поэтому вы можете пропустить шаг с задачей развертывания и запуска примера приложения. Если вы выполнили действия, описанные в статье [Создание приложения-функции Azure и учетной записи хранения Azure][create-an-azure-function-app-and-storage-account], на другом компьютере, то необходимо заменить заполнители в файле `config-arduino.json`. Файл `config-arduino.json` находится во вложенной папке вашей домашней папки.

   ![Содержимое файла config-arduino.json][config-arduino-json]

   * Замените **[Wi-Fi SSID]** своим SSID для подключения Wi-Fi к Интернету.
   * Замените **[Wi-Fi password]** своим паролем Wi-Fi. Удалите строку, если для подключения к Wi-Fi не требуется пароль.
   * Замените **[IoT device connection string]** строкой подключения к устройству, которую можно получить с помощью команды `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Замените **[IoT hub connection string]** строкой подключения к Центру Интернета вещей, которую можно получить с помощью команды `az iot hub show-connection-string --name {my hub name}`.

## <a name="deploy-and-run-the-sample-application"></a>Развертывание и запуск примера приложения
Разверните и запустите пример приложения на плате Arduino, выполнив следующую команду.

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

Эта команда Gulp развертывает пример приложения на плате Arduino. Затем она запускает приложение на плате Arduino и отдельную задачу на главном компьютере, которая отправляет из Центра Интернета вещей 20 сообщений для включения и отключения светодиодного индикатора на плату Arduino.

Запущенное приложение начинает ожидать передачи сообщений от Центра Интернета вещей. Одновременно с этим задача Gulp отправляет из Центра Интернета вещей на плату Arduino несколько сообщений для включения и отключения светодиодного индикатора. При получении каждого такого сообщения платой пример приложения вызывает функцию `blinkLED` для включения и отключения светодиодного индикатора.

В результате этот индикатор должен мигать каждые две секунды, пока задача Gulp отправляет 20 сообщений из Центра Интернета вещей на плату Arduino. Последнее сообщение содержит команду stop, при получении которой приложение завершит работу.

![Пример приложения с командой Gulp и сообщениями для включения и отключения светодиодного индикатора][sample-application]

## <a name="summary"></a>Сводка
Итак, вы успешно отправили из Центра Интернета вещей на плату Arduino сообщения для включения и отключения светодиодного индикатора. Следующая задача является необязательной: изменение режима включения и отключения светодиодного индикатора.

## <a name="next-steps"></a>Дальнейшие действия
[Изменение режима включения и отключения светодиодного индикатора][change-the-on-and-off-led-behavior]


<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/repo_structure_arduino.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[create-an-azure-function-app-and-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/config-arduino.png
[sample-application]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_blink_arduino.png
[change-the-on-and-off-led-behavior]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
