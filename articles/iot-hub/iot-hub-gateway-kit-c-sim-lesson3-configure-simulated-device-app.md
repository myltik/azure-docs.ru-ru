---
title: "Приступая к работе с имитацией устройства и шлюзом Azure IoT. Урок 3. Запуск примера приложения | Документация Майкрософт"
description: "Запуск примера приложения для имитации устройства для отправки данных в Центр Интернета вещей"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "отправка данных в облако"
ms.assetid: 5d051d99-9749-4150-b3c8-573b0bda9c52
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 873f3cef8cb1d115f77f0d3fa2c4b50391f66d91
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-simulated-device-sample-app"></a>Настройка и запуск примера приложения для имитации устройства

## <a name="what-you-will-do"></a>Выполняемая задача

- Клонируйте репозиторий.
- Чтобы получить сведения о Центре Интернета вещей и логическом устройстве примера приложения для имитации устройства, используйте Azure CLI. Затем настройте и запустите пример приложения для имитации устройства.

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания

В этой статье вы узнаете следующее:

- Как настроить и запустить пример приложения для имитации устройства.

## <a name="what-you-need"></a>Необходимые элементы

Необходимо успешно выполнить инструкции, изложенные в следующих статьях:

- [Создание Центра Интернета вещей и регистрация устройства](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>Клонирование примера репозитория на главном компьютере

Чтобы клонировать пример репозитория на главном компьютере, сделайте следующее:

1. Откройте командную строку в Windows или окно терминала в МacOS или Ubuntu.
2. Выполните следующие команды:

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="configure-the-simulated-device-and-your-nuc"></a>Настройка имитации устройства и NUC

1. Откройте файл конфигурации `config.json` в Visual Studio Code, выполнив следующую команду.

   ```bash
   code config.json
   ```

2. Замените `"has_sensortag": true` на `"has_sensortag": false`:

   ![конфигурация, нет устройства TI SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/config_no_sensortag.png)

3. Запустите файл конфигурации, выполнив приведенную ниже команду.

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

4. Откройте файл `config-gateway.json` в Visual Studio Code, выполнив следующую команду:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

5. Найдите следующую строку кода и замените `[device hostname or IP address]` IP-адресом или именем Intel NUC.
   ![снимок экрана настройки шлюза](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

## <a name="get-the-connection-string-of-your-iot-hub-logical-device"></a>Получение строки подключения логического устройства Центра Интернета вещей

Чтобы получить строку подключения Центра Интернета вещей Azure для логического устройства, выполните следующую команду на главном компьютере:

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` — это имя использованного Центра Интернета вещей. Используйте iot-gateway в качестве значения `{resource group name}`, а mydevice в качестве значения `{device id}`, если в уроке 2 значение не изменялось.

## <a name="configure-the-simulated-device-cloud-upload-sample-application"></a>Настройка примера приложения отправки в облако для имитации устройства

Чтобы настроить и запустить пример приложения отправки в облако для имитации устройства, сделайте следующее на главном компьютере:

1. Откройте файл `config-sensortag.json` в Visual Studio Code, выполнив следующую команду:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![снимок экрана настройки SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/config_simulated_device.png)

2. Выполните следующие замены в коде:
   - Замените `[IoT hub name]` именем Центра Интернета вещей.
   - Замените `[IoT device connection string]` строкой подключения логического устройства Центра Интернета вещей.

3. Запустите приложение.

   Разверните и запустите приложение, выполнив следующую команду.

   ```bash
   gulp run
   ```

## <a name="verify-the-sample-application-works"></a>Проверка работы примера приложения

Должны отобразиться подобные выходные данные:

![выходные данные примера приложения для имитации устройства](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_simudev.png)

Приложение отправляет данные о температуре в Центр Интернета вещей в течение 40 секунд.

## <a name="summary"></a>Сводка

Вы успешно настроили и запустили пример приложения отправки в облако для имитации устройства, которое отправляет данные в Центр Интернета вещей с помощью имитации устройства.

## <a name="next-steps"></a>Дальнейшие действия
[Чтение сообщений из Центра Интернета вещей](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)
