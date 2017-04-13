---
title: "Подключение Arduino (C) к Интернету вещей Azure. Урок 3. Запуск примера | Документация Майкрософт"
description: "Разверните и запустите на плате Adafruit Feather M0 WiFi пример приложения, которое отправляет сообщения в Центр Интернета вещей и заставляет светодиодный индикатор мигать."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "облачная служба Интернета вещей, отправка данных в облако с помощью Arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 92cce319-2b17-4c9b-889d-deac959e3e7c
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 631a6677e4801b1c3475b9e8f2726219dd101132
ms.lasthandoff: 01/24/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Запуск примера приложения для отправки сообщений с устройства в облако
## <a name="what-you-will-do"></a>Выполняемая задача
В этой статье показано, как развернуть и запустить на плате Adafruit Feather M0 WiFi Arduino пример приложения, которое отправляет сообщения в Центр Интернета вещей.

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-you-will-learn"></a>Новые знания
Вы узнаете, как с помощью инструмента Gulp развернуть и запустить пример приложения Arduino на плате Arduino.

## <a name="what-you-need"></a>Необходимые элементы
* К выполнению этой задачи следует приступать только после выполнения действий, описанных в статье [Создание приложения-функции Azure и учетной записи хранения Azure][process-and-store-iot-hub-messages].

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Получение строк подключения Центра Интернета вещей и устройства
Строка подключения устройства используется для подключения платы Arduino к Центру Интернета вещей. Строка подключения Центра Интернета вещей используется для его подключения к удостоверению устройства, которое представляет плату Arduino в Центре Интернета вещей.

* Для вывода списка всех Центров Интернета вещей в группе ресурсов выполните следующую команду Azure CLI:

```bash
az iot hub list -g iot-sample --query [].name
```

Используйте `iot-sample` в качестве значения `{resource group name}`, если вы не меняли это значение.

* Для получения строки подключения Центра Интернета вещей выполните следующую команду интерфейса командной строки Azure:

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` — это имя, указанное при создании Центра Интернета вещей и регистрации платы Arduinoi.

* Для получения строки подключения устройства выполните следующую команду:

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id mym0wifi
```

Используйте `mym0wifi` в качестве значения `{device id}`, если вы не меняли это значение.
## <a name="configure-the-device-connection"></a>Настройка подключения устройства
Чтобы настроить подключение устройства, выполните следующие действия.

1. Получите последовательный порт устройства, используя интерфейс командной строки обнаружения устройств:

   ```bash
   devdisco list --usb
   ```

   Отобразятся следующие выходные данные, и вы сможете найти COM-порт вашей платы Arduino.

   ![Обнаружение устройства][device-discovery]

2. Откройте файл `config.json` в папке занятия и добавьте значение найденного номера COM-порта:

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Для COM-порта на платформе Windows он имеет формат `COM1, COM2, ...`. На macOS или Ubuntu он начинается с `/dev/`.

3. Запустите файл конфигурации, выполнив приведенную ниже команду.

   ```bash
   npm install
   gulp init
   gulp install-tools
   ```
4. Откройте файл конфигурации устройства `config-arduino.json` в Visual Studio Code, выполнив приведенную ниже команду.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```

   ![config-arduino.json][config-arduino-json]

5. В файле `config-arduino.json` выполните следующие замены.

   * Замените **[Wi-Fi SSID]** своим SSID для Wi-Fi, подключенным к Интернету.
   * Замените **[Wi-Fi password]** своим паролем Wi-Fi. Удалите строку, если для подключения к Wi-Fi не требуется пароль.
   * Замените **[строка подключения устройства Интернета вещей]** на полученное значение `device connection string`.
   * Замените **[IoT hub connection string]** (строка подключения Центра Интернета вещей) полученным значением `iot hub connection string`.

   > [!NOTE]
   > В этой статье не требуется `azure_storage_connection_string`. Оставьте эту настройку без изменений.

## <a name="deploy-and-run-the-sample-application"></a>Развертывание и запуск примера приложения
Разверните и запустите пример приложения на плате Arduino, выполнив следующую команду:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

> [!NOTE]
> Задача инструмента Gulp по умолчанию последовательно запускает задачи `install-tools` и `run`. [Развернув приложение для включения индикатора][deployed-the-blink-app], выполните эти задачи отдельно.

## <a name="verify-that-the-sample-application-works"></a>Проверка работы примера приложения
Встроенный светодиодный индикатор GPIO #0 должен мигать каждые две секунды. Каждый раз, когда индикатор мигает, пример приложения отправляет сообщение в Центр Интернета вещей и проверяет, что сообщение успешно отправлено. Кроме того, каждое сообщение, полученное Центром Интернета вещей, выводится в окне консоли. Пример приложения автоматически завершает работу после отправки 20 сообщений.

![Пример приложения с отправленными и полученными сообщениями][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>Сводка
Вы развернули и запустили новый пример приложения для включения светодиодного индикатора на плате Arduino для отправки сообщений с устройства в облако, в котором находится Центр Интернета вещей. Теперь можно отслеживать сообщения по мере их записывания в учетную запись хранения.

## <a name="next-steps"></a>Дальнейшие действия
[Чтение сообщений, сохраненных в службе хранилища Azure][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/config-arduino.png
[deployed-the-blink-app]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_run_arduino.png
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
