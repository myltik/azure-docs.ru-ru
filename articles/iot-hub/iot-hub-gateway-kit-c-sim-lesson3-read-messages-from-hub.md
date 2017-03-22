---
title: "Приступая к работе с имитацией устройства и шлюзом Azure IoT. Урок 3. Чтение сообщений | Документация Майкрософт"
description: "Запустите пример кода на главном компьютере для чтения сообщений из Центра Интернета вещей."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "данные в облаке, коллекция облачных данных, облачная служба Интернета вещей, данные Интернета вещей"
ms.assetid: 5a6ec9c1-d83c-41c1-beaf-7c0d3395d77f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 08ccd35c6e6a8e77f0fd4637f14a1f27730560d6
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-from-your-iot-hub"></a>Чтение сообщений из Центра Интернета вещей

## <a name="what-you-will-do"></a>Выполняемая задача

- Запустите пример кода на главном компьютере для чтения сообщений из Центра Интернета вещей.

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания

Чтение сообщений из Центра Интернета вещей с использованием инструмента Gulp.

## <a name="what-you-need"></a>Необходимые элементы

- Пример имитации устройства можно найти в статье [Configure and run a simulated device cloud upload sample application](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md) (Настройка и запуск примера приложения отправки в облако для имитации устройства).

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Получение строк подключения Центра Интернета вещей и устройства

Строка подключения устройства используется имитацией устройства для подключения к Центру Интернета вещей. Строка подключения Центра Интернета вещей используется для подключения к реестру удостоверений в Центре Интернета вещей, чтобы управлять устройствами, которым разрешено подключаться к этому центру.

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

Обновите Центр Интернета вещей и конфигурации подключения устройства в `config-azure.json`, сделав следующее:

1. Откройте файл `config-azure.json` в Visual Studio Code, выполнив следующую команду в окне консоли:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. В файле `config-azure.json` выполните следующие замены.

   ![снимок экрана настройки Azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Замените `[IoT hub connection string]` строкой подключения Центра Интернета вещей.

## <a name="read-messages-from-your-iot-hub"></a>Чтение сообщений из Центра Интернета вещей

Запустите пример приложения для имитации устройства и прочтите сообщения Центра Интернета вещей, выполнив следующую команду:

```bash
gulp run --iot-hub
```

Команда запускает приложение, которое отправляет сообщения в Центр Интернета вещей каждые 2 секунды. Она также создает дочерний процесс получения сообщения.

Все отправленные и полученные сообщения мгновенно появляются в том же окне консоли на главном компьютере. Приложение завершит работу через 40 секунд.

![Имитация примера приложения с отправленными и полученными сообщениями](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub_simudev.png)

## <a name="summary"></a>Сводка

Вы успешно запустили пример приложения для отправки данных в Центр Интернета вещей с помощью имитации устройства. Вы также прочли сообщения, отправленные в Центр Интернета вещей.

## <a name="next-steps"></a>Дальнейшие действия
[Создание учетной записи хранения Azure и приложения-функции Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)



