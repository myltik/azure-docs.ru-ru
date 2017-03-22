---
title: "Подключение Arduino (C) к Интернету вещей Azure. Урок 3. Хранилище таблиц | Документация Майкрософт"
description: "Отслеживайте сообщения, передаваемые с устройства в облако, по мере их записывания в хранилище таблиц Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "данные в облаке, коллекция облачных данных, облачная служба Интернета вещей, данные Интернета вещей"
ms.assetid: 386083e0-0dbb-48c0-9ac2-4f8fb4590772
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 7a49a15b00bf25c9416235f41da8cc86e54d0062
ms.lasthandoff: 01/24/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>Чтение сообщений, сохраненных в службе хранилища Azure
## <a name="what-you-will-do"></a>Выполняемая задача
Отслеживание сообщений, передаваемых с устройства в облако (то есть с платы Adafruit Feather M0 WiFi Arduino в центр Интернета вещей), по мере их записывания в Хранилище таблиц Azure.

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете, как использовать задачу gulp read-message для чтения сообщений, сохраненных в хранилище таблиц Azure.

## <a name="what-you-need"></a>Необходимые элементы
Прежде чем начинать, необходимо успешно выполнить инструкции статьи, посвященной [запуску примера приложения Azure для включения индикатора на плате Arduino][run-blink-application].

## <a name="read-new-messages-from-your-storage-account"></a>Чтение новых сообщений из учетной записи хранения
В предыдущей статье вы запустили пример приложения на плате Arduino. Пример приложения отправлял сообщения в центр Интернета вещей Azure. Сообщения, отправленные в центр Интернета вещей, сохраняются в хранилище таблиц Azure с помощью приложения-функции Azure. Для чтения сообщений из хранилища таблиц Azure потребуется строка подключения к хранилищу Azure.

Чтобы прочитать сообщения, сохраненные в хранилище таблиц Azure, выполните следующие действия:

1. Получите строку подключения, выполнив следующую команду:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   Первая команда получает имя `storage name`, которое используется во второй команде для получения строки подключения. Используйте `iot-sample` в качестве значения `{resource group name}`, если вы не меняли это значение.
2. Откройте файл конфигурации `config-arduino.json` в Visual Studio Code, выполнив следующую команду.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```
3. Замените `[Azure storage connection string]` строкой подключения, полученной на шаге 1.
4. Сохраните файл `config-arduino.json`.
5. Отправьте сообщения еще раз и считайте их из хранилища таблиц Azure, выполнив следующую команду.

   ```bash
   gulp run --read-storage

   # You can monitor the serial port by running listen task:
   gulp listen

   # Or you can combine above two gulp tasks into one:
   gulp run --read-storage --listen
   ```

   Логика чтения из хранилища таблиц Azure содержится в файле `azure-table.js`.

   ![gulp run --read-storage][gulp-run]

## <a name="summary"></a>Сводка
Вы успешно подключили плату Arduino к центру Интернета вещей в облаке и отправили сообщения с устройства в облако с помощью примера приложения для включения индикатора. Также вы использовали приложение-функцию Azure для сохранения входящих сообщений из Центра Интернета вещей в хранилище таблиц Azure. Теперь можно перейти к отправке сообщений из облака на устройство, т. е. из Центра Интернета вещей на плату Arduino.

## <a name="next-steps"></a>Дальнейшие действия
[Отправка сообщений из облака на устройство][send-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[run-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[gulp-run]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_read_message_arduino.png
[send-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
