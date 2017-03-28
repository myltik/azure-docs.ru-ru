---
title: "Приступая к работе с устройством SensorTag и шлюзом Azure IoT. Урок 2. Получение инструментов (Ubuntu) | Документация Майкрософт"
description: "Установите инструменты и программное обеспечение на главном компьютере под управлением Ubuntu, создайте Центр Интернета вещей и зарегистрируйте устройство в нем."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "разработка для Интернета вещей, программное обеспечение Интернета вещей, облачные службы Интернета вещей, ПО Интернета вещей, Azure CLI, установка git в Ubuntu, запуск инструмента Gulp, установка Node.js в Ubuntu"
ms.assetid: 0bac1412-385b-4255-a33f-9d44c35feb3e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 6414a4f37367d8acc0bab9356b8a09aadf13bfc9
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-ubuntu-1604"></a>Получение инструментов (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 или более поздние версии](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Выполняемая задача

- Установка Git, Node.js, Gulp и Python.
- Установите интерфейс командной строки Azure (Azure CLI). 

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-gateway-kit-c-troubleshooting.md).
## <a name="what-you-will-learn"></a>Новые знания

Из этого урока вы узнаете:

- Как установить Git и Node.js.
  - Git — это распределенная система управления версиями с открытым кодом. Пример приложения, используемый в этом руководстве, хранится в репозитории Git.
  - Node.js — это среда выполнения JavaScript, характеризующаяся экосистемой пакета с широкими возможностями.
- Как установить инструменты разработки для Node.js. с помощью NPM.
  - Минимальная требуемая версия Node.js — 4.5 LTS.
  - NPM — это один из диспетчеров пакетов для Node.js.
- Как установить Visual Studio Code.
  - Visual Studio Code — это упрощенный кроссплатформенный, но мощный редактор исходного кода для платформ Windows, Linux и macOS. Он поддерживает возможности отладки, выделения синтаксиса, интеллектуального автозавершения кода, встроенные элементы управления Git, фрагменты кода, а также возможности рефакторинга кода.
- Установка Azure CLI
  - Azure CLI — это кроссплатформенное средство для работы с командной строкой. С его помощью можно подготавливать ресурсы для Azure и управлять ими непосредственно в командной строке.
- Как создать Центр Интернета вещей с помощью Azure CLI.

## <a name="what-you-need"></a>Необходимые элементы

- Подключение к Интернету для скачивания инструментов и программного обеспечения.
- Компьютер под управлением Ubuntu 16.04 или более поздней версии.

## <a name="install-git-and-nodejs"></a>Установка Git и Node.js

Чтобы установить Git и Node.js, сделайте следующее:

1. Нажмите клавиши `Ctrl + Alt + T`, чтобы открыть окно терминала.
2. Выполните следующие команды:

   ```bash
   sudo apt-get update
   curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
   sudo apt-get install -y nodejs
   sudo apt-get install git
   ```

## <a name="install-nodejs-development-tools"></a>Установка инструментов разработки Node.js

Используйте [gulp.js](http://gulpjs.com/) для автоматизации развертывания и выполнения сценариев.

Чтобы установить Gulp, выполните следующую команду в окне терминала:

```bash
sudo npm install -g gulp
```

Если при установке возникают проблемы, способы их устранения см. в [этом руководстве по устранению неполадок](iot-hub-gateway-kit-c-troubleshooting.md).

> [!Note]
> Для выполнения сценариев автоматизации, разработанных в Node.js, требуются Node, NPM и Gulp.

## <a name="install-the-azure-cli"></a>Установка Azure CLI

Для установки Azure CLI выполните следующие действия:

1. Выполните следующие команды в окне терминала:

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```

   Установка может занять 5 минут.

2. Выполните следующую команду, чтобы проверить установку:

   ```bash
   az iot -h
   ```
Если установка прошла успешно, отобразятся следующие сведения.
![Проверка установки Azure CLI](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_ubuntu.png)

### <a name="install-visual-studio-code"></a>Установка Visual Studio Code

Позже в этом руководстве вы используете Visual Studio Code, чтобы изменить файлы конфигурации.

[Скачайте](https://code.visualstudio.com/docs/setup/linux) и установите Visual Studio Code.

## <a name="summary"></a>Сводка

Вы установили на главном компьютере все необходимые инструменты и программное обеспечение. Следующая задача заключается в создании Центра Интернета вещей и регистрации в нем устройства с помощью Azure CLI.

## <a name="next-steps"></a>Дальнейшие действия
[Создание Центра Интернета вещей и регистрация устройства](iot-hub-gateway-kit-c-lesson2-register-device.md)

