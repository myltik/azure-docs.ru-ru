---
title: "Получение инструментов для начального набора Интернета вещей Azure (Ubuntu 16.04) | Документация Майкрософт"
description: "Скачайте и установите необходимые инструменты и программное обеспечение для работы с первым примером приложения на Adafruit Feather M0 WiFi под управлением Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "инструменты для разработки Arduino, разработка для Интернета вещей, программное обеспечение Интернета вещей, ПО Интернета вещей, установка git в Ubuntu, установка Node.js в Ubuntu"
ms.assetid: 7572f191-420d-41f0-923b-7ea86c0bfa73
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 6eaf3c0a0509f78276a9303102837e37b06a80e0


---
# <a name="get-the-tools-ubuntu-1604"></a>Получение инструментов (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 или более поздние версии][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Выполняемая задача

Скачайте инструменты для разработки и программное обеспечение для работы с первым примером приложения на плате Adafruit Feather M0 WiFi Arduino. 

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

> [!NOTE]
> Хотя Arduino является языком программирования основного приложения логики, в уроках используются инструменты Node.js, позволяющие ознакомиться со списком устройств, а также выполнить сборку и развертывание примеров приложений.

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:

* Как установить Git и Node.js.
  * [Git](https://git-scm.com) — это распределенная система управления версиями с открытым исходным кодом. Пример приложения, используемый в этой статье, хранится в репозитории Git.
  * [Node.js](https://nodejs.org/en/) — это среда выполнения JavaScript, характеризующаяся экосистемой пакета с широкими возможностями.
* Как использовать NPM, чтобы установить дополнительные средства разработки для Node.js.
  * Минимальная требуемая версия Node.js — 4.5 LTS.
  * [NPM](https://www.npmjs.com) — это один из диспетчеров пакетов для Node.js.

## <a name="what-you-need"></a>Необходимые элементы
Для выполнения этой операции требуется:
* Подключение к Интернету для скачивания средств разработки и программного обеспечения.
* Компьютер под управлением Ubuntu 16.04 или более поздней версии.

## <a name="install-git-nodejs-and-npm"></a>Установка Git, Node.js и NPM
Сочетанием клавиш `Ctrl + Alt + T` откройте окно терминала и выполните следующие команды:

```bash
sudo apt-get update
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install git
```

## <a name="install-additional-nodejs-development-tools"></a>Установка дополнительных средств разработки для Node.js
Используйте [gulp.js](http://gulpjs.com) для автоматизации развертывания примера приложения на плате Arduino.

Установите `gulp` и `device-discovery-cli`, выполнив следующую команду в окне терминала:

```bash
sudo npm install -g gulp device-discovery-cli
```

Если у вас возникли проблемы с установкой Node.js и этих дополнительных инструментов для разработки на компьютер под управлением Ubuntu, способы решения распространенных проблем см. [руководство по устранению неполадок][troubleshooting], где приведены способы решения распространенных проблем.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code
[Скачайте](https://code.visualstudio.com/docs/setup/linux) и установите Visual Studio Code. Visual Studio Code — это легковесный, но мощный редактор исходного кода для платформ Windows, Linux и macOS. Вы будете использовать этот редактор позже, чтобы изменить кода примера.

## <a name="summary"></a>Сводка
Вы установили требуемые средства разработки и программное обеспечение для работы с примером приложения. Следующей задачей является создание, развертывание и запуск примера приложения на плате Arduino.

## <a name="next-steps"></a>Дальнейшие действия
[Создание и развертывание приложения для включения индикатора][create-and-deploy-the-blink-sample-application]

<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-mac.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[create-and-deploy-the-blink-sample-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md


<!--HONumber=Dec16_HO2-->


