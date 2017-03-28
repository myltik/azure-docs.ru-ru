---
title: "Подключение Intel Edison (Node) к Интернету вещей Azure. Урок 1. Получение инструментов (Windows) | Документация Майкрософт"
description: "Скачайте и установите необходимые инструменты и программное обеспечение для работы с примером приложения на устройстве Edison под управлением Windows 7 и более поздних версий."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "инструменты для разработки Arduino, разработка для Интернета вещей, программное обеспечение Интернета вещей, ПО Интернета вещей, установка git в Windows, установка Node.js в Windows"
ms.assetid: 4164b5a1-5a42-4d8a-9ff6-441e79fcc936
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 67e140e086d1deb17b7075133d969a93b859951c
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-windows-7-or-later"></a>Получение инструментов (Windows 7 или более поздние версии)
> [!div class="op_single_selector"]
> * [Windows 7 или более поздние версии][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Выполняемая задача
Скачайте инструменты для разработки и программное обеспечение для работы с примером приложения на устройстве Intel Edison. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:

* Как установить Git и Node.js.
  * [Git](https://git-scm.com) — это распределенная система управления версиями с открытым исходным кодом. Пример приложения, используемый в этой статье, хранится в репозитории Git.
  * [Node.js](https://nodejs.org/en/) — это среда выполнения JavaScript, характеризующаяся экосистемой пакета с широкими возможностями.
* Как использовать NPM, чтобы установить дополнительные средства разработки для Node.js.
  * Минимальная требуемая версия Node.js — 4.5 LTS.
  * [NPM](https://www.npmjs.com) — это один из диспетчеров пакетов для Node.js.

## <a name="what-you-need"></a>Необходимые элементы

Для выполнения этой операции требуется:

* Подключение к Интернету для скачивания средств разработки и программного обеспечения.
* Компьютер под управлением Windows.

## <a name="install-git-and-nodejs"></a>Установка Git и Node.js

Щелкните ссылки ниже, чтобы скачать и установить Git и LTS Node.js для Windows.

* [Скачать Git для Windows](https://git-scm.com/download/win/)
* [Скачать Node.js LTS для Windows](https://nodejs.org/en/)

## <a name="install-additional-nodejs-development-tools"></a>Установка дополнительных средств разработки для Node.js

Используйте [gulp.js](http://gulpjs.com) для автоматизации развертывания примера приложения на устройстве Edison.

Запустите командную строку от имени администратора. Установите `gulp`, выполнив следующую команду:

```cmd
npm install -g gulp
```

Если у вас возникли проблемы с установкой Node.js и этих дополнительных инструментов для разработки для Node.js на компьютер, способы решения распространенных проблем см. [руководство по устранению неполадок][troubleshooting]. Там приведены способы решения распространенных проблем.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code

[Скачайте](https://code.visualstudio.com/docs/setup/windows) и установите Visual Studio Code. Visual Studio Code — это легковесный, но мощный редактор исходного кода для платформ Windows, Linux и macOS. Вы будете использовать этот редактор позже, чтобы изменить кода примера.

## <a name="summary"></a>Сводка

Вы установили требуемые средства разработки и программное обеспечение для работы с примером приложения. Следующей задачей является создание, развертывание и запуск примера приложения на устройстве Edison.

## <a name="next-steps"></a>Дальнейшие действия

[Создание и развертывание приложения для включения индикатора][create-and-deploy-the-blink-application]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-node-lesson1-deploy-blink-app.md
[windows]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-mac.md

