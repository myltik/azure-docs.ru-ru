---
title: "Создание приложения-функции Azure и учетной записи хранения | Документация Майкрософт"
description: "Приложение-функция Azure ожидает передачи событий Центра Интернета вещей Azure, обрабатывает входящие сообщения и записывает их в хранилище таблиц Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "хранение данных в облаке, данные, хранящиеся в облаке, облачная служба Интернета вещей"
ms.assetid: 9c8f4cd1-9511-4601-ad7e-51761a986753
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 946d66cc7ed1173e7b2b92dae90a1ce69ba3c165
ms.openlocfilehash: 18806dc515a9a8951134d580a2c0664ff503e17c


---

# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Создание приложения-функции Azure и учетной записи хранения Azure
[Функции Azure](../../articles/azure-functions/functions-overview.md) — это решение для быстрого запуска *функций* (фрагментов кода) в облаке. Выполнение функций в Azure производится с помощью приложения-функции.

## <a name="what-will-you-do"></a>Ваши действия
С помощью шаблона Azure Resource Manager создайте приложение-функцию Azure и учетную запись хранения Azure. Приложение-функция Azure ожидает передачи событий Центра Интернета вещей Azure, обрабатывает входящие сообщения и записывает их в хранилище таблиц Azure.

Если возникнут какие-либо проблемы в работе платы Adafruit Feather M0 WiFi Arduino, решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-will-you-learn"></a>Новые знания
В этой статье вы узнаете следующее:
* Использование [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) для развертывания ресурсов в Azure.
* Использование приложения-функции Azure для обработки сообщений Центра Интернета вещей и их записи в таблицу в хранилище таблиц Azure.

## <a name="what-do-you-need"></a>Требования
Необходимо успешно выполнить инструкции, изложенные в следующих статьях:
- [Приступая к работе с платой Arduino: Adafruit Feather M0 Wi-Fi][get-started]
- [Создание Центра Интернета вещей Azure и регистрация платы Adafruit Feather M0 WiFi][create-iot-hub]

## <a name="open-the-sample-app"></a>Открытие примера приложения
Откройте пример проекта в Visual Studio Code, выполнив следующие команды:

```bash
cd Lesson3
code .
```

![Структура репозитория][repo-structure]

* Файл `app.ino` во вложенной папке `app` — ключевой файл исходного кода. Этот исходный файл содержит код для отправки сообщения 20 раз в центр Интернета вещей и мигания светодиода с каждым отправляемым сообщением.
* `config.json` содержит обязательные параметры конфигурации.
* Файл `arm-template.json` — шаблон Azure Resource Manager, содержащий приложение-функцию Azure и учетную запись хранения Azure.
* Файл `arm-template-param.json` — файл конфигурации, используемый в шаблоне Azure Resource Manager.
* Вложенная папка `ReceiveDeviceMessages` содержит код Node.js для функции Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Настройка шаблонов Azure Resource Manager и создание ресурсов в Azure
Обновите файл `arm-template-param.json` в Visual Studio Code.

![Параметры шаблона Azure Resource Manager][arm-template-params]

* Замените **[your IoT Hub name]** своим именем Центра Интернета вещей (это значение **{my hub name}**, которое вы указали при [создании Центра Интернета вещей и регистрации платы Arduino][created-iot-hub-and-registered-arduino-board]).
* Замените **[строку-префикс для новых ресурсов]** любым префиксом по вашему усмотрению. Префикс обеспечивает глобальную уникальность имени ресурса во избежание конфликта. Префикс не должен начинаться с дефиса или цифры.

После обновления файла `arm-template-param.json` разверните ресурсы в Azure, выполнив следующую команду:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Создание этих ресурсов занимает около пяти минут. Пока ресурсы создаются, можно перейти к следующей статье.

## <a name="summary"></a>Сводка
Вы создали приложение-функцию Azure для обработки сообщений Центра Интернета вещей и учетную запись хранения Azure для хранения этих сообщений. Теперь можно развернуть и запустить на плате Arduino пример приложения для отправки сообщений c устройства в облако.

## <a name="next-steps"></a>Дальнейшие действия
[Запуск примера приложения для отправки сообщений с устройства в облако][send-device-to-cloud-messages].

<!-- Images and links -->

[get-started]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[create-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/repo_structure_c.png
[arm-template-params]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/arm_para_arduino.png
[created-iot-hub-and-registered-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md


<!--HONumber=Dec16_HO2-->


