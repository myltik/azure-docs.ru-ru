---
title: "Приступая к работе с имитацией устройства и шлюзом Azure IoT. Урок 2. Получение инструментов (macOS) | Документация Майкрософт"
description: "Установите инструменты на компьютере Mac, создайте Центр Интернета вещей и зарегистрируйте устройство в нем."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "разработка для Интернета вещей, программное обеспечение Интернета вещей, облачные службы Интернета вещей, ПО Интернета вещей, Azure CLI, установка Python на компьютере Mac, установка git на компьютере Mac, запуск инструмента Gulp, установка Node.js на компьютере Mac"
ms.assetid: 42f9d186-e20c-4ef9-98cc-71d39e058b06
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 8b79b166fb16d6454fde46996e715779bf19eac7
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-macos"></a>Получение инструментов (macOS)
> [!div class="op_single_selector"]
> * [Windows 7 или более поздние версии](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Выполняемая задача

- Установка Git, Node.js, Gulp и Python.
- Установите интерфейс командной строки Azure (Azure CLI). 

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания

Из этого урока вы узнаете:

- Как установить [Git](https://git-scm.com/) и [Node.js](https://nodejs.org/en/).
  - Git — это распределенная система управления версиями с открытым кодом. Пример приложения, используемый в этом руководстве, хранится в репозитории Git.
  - Node.js — это среда выполнения JavaScript, характеризующаяся экосистемой пакета с широкими возможностями.
- Как установить инструменты разработки для Node.js. с помощью [NPM](https://www.npmjs.com/).
  - Минимальная требуемая версия Node.js — 4.5 LTS.
  - NPM — это один из диспетчеров пакетов для Node.js.
- Как установить Visual Studio Code.
  - Visual Studio Code — это упрощенный кроссплатформенный, но мощный редактор исходного кода для платформ Windows, Linux и macOS. Он поддерживает возможности отладки, выделения синтаксиса, интеллектуального автозавершения кода, встроенные элементы управления Git, фрагменты кода, а также возможности рефакторинга кода.
- Как установить Python.
  - Python — это широко используемый высокоуровневый интерпретируемый и динамический язык программирования общего назначения.
- Как установить интерфейс командной строки Azure.
  - Azure CLI — это кроссплатформенное средство для работы с командной строкой. С его помощью можно подготавливать ресурсы для Azure и управлять ими непосредственно в командной строке.
- Как создать Центр Интернета вещей с помощью Azure CLI.

## <a name="what-you-need"></a>Необходимые элементы

- Подключение к Интернету для скачивания инструментов и программного обеспечения.
- Компьютер Mac под управлением OS X Yosemite (10.10) или более поздней версии.

## <a name="install-git-and-nodejs"></a>Установка Git и Node.js

Чтобы установить Git и Node.js, используйте служебную программу управления пакетами Homebrew и сделайте следующее:

1. [Скачайте](http://brew.sh/) и установите Homebrew. Если вы уже установили Homebrew, перейдите к шагу 2.
   1. Нажмите сочетание клавиш `Cmd + Space` и введите `Terminal`, чтобы открыть окно терминала.
   2. Выполните следующую команду:

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```

2. Установите Git и Node.js, выполнив следующую команду:

    ```bash
    brew install node git
    ```

## <a name="install-nodejs-development-tools"></a>Установка инструментов разработки Node.js

Используйте [gulp.js](http://gulpjs.com/) для автоматизации развертывания и выполнения сценариев.

Чтобы установить Gulp, выполните следующую команду в окне терминала:

```bash
npm install -g gulp
```

Если при установке возникают проблемы, способы их устранения см. в [этом руководстве по устранению неполадок](iot-hub-gateway-kit-c-sim-troubleshooting.md).

> [!Note]
> Для выполнения сценариев автоматизации, разработанных в Node.js, требуются Node, NPM и Gulp.

## <a name="install-python"></a>Установка Python

Несмотря на то что Mac OS X поставляется с версией Python 2.7, мы рекомендуем установить Python через Homebrew. Ознакомьтесь со статьей [Installing Python on Mac OS X](http://docs.python-guide.org/en/latest/starting/install/osx/) (Установка Python на Mac OS X).

Установите Python и pip, выполнив следующую команду:

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Установка Azure CLI

Для установки Azure CLI выполните следующие действия:

1. Выполните следующие команды в окне терминала:
   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
   Установка может занять 5 минут.

2. Выполните следующую команду, чтобы проверить установку:
   ```bash
   az iot -h
   ```
   Если установка прошла успешно, отобразятся следующие сведения.

   ![Проверка установки Azure CLI](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_osx.png)

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code

Позже в этом руководстве вы используете Visual Studio Code, чтобы изменить файлы конфигурации.

[Скачайте](https://code.visualstudio.com/docs/setup/osx) и установите Visual Studio Code.

## <a name="summary"></a>Сводка

Вы установили на компьютере Mac все необходимые инструменты и программное обеспечение. Следующая задача заключается в создании Центра Интернета вещей и регистрации в нем устройства с помощью Azure CLI.

## <a name="next-steps"></a>Дальнейшие действия
[Создание Центра Интернета вещей и регистрация устройства](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

