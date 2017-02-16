---
title: "Подключение Raspberry Pi (Node) к Интернету вещей Azure. Урок 1. Получение инструментов (Windows) | Документация Майкрософт"
description: "Скачайте и установите необходимые инструменты и программное обеспечение для работы с примером приложения на устройстве Pi под управлением Windows 7 и более поздних версий."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "разработка для Интернета вещей, программное обеспечение Интернета вещей, ПО Интернета вещей, установка git в Windows, gulp run, установка node js в Windows, установка npm в Windows, установка python в Windows"
ms.assetid: b3d88e17-97cc-4f23-85fd-a688fc228eb8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 73df1a85cbcf7ee54fe625b6a150f89366ea2c14


---
# <a name="get-the-tools-windows-7-or-later"></a>Получение инструментов (Windows 7 или более поздние версии)

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
  * Минимальная требуемая версия Node.js — 4.5 LTS.
  * [NPM](https://www.npmjs.com) — это один из диспетчеров пакетов для Node.js.

## <a name="what-you-need"></a>Необходимые элементы
Для выполнения этой операции требуется:

* Подключение к Интернету для скачивания средств разработки и программного обеспечения.
* Компьютер под управлением Windows.

## <a name="install-git-and-nodejs"></a>Установка Git и Node.js
Воспользуйтесь приведенными ниже ссылками, чтобы скачать и установить Git и LTS Node.js для Windows.

* [Скачать Git для Windows](https://git-scm.com/download/win/)
* [Скачать Node.js LTS для Windows](https://nodejs.org/en/)

## <a name="install-additional-nodejs-development-tools"></a>Установка дополнительных средств разработки для Node.js
Используйте [gulp.js](http://gulpjs.com) для автоматизации развертывания примера приложения на устройстве Pi. Можно также использовать средство [device-discovery-cli](https://github.com/Azure/device-discovery-cli), чтобы получить сведения о сети для устройств IoT.

Запустите командную строку от имени администратора. Установите `gulp` и `device-discovery-cli`, выполнив следующую команду:

```bash
npm install -g device-discovery-cli gulp
```

При возникновении проблем с установкой на компьютер Node.js и этих дополнительных средств разработки для Node.js см. [руководство по устранению неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md). Там приведены способы решения распространенных проблем.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code
[Скачайте](https://code.visualstudio.com/docs/setup/windows) и установите Visual Studio Code. Visual Studio Code — это легковесный, но мощный редактор исходного кода для платформ Windows, Linux и macOS. Вы будете использовать этот редактор позже, чтобы изменить кода примера.

## <a name="summary"></a>Сводка
Вы установили требуемые средства разработки и программное обеспечение для работы с примером приложения. Следующей задачей является создание, развертывание и запуск примера приложения на устройстве Pi.

## <a name="next-steps"></a>Дальнейшие действия
[Create and deploy the blink sample application](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md) (Создание и развертывание примера приложения для включения индикатора)




<!--HONumber=Jan17_HO4-->


