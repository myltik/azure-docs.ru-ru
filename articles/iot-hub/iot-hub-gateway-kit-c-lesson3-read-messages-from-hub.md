---
title: "Приступая к работе с устройством SensorTag и шлюзом Azure IoT. Урок 3. Чтение сообщений | Документация Майкрософт"
description: "Запустите пример кода на главном компьютере для чтения сообщений из Центра Интернета вещей."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "данные в облаке, коллекция облачных данных, облачная служба Интернета вещей, данные Интернета вещей"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: cc88be24-b5c0-4ef2-ba21-4e8f77f3e167
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 0a5dd7e010ba46b724bb6a2526dc2a2730acc10f
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-from-your-iot-hub"></a>Чтение сообщений из Центра Интернета вещей

## <a name="what-you-will-do"></a>Выполняемая задача

- Запустите пример кода на главном компьютере для чтения сообщений из Центра Интернета вещей.

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания

Чтение сообщений из Центра Интернета вещей с использованием инструмента Gulp.

## <a name="what-you-need"></a>Необходимые элементы

- Пример приложения BLE, успешно запущенный на уроке 3.

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Получение строк подключения Центра Интернета вещей и устройства

Ваше устройство (TI SensorTag или имитация устройства) использует строку подключения для подключения к Центру Интернета вещей. Строка подключения Центра Интернета вещей используется для подключения к реестру удостоверений в Центре Интернета вещей, чтобы управлять устройствами, которым разрешено подключаться к этому центру.

- Для вывода списка всех Центров Интернета вещей в группе ресурсов выполните следующую команду:

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   Используйте `iot-gateway` в качестве значения `{resource group name}`, если вы не меняли это значение.
- Для получения строки подключения Центра Интернета вещей выполните следующую команду:

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` — это имя, указанное в уроке 2.

## <a name="configure-the-device-connection-for-the-sample-code"></a>Настройка подключения устройства для примера кода

Обновите файл конфигурации устройства `config-azure.json` для чтения сообщений из Центра Интернета вещей на главном компьютере. Для этого выполните следующие действия.

1. Откройте файл `config-azure.json` в Visual Studio Code, выполнив следующую команду в окне консоли:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. В файле `config-azure.json` выполните следующие замены.

   ![снимок экрана настройки Azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Замените `[IoT hub connection string]` полученной строкой подключения Центра Интернета вещей.

## <a name="read-messages-from-your-iot-hub"></a>Чтение сообщений из Центра Интернета вещей

Если у вас есть TI SensorTag, убедитесь, что устройство SensorTag включено. Запустите пример приложения шлюза и прочтите сообщения Центра Интернета вещей, выполнив следующую команду:

```bash
gulp run --iot-hub
```

Команда запускает пример приложения BLE, которое считывает данные температуры с SensorTag или имитации устройства и каждые 2 секунды отправляет сообщение в Центр Интернета вещей. Она также создает дочерний процесс получения сообщения.

Все отправленные и полученные сообщения мгновенно появляются в том же окне консоли на главном компьютере. Экземпляр примера приложения прекращает свое действие автоматически через 40 секунд.

![Пример приложения BLE с отправленными и полученными сообщениями](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub.png)

## <a name="summary"></a>Сводка

Вы запустили пример кода для чтения сообщений из Центра Интернета вещей. Теперь можно приступить к чтению сообщений, которые хранятся в Хранилище таблиц Azure.

## <a name="next-steps"></a>Дальнейшие действия
[Создание учетной записи хранения Azure и приложения-функции Azure](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)



