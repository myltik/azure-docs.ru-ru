---
title: "Получение инструментов (Ubuntu 16.04) | Документация Майкрософт"
description: "Скачайте и установите необходимые инструменты и программное обеспечение для работы с примером приложения на устройстве Pi под управлением Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "разработка для Интернета вещей, программное обеспечение Интернета вещей, ПО Интернета вещей, установка git в ubuntu, gulp run, установка node js ubuntu"
ms.assetid: 4d5e45c0-1db9-4662-a039-99ba26333085
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: 31b5ac4e60eaafcc134ee702a4d47c4ea735df39


---
# <a name="get-the-tools-ubuntu-1604"></a>Получение инструментов (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 или более поздние версии](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)


## <a name="what-you-will-do"></a>Выполняемая задача
Скачайте средства разработки и программное обеспечение для работы с примером приложения на устройстве Raspberry Pi 3. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:

* Как установить Git и Node.js.
  * [Git](https://git-scm.com) — это распределенная система управления версиями с открытым исходным кодом. Пример приложения, используемый в этой статье, хранится в репозитории Git.
  * [Node.js](https://nodejs.org/en/) — это среда выполнения JavaScript, характеризующаяся экосистемой пакета с широкими возможностями.
* Как использовать NPM, чтобы установить дополнительные средства разработки для Node.js.
  * Минимальная требуемая версия Node.js — 4.5 LTS.
  * [NPM](https://www.npmjs.com) — это один из диспетчеров пакетов для Node.js.

## <a name="what-do-you-need"></a>Требования
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
Используйте [gulp.js](http://gulpjs.com) для автоматизации развертывания примера приложения на устройстве Pi. Можно также использовать средство [device-discovery-cli](https://github.com/Azure/device-discovery-cli), чтобы получить сведения о сети для устройств IoT.

Установите `gulp` и `device-discovery-cli`, выполнив следующую команду в окне терминала:

```bash
sudo npm install -g device-discovery-cli gulp
```

При возникновении проблем с установкой Node.js и этих дополнительных средств разработки на компьютер под управлением Ubuntu см. [руководство по устранению неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md), где приведены способы решения распространенных проблем.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code
[Скачайте](https://code.visualstudio.com/docs/setup/linux) и установите Visual Studio Code. Visual Studio Code — это легковесный, но мощный редактор исходного кода для платформ Windows, Linux и macOS. Вы будете использовать этот редактор позже, чтобы изменить кода примера.

## <a name="summary"></a>Сводка
Вы установили требуемые средства разработки и программное обеспечение для работы с примером приложения. Следующей задачей является создание, развертывание и запуск примера приложения на устройстве Pi.

## <a name="next-steps"></a>Дальнейшие действия
[Create and deploy the blink sample application](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md) (Создание и развертывание примера приложения для включения индикатора)




<!--HONumber=Nov16_HO5-->


