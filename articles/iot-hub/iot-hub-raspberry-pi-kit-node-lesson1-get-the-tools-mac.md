---
title: "Получение инструментов (macOS 10.10) | Документация Майкрософт"
description: "Скачайте и установите необходимые инструменты и программное обеспечение для работы с примером приложения на устройстве Pi под управлением macOS."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "разработка для Интернета вещей, программное обеспечение Интернета вещей, ПО Интернета вещей, установка python mac, установка git в mac, gulp run, установка node js mac"
ms.assetid: 2ea6d211-c0e8-4ade-ac69-d1c2261d78c4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: cd3d9a6db98f5239fc6231464605848789751c7d


---
# <a name="get-the-tools-macos-1010"></a>Получение инструментов (MacOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 или более поздние версии](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Выполняемая задача
Скачаете средства разработки и программное обеспечение для работы с примером приложения на компьютере Raspberry Pi 3. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

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
* Компьютер Mac под управлением Yosemite macOS (10.10) или более поздней версии.

## <a name="install-git-and-nodejs"></a>Установка Git и Node.js
Чтобы установить Git и Node.js, используйте пакет управления [Homebrew](http://brew.sh), выполнив следующие действия.

1. Установите Homebrew. Если вы уже установили Homebrew, перейдите к шагу 2.
   
   1. Нажмите сочетание клавиш `Cmd + Space` и введите `Terminal`, чтобы открыть окно терминала.
   2. Выполните следующую команду:
      
      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```
2. Установите Git и Node.js, выполнив следующую команду:
   
   ```bash
   brew install node git
   ```

## <a name="install-additional-nodejs-development-tools"></a>Установка дополнительных средств разработки для Node.js
Используйте [gulp.js](http://gulpjs.com) для автоматизации развертывания примера приложения на устройстве Pi. Используйте средство [device-discovery-cli](https://github.com/Azure/device-discovery-cli), чтобы получить сведения о сети для устройств Интернета вещей.

Установите `gulp` и `device-discovery-cli`, выполнив следующую команду в окне терминала:

```bash
npm install -g device-discovery-cli gulp
```

При возникновении проблем с установкой Node.js и этими дополнительными средствами разработки на компьютер под управлением macOS, см. [руководство по устранению неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md). Там приведены способы решения распространенных проблем.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code
[Скачайте](https://code.visualstudio.com/docs/setup/osx) и установите Visual Studio Code. Visual Studio Code — это легковесный, но мощный редактор исходного кода для платформ Windows, Linux и macOS. Вы будете использовать этот редактор позже, чтобы изменить кода примера.

## <a name="summary"></a>Сводка
Вы установили требуемые средства разработки и программное обеспечение для работы с примером приложения. Следующей задачей является создание, развертывание и запуск примера приложения на устройстве Pi.

## <a name="next-steps"></a>Дальнейшие действия
[Create and deploy the blink sample application](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md) (Создание и развертывание примера приложения для включения индикатора)




<!--HONumber=Nov16_HO5-->


