---
title: "Приступая к работе с устройством SensorTag и шлюзом Azure IoT. Урок 4. Создание приложения-функции | Документация Майкрософт"
description: "Сохраняйте сообщения из Intel NUC в Центр Интернета вещей, записывайте их в Хранилище таблиц Azure, а затем читайте их из облака."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "хранение данных в облаке, данные, хранящиеся в облаке, облачная служба Интернета вещей"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: f84f9a85-e2c4-4a92-8969-f65eb34c194e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 3672804218dbbe3a545536b8c4baccb2b9171cdc
ms.lasthandoff: 01/25/2017


---
# <a name="create-an-azure-function-app-and-storage-account"></a>Создание приложения-функции Azure и учетной записи хранения Azure

Функции Azure — это решение для быстрого запуска _функций_ (фрагментов кода) в облаке. Выполнение функций в Azure производится с помощью приложения-функции. 

## <a name="what-you-will-do"></a>Выполняемая задача

- С помощью шаблона Azure Resource Manager создайте приложение-функцию Azure и учетную запись хранения Azure. Приложение-функция Azure ожидает передачи событий Центра Интернета вещей Azure, обрабатывает входящие сообщения и записывает их в хранилище таблиц Azure.

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-gateway-kit-c-troubleshooting.md).


## <a name="what-you-will-learn"></a>Новые знания

Из этого урока вы узнаете:

- как использовать Azure Resource Manager для развертывания ресурсов в Azure;
- как использовать приложение-функцию Azure для обработки сообщений Центра Интернета вещей и их записи в таблицу в Хранилище таблиц Azure.

## <a name="what-you-need"></a>Необходимые элементы

Необходимо успешно пройти следующие уроки:

- [Урок 1. Настройка Intel NUC в качестве шлюза Интернета вещей](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
- [Урок 2. Подготовка главного компьютера и Центра Интернета вещей Azure](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
- [Урок 3. Получение сообщений из SensorTag и чтение сообщений из Центра Интернета вещей](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

## <a name="open-a-sample-app"></a>Открытие примера приложения

Перейдите в папку репозитория `iot-hub-c-intel-nuc-gateway-getting-started`, выполните инициализацию файлов конфигурации, а затем откройте пример проекта в Visual Studio Code, выполнив следующую команду:

```bash
cd Lesson4
npm install
gulp init
code .
```

![структура репозитория](media/iot-hub-gateway-kit-lessons/lesson4/arm_template.png)

- Файл `arm-template.json` — шаблон Azure Resource Manager, содержащий приложение-функцию Azure и учетную запись хранения Azure.
- Файл `arm-template-param.json` — файл конфигурации, используемый в шаблоне Azure Resource Manager.
- Вложенная папка `ReceiveDeviceMessages` содержит код Node.js для функции Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Настройка шаблонов Azure Resource Manager и создание ресурсов в Azure

Обновите файл `arm-template-param.json` в Visual Studio Code.

![JSON-файл шаблона ARM](media/iot-hub-gateway-kit-lessons/lesson4/arm_template_param.png)

- Замените `[your IoT Hub name]` именем `{my hub name}`, указанным в уроке 2.

После обновления файла `arm-template-param.json` разверните ресурсы в Azure, выполнив следующую команду:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-gateway
```

Используйте `iot-gateway` в качестве значения `{resource group name}`, если в уроке 2 значение не изменялось.

## <a name="summary"></a>Сводка

Вы создали приложение-функцию Azure для обработки сообщений Центра Интернета вещей и учетную запись хранения Azure для хранения этих сообщений. Теперь можно читать сообщения, отправленные шлюзом в Центр Интернета вещей.

## <a name="next-steps"></a>Дальнейшие действия
[Чтение сообщений, сохраненных в службе хранилища Azure](iot-hub-gateway-kit-c-lesson4-read-table-storage.md)

