---
title: "Подключение Intel Edison (C) к Интернету вещей Azure. Урок 1. Развертывание приложения | Документация Майкрософт"
description: "Клонируйте пример приложения C c GitHub и разверните его с помощью инструмента Gulp на плате Intel Edison. Это приложение будет каждые две секунды включать и выключать светодиодный индикатор на компьютере."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "проекты светодиодного индикатора Arduino, включение светодиодного индикатора Arduino, код включения индикатора Arduino, программа включения индикатора Arduino, пример включения индикатора Arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: b02dfd3f-28fd-4b52-8775-eb0eaf74d707
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: c0589d488be5ec62686551b97d8949e5fed2f0a3
ms.contentlocale: ru-ru
ms.lasthandoff: 01/25/2017

---
<a id="create-and-deploy-the-blink-application" class="xliff"></a>

# Создание и развертывание приложения для включения индикатора
<a id="what-you-will-do" class="xliff"></a>

## Выполняемая задача
Клонирование примера приложения C из GitHub и его развертывание с помощью инструмента Gulp на устройстве Intel Edison. Этот пример приложения будет каждые две секунды включать светодиодный индикатор, подключенный к плате. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## Новые знания
* Как развертывать и запускать пример приложения в Edison.

<a id="what-you-need" class="xliff"></a>

## Необходимые элементы
Необходимо успешно выполнить следующие операции:

* [Настройка устройства][configure-your-device]
* [Get the tools][get-the-tools] (Получение инструментов)

<a id="open-the-sample-application" class="xliff"></a>

## Открытие примера приложения
Чтобы открыть пример приложения, сделайте следующее:

1. Клонируйте пример репозитория из GitHub, выполнив следующую команду.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-edison-getting-started.git
   ```
2. Откройте пример приложения в Visual Studio Code, выполнив следующие команды:

   ```bash
   cd iot-hub-c-edison-getting-started
   cd Lesson1
   code .
   ```

   ![Структура репозитория][repo-structure]

Файл в подпапке `app` — это ключевой исходный файл, содержащий код для управления светодиодным индикатором.

<a id="install-application-dependencies" class="xliff"></a>

### Установка зависимостей приложения
Установите библиотеки и другие модули, необходимые для примера приложения, выполнив следующую команду:

```bash
npm install
```

<a id="configure-the-device-connection" class="xliff"></a>

## Настройка подключения устройства
Чтобы настроить подключение устройства, выполните следующие действия.

1. Создайте файл конфигурации устройства, выполнив приведенную ниже команду.

   ```bash
   gulp init
   ```

   Файл конфигурации `config-edison.json` содержит учетные данные пользователя для входа в Edison. Чтобы избежать утечки учетных данных пользователя, файл конфигурации создается в подпапке `.iot-hub-getting-started` домашней папки на компьютере.

2. Откройте файл конфигурации устройства в Visual Studio Code, выполнив приведенную ниже команду.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

3. Замените заполнитель `[device hostname or IP address]` и `[device password]` на IP-адрес и пароль, указанный на предыдущем уроке.

   ![Config.json](media/iot-hub-intel-edison-lessons/lesson1/vscode-config-mac.png)

Поздравляем! Вы успешно создали пример приложения для платы Edison.

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

## Развертывание и запуск примера приложения
<a id="install-the-azure-iot-hub-sdk-on-edison" class="xliff"></a>

### Установка пакета SDK для Центра Интернета вещей Azure на устройстве Edison
Установите пакет SDK для Центра Интернета вещей Azure на устройстве Edison, выполнив следующую команду:

```bash
gulp install-tools
```

В зависимости от сетевого подключения для выполнения этой команды может потребоваться много времени. Для устройства Edison эту команду нужно запустить только один раз.

<a id="deploy-and-run-the-sample-app" class="xliff"></a>

### Развертывание и запуск примера приложения
Разверните и запустите пример приложения, выполнив следующую команду.

```bash
gulp deploy && gulp run
```

<a id="verify-the-app-works" class="xliff"></a>

### Проверка работы приложения
После того, как светодиодный индикатор мигнет 20 раз, пример приложения завершит работу автоматически. Если светодиодный индикатор не мигает, см. способы решения распространенных проблем в [руководстве по устранению неполадок][troubleshooting].

![Светодиодный индикатор мигает][led-blinking]

<a id="summary" class="xliff"></a>

## Сводка
Вы установили необходимые инструменты для работы с устройством Edison и развернули пример приложения, заставляющего светодиодный индикатор мигать. Теперь можно приступать к созданию, развертыванию и запуску другого примера приложения, которое подключает устройство Edison к Центру Интернета вещей Azure для отправки и получения сообщений.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
[Get the Azure tools][get-the-azure-tools] (Получение инструментов Azure)

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure_c.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking_c.jpg
[get-the-azure-tools]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md

