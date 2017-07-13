---
title: "Приступая к работе с устройством SensorTag и шлюзом Azure IoT. Урок 3. Запуск примера приложения | Документация Майкрософт"
description: "Запустите пример приложения BLE для получения данных из BLE SensorTag и Центра Интернета вещей."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "приложение BLE, приложение мониторинга датчиков, сбор данных датчиков, данные датчиков, передача данных датчиков в облако"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: b33e53a1-1df7-4412-ade1-45185aec5bef
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a9f689c0b231af3cdf9257e2179bf86ff7bc9a31
ms.contentlocale: ru-ru
ms.lasthandoff: 01/25/2017

---
<a id="configure-and-run-a-ble-sample-application" class="xliff"></a>

# Настройка и запуск примера приложения BLE

<a id="what-you-will-do" class="xliff"></a>

## Выполняемая задача

- Клонируйте репозиторий. 
- Настройте подключение между SensorTag и Intel NUC. 
- Используйте Azure CLI для получения сведений о Центре Интернета вещей и SensorTag для примера приложения BLE (Bluetooth с низким энергопотреблением). Настройте и запустите пример приложения BLE. 

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-gateway-kit-c-troubleshooting.md).

<a id="what-you-will-learn" class="xliff"></a>

## Новые знания

В этой статье вы узнаете следующее:

- Как настроить и запустить пример приложения BLE.

<a id="what-you-need" class="xliff"></a>

## Необходимые элементы

Необходимо успешно выполнить инструкции, изложенные в следующих статьях:

- [Создание Центра Интернета вещей и регистрация SensorTag](iot-hub-gateway-kit-c-lesson2-register-device.md)

<a id="clone-the-sample-repository-to-the-host-computer" class="xliff"></a>

## Клонирование примера репозитория на главном компьютере

Чтобы клонировать пример репозитория на главном компьютере, сделайте следующее:

1. Откройте окно командной строки в Windows или терминала в macOS или Ubuntu.
2. Выполните следующие команды:

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

<a id="set-up-the-connectivity-between-sensortag-and-intel-nuc" class="xliff"></a>

## Настройте подключение между SensorTag и Intel NUC.

Для этого сделайте следующее на главном компьютере:

1. Запустите файл конфигурации, выполнив приведенную ниже команду.

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

2. Откройте файл `config-gateway.json` в Visual Studio Code, выполнив следующую команду:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

3. Найдите следующую строку кода и замените `[device hostname or IP address]` IP-адресом или именем Intel NUC.
   ![снимок экрана настройки шлюза](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

4. Установите вспомогательные инструменты на устройстве Intel NUC, выполнив следующую команду:

   ```bash
   gulp install-tools
   ```

5. Включите SensorTag, нажав кнопку питания, как на следующем рисунке. После этого зеленый светодиодный индикатор должен мигнуть.

   ![включение SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/turn on_off sensortag.jpg)

6. Отсканируйте устройства SensorTag, выполнив следующие команды:

   ```bash
   gulp discover-sensortag
   ```

7. Проверьте подключение между SensorTag и Intel NUC, выполнив следующую команду:

   ```bash
   gulp test-connectivity --mac {mac address}
   ```

   Замените `{mac address}` MAC-адресом, полученным на предыдущем шаге.

<a id="get-the-connection-string-of-sensortag" class="xliff"></a>

## Получение строки подключения SensorTag

Чтобы получить строку подключения Центра Интернета вещей Azure для SensorTag, выполните следующую команду на главном компьютере:

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` — это имя использованного Центра Интернета вещей. Используйте iot-gateway в качестве значения `{resource group name}`, а mydevice в качестве значения `{device id}`, если в уроке 2 значение не изменялось.

<a id="configure-the-ble-sample-application" class="xliff"></a>

## Настройка примера приложения BLE

Чтобы настроить и запустить пример приложения BLE, сделайте следующее на главном компьютере:

1. Откройте файл `config-sensortag.json` в Visual Studio Code, выполнив следующую команду:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![снимок экрана настройки SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/config_sensortag.png)

2. Выполните следующие замены в коде:
   - Замените `[IoT hub name]` именем использованного Центра Интернета вещей.
   - Замените `[IoT device connection string]` полученной строкой подключения SensorTag.
   - Замените `[device_mac_address]` полученным MAC-адресом SensorTag.

3. Запустите пример приложения BLE

   Чтобы запустить пример приложения BLE, сделайте следующее на главном компьютере:

   1. Включите SensorTag.

   2. Разверните и запустите пример приложения BLE на устройстве Intel NUC, выполнив следующую команду:
   
      ```bash
      gulp run
      ```

<a id="verify-that-the-ble-sample-application-works" class="xliff"></a>

## Проверка работы примера приложения BLE

Должны отобразиться подобные выходные данные:

![выходные данные примера приложения BLE](media/iot-hub-gateway-kit-lessons/lesson3/BLE_running.png)

Пример приложения выполняет сбор данных температуры и отправляет их в ваш Центр Интернета вещей. Пример приложения автоматически завершает работу через 40 секунд после отправки.

<a id="summary" class="xliff"></a>

## Сводка

Вы успешно настроили подключение между SensorTag и Intel NUC, а также запустили пример приложения BLE, который собирает и отправляет данные из SensorTag в Центр Интернета вещей. Теперь можно перейти к проверке получения данных в Центре Интернета вещей.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
[Чтение сообщений из Центра Интернета вещей](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)
