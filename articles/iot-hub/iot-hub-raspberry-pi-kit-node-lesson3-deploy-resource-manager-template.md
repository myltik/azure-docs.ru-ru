---
title: "Подключение Raspberry Pi (Node) к Интернету вещей Azure. Урок 3. Развертывание шаблона | Документация Майкрософт"
description: "Приложение-функция Azure ожидает передачи событий Центра Интернета вещей Azure, обрабатывает входящие сообщения и записывает их в хранилище таблиц Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "хранение данных в облаке, данные, хранящиеся в облаке, облачная служба Интернета вещей"
ms.assetid: 6c58de85-c5c4-4989-bb5e-08c45c549966
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a3a7ec4c81556e4cb530f32c9997d8701db68b2c


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Создание приложения-функции Azure и учетной записи хранения Azure
[Функции Azure](../azure-functions/functions-overview.md) — это решение для быстрого запуска *функций* (фрагментов кода) в облаке. Выполнение функций в Azure производится с помощью приложения-функции.

## <a name="what-you-will-do"></a>Выполняемая задача
С помощью шаблона Azure Resource Manager создайте приложение-функцию Azure и учетную запись хранения Azure. Приложение-функция Azure ожидает передачи событий Центра Интернета вещей Azure, обрабатывает входящие сообщения и записывает их в хранилище таблиц Azure. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:

* Использование [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) для развертывания ресурсов в Azure.
* Использование приложения-функции Azure для обработки сообщений Центра Интернета вещей и их записи в таблицу в хранилище таблиц Azure.

## <a name="what-you-need"></a>Необходимые элементы
Необходимо успешно выполнить инструкции, изложенные в следующих статьях:
* [Get started with Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md) (Приступая к работе с Raspberry Pi 3)
* [Create your Azure IoT hub](iot-hub-raspberry-pi-kit-node-get-started.md) (Создание Центра Интернета вещей Azure)

## <a name="open-the-sample-app"></a>Открытие примера приложения
Откройте пример проекта в Visual Studio Code, выполнив следующие команды:

```bash
cd Lesson3
code .
```

![Структура репозитория](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

* Файл `app.js` во вложенной папке `app` — ключевой файл исходного кода. Этот исходный файл содержит код для отправки сообщения 20 раз в центр Интернета вещей и мигания светодиода с каждым отправляемым сообщением.
* Файл `arm-template.json` — шаблон Azure Resource Manager, содержащий приложение-функцию Azure и учетную запись хранения Azure.
* Файл `arm-template-param.json` — файл конфигурации, используемый в шаблоне Azure Resource Manager.
* Вложенная папка `ReceiveDeviceMessages` содержит код Node.js для функции Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Настройка шаблонов Azure Resource Manager и создание ресурсов в Azure
Обновите файл `arm-template-param.json` в Visual Studio Code.

![Параметры шаблона Azure Resource Manager](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

* Замените **[your IoT Hub name]** своим **{именем центра}**, которое вы указали при [создании Центра Интернета вещей и регистрации Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).
* Замените **[строку-префикс для новых ресурсов]** любым префиксом по вашему усмотрению. Префикс обеспечивает глобальную уникальность имени ресурса во избежание конфликта. Префикс не должен начинаться с дефиса или цифры.

После обновления файла `arm-template-param.json` разверните ресурсы в Azure, выполнив следующую команду:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Создание этих ресурсов занимает около пяти минут. Пока ресурсы создаются, можно перейти к следующей статье.

## <a name="summary"></a>Сводка
Вы создали приложение-функцию Azure для обработки сообщений Центра Интернета вещей и учетную запись хранения Azure для хранения этих сообщений. Теперь можно развернуть и запустить на устройстве Pi пример приложения для отправки сообщений c устройства в облако.

## <a name="next-steps"></a>Дальнейшие действия
[Run a sample application to send device-to-cloud messages on Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md) (Запуск примера приложения для отправки сообщений с устройства в облако на Raspberry Pi 3)




<!--HONumber=Jan17_HO4-->


