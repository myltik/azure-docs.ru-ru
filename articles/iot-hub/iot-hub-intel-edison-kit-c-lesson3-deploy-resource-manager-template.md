---
title: "Подключение Intel Edison (C) к Интернету вещей Azure. Урок 3. Создание приложения-функции | Документация Майкрософт"
description: "Приложение-функция Azure ожидает передачи событий Центра Интернета вещей Azure, обрабатывает входящие сообщения и записывает их в хранилище таблиц Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "хранение данных в облаке, данные, хранящиеся в облаке, облачная служба Интернета вещей"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 739b82e9-5d4e-4485-8971-f57cbb682faf
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 2c2f540a0000851fc5ea50ef28f559d19fc8357f
ms.lasthandoff: 01/25/2017


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Создание приложения-функции Azure и учетной записи хранения Azure
[Функции Azure](../../articles/azure-functions/functions-overview.md) — это решение для быстрого запуска *функций* (фрагментов кода) в облаке. Выполнение функций в Azure производится с помощью приложения-функции.

## <a name="what-will-you-do"></a>Ваши действия
С помощью шаблона Azure Resource Manager создайте приложение-функцию Azure и учетную запись хранения Azure. Приложение-функция Azure ожидает передачи событий Центра Интернета вещей Azure, обрабатывает входящие сообщения и записывает их в хранилище таблиц Azure. Учетная запись хранения используется для чтения сохраненных копий сообщений из таблицы Azure. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-will-you-learn"></a>Новые знания
В этой статье вы узнаете следующее:
* Использование [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) для развертывания ресурсов в Azure.
* Использование приложения-функции Azure для обработки сообщений Центра Интернета вещей и их записи в таблицу в хранилище таблиц Azure.

## <a name="what-do-you-need"></a>Требования
Необходимо успешно выполнить инструкции, изложенные в следующих статьях:
- [Начало работы с Intel Edison (Node.js)][get-started-with-your-intel-edison]
- [Create your Azure IoT hub][create-your-azure-iot-hub] (Создание Центра Интернета вещей Azure)

## <a name="open-the-sample-app"></a>Открытие примера приложения
Откройте пример проекта в Visual Studio Code, выполнив следующие команды:

```bash
cd Lesson3
code .
```

![Структура репозитория][repo-structure]

* Файл во вложенной папке `app` — ключевой файл исходного кода. Этот исходный файл содержит код для отправки сообщения 20 раз в центр Интернета вещей и мигания светодиода с каждым отправляемым сообщением.
* Файл `arm-template.json` — шаблон Azure Resource Manager, содержащий приложение-функцию Azure и учетную запись хранения Azure.
* Файл `arm-template-param.json` — файл конфигурации, используемый в шаблоне Azure Resource Manager.
* Вложенная папка `ReceiveDeviceMessages` содержит код Node.js для функции Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Настройка шаблонов Azure Resource Manager и создание ресурсов в Azure
Обновите файл `arm-template-param.json` в Visual Studio Code.

![Параметры шаблона Azure Resource Manager][arm-template-parameters]

* Замените **[your IoT Hub name]** своим **{именем центра}**, которое вы указали при [создании Центра Интернета вещей и регистрации Intel Edison][created-your-iot-hub-and-registered-intel-edison].
* Замените **[строку-префикс для новых ресурсов]** любым префиксом по вашему усмотрению. Префикс обеспечивает глобальную уникальность имени ресурса во избежание конфликта. Префикс не должен начинаться с дефиса или цифры.

После обновления файла `arm-template-param.json` разверните ресурсы в Azure, выполнив следующую команду:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Создание этих ресурсов занимает около пяти минут. Пока ресурсы создаются, можно перейти к следующей статье.

## <a name="summary"></a>Сводка
Вы создали приложение-функцию Azure для обработки сообщений Центра Интернета вещей и учетную запись хранения Azure для хранения этих сообщений. Теперь можно развернуть и запустить на устройстве Edison пример приложения для отправки сообщений c устройства в облако.

## <a name="next-steps"></a>Дальнейшие действия
[Run a sample application to send device-to-cloud messages][send-device-to-cloud-messages] (Запуск примера приложения для отправки сообщений с устройства в облако).
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[get-started-with-your-intel-edison]: iot-hub-intel-edison-kit-c-get-started.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-c-get-started.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson3/repo_structure_c.png
[arm-template-parameters]: /media/iot-hub-intel-edison-lessons/lesson3/arm_para_c.png
[created-your-iot-hub-and-registered-intel-edison]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-c-lesson3-run-azure-blink.md
