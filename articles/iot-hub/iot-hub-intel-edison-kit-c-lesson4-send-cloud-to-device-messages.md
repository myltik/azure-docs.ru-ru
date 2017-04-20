---
title: "Подключение Intel Edison (C) к Интернету вещей Azure. Урок 4. Получение сообщений | Документация Майкрософт"
description: "Пример приложения выполняется на устройстве Edison и отслеживает входящие сообщения от Центра Интернета вещей. Новая задача Gulp отправляет сообщения из Центра Интернета вещей на устройство Edison для управления состоянием светодиодного индикатора."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "управление светодиодным индикатором с помощью Arduino с веб-интерфейса, управление светодиодным индикатором с помощью Arduino через веб-интерфейс"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 820d38f3-d3b8-4249-9e2b-f1b9b771e62f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: b4d76eeb1bc0b406800c89c4952900350d8eafa8
ms.lasthandoff: 01/25/2017


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Запуск примера приложения для получения сообщений из облака на устройство
В этой статье вы развернете пример приложения на устройстве Intel Edison. Пример приложения отслеживает входящие сообщения от Центра Интернета вещей. Вы можете также запустить на компьютере задачу Gulp для отправки сообщений из Центра Интернета вещей на устройство Edison. При получении сообщений пример приложения включает и отключает светодиодный индикатор. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-you-will-do"></a>Выполняемая задача
* Подключите пример приложения к Центру Интернета вещей.
* Развернете и запустите пример приложения.
* Отправьте сообщения от Центра Интернета вещей на устройство Edison для включения и отключения светодиодного индикатора.

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:
* Как отслеживать входящие сообщения от Центра Интернета вещей.
* Как отправлять сообщения из облака на устройство, т. е. из Центра Интернета вещей на устройство Edison.

## <a name="what-you-need"></a>Необходимые элементы
* Настройка Intel Edison для использования. Узнайте, как настроить устройство Edison, в статье [Настройка устройства][configure-your-device] (Настройка Intel Edison).
* Центр Интернета вещей, созданный в вашей подписке Azure. Создание Центра Интернета вещей описано в статье [Создание Центра Интернета вещей и регистрация Intel Edison][create-your-azure-iot-hub].

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Подключение примера приложения к Центру Интернета вещей
1. Перейдите в папку репозитория `iot-hub-c-edison-getting-started`. Откройте пример приложения в Visual Studio Code, выполнив следующие команды:

   ```bash
   cd Lesson4
   code .
   ```

   Файл во вложенной папке `app` содержит основной код для отслеживания входящих сообщений из Центра Интернета вещей. Функция `blinkLED` включает и отключает светодиодный индикатор.

   ![Структура репозитория в примере приложения][repo-structure]
2. Запустите файл конфигурации, выполнив приведенную ниже команду.

   ```bash
   npm install
   gulp init
   ```

   Если вы выполнили действия, описанные в статье [Создание приложения-функции Azure и учетной записи хранения Azure][create-an-azure-function-app-and-storage-account], на этом компьютере, то все конфигурации наследуются, поэтому вы можете пропустить шаг с задачей развертывания и запуска примера приложения. Если вы выполнили действия, описанные в статье [Создание приложения-функции Azure и учетной записи хранения Azure][create-an-azure-function-app-and-storage-account], на другом компьютере, то необходимо заменить заполнители в файле `config-edison.json`. Файл `config-edison.json` находится во вложенной папке вашей домашней папки.

   ![Содержимое файла config-edison.json](media/iot-hub-intel-edison-lessons/lesson4/config-edison.png)

   * Замените **[device hostname or IP address]** IP-адресом, указанным при настройке устройства.
   * Замените **[IoT device connection string]** строкой подключения к устройству, которую можно получить с помощью команды `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Замените **[IoT hub connection string]** строкой подключения к Центру Интернета вещей, которую можно получить с помощью команды `az iot hub show-connection-string --name {my hub name}`.

   > [!NOTE]
   > Выполните также **gulp install-tools**, если это не было сделано на уроке 1.

## <a name="deploy-and-run-the-sample-application"></a>Развертывание и запуск примера приложения
Разверните и запустите пример приложения в Edison, выполнив следующие команды:

```bash
gulp deploy && gulp run
```

Эта команда Gulp развертывает пример приложения на устройстве Edison. Затем она запускает приложение на устройстве Edison и отдельную задачу на главном компьютере, которая отправляет на устройство Edison из Центра Интернета вещей 20 сообщений для включения и отключения светодиодного индикатора.

Запущенное приложение начинает ожидать передачи сообщений от Центра Интернета вещей. Одновременно с этим задача Gulp отправляет несколько сообщений для включения и отключения светодиодного индикатора из Центра Интернета вещей на устройство Edison. При получении устройством Edison каждого такого сообщения пример приложения вызывает функцию `blinkLED` для включения и отключения светодиодного индикатора.

В результате этот индикатор должен мигать каждые две секунды, пока задача Gulp отправляет 20 сообщений от Центра Интернета вещей на устройство Edison. Последнее сообщение содержит команду stop, при получении которой приложение завершит работу.

![Пример приложения с командой Gulp и сообщениями для включения и отключения светодиодного индикатора][gulp-command-and-blink-messages]

## <a name="summary"></a>Сводка
Итак, вы успешно отправили сообщения от Центра Интернета вещей на устройство Edison для включения и отключения светодиодного индикатора. Следующая задача является необязательной: изменение режима включения и отключения светодиодного индикатора.

## <a name="next-steps"></a>Дальнейшие действия
[Изменение режима включения и отключения светодиодного индикатора][change-the-on-and-off-behavior-of-the-led]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson4/repo_structure_c.png
[create-an-azure-function-app-and-storage-account]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[gulp-command-and-blink-messages]: media/iot-hub-intel-edison-lessons/lesson4/gulp_blink_c.png
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-c-lesson4-change-led-behavior.md
