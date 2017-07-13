---
title: "Подключение Raspberry Pi (C) к Интернету вещей Azure. Урок 1. Получение инструментов (Ubuntu) | Документация Майкрософт"
description: "Скачайте и установите необходимые инструменты и программное обеспечение для работы с примером приложения на устройстве Pi под управлением Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "разработка для Интернета вещей, программное обеспечение Интернета вещей, ПО Интернета вещей, установка git в ubuntu, gulp run, установка node js ubuntu"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: 32cfea00-c254-4cef-8f6f-bbf807eca6b6
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: aadeff581e6134144ddc43416d4d5f9daea1b9f4
ms.contentlocale: ru-ru
ms.lasthandoff: 01/24/2017

---
<a id="get-the-tools-ubuntu-1604" class="xliff"></a>

# Получение инструментов (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 или более поздние версии](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-mac.md)

<a id="what-you-will-do" class="xliff"></a>

## Выполняемая задача
Скачаете средства разработки и программное обеспечение для работы с примером приложения на компьютере Raspberry Pi 3. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

> [!NOTE]
> Хотя С является языком программирования основного приложения логики, в уроках используются инструменты Node.js, позволяющие ознакомиться со списком устройств, а также выполнить сборку и развертывание примеров приложений.

<a id="what-you-will-learn" class="xliff"></a>

## Новые знания
В этой статье вы узнаете следующее:

* Как установить Git и Node.js.
  * [Git](https://git-scm.com) — это распределенная система управления версиями с открытым исходным кодом. Пример приложения, используемый в этой статье, хранится в репозитории Git.
  * [Node.js](https://nodejs.org/en/) — это среда выполнения JavaScript, характеризующаяся экосистемой пакета с широкими возможностями.
* Как использовать NPM, чтобы установить дополнительные средства разработки для Node.js.
  * Минимальная требуемая версия Node.js — 4.5 LTS.
  * [NPM](https://www.npmjs.com) — это один из диспетчеров пакетов для Node.js.

<a id="what-you-need" class="xliff"></a>

## Необходимые элементы
Для выполнения этой операции требуется:

* Подключение к Интернету для скачивания средств разработки и программного обеспечения.
* Компьютер под управлением Ubuntu 16.04 или более поздней версии.

<a id="install-git-nodejs-and-npm" class="xliff"></a>

## Установка Git, Node.js и NPM
Сочетанием клавиш `Ctrl + Alt + T` откройте окно терминала и выполните следующие команды:

```bash
sudo apt-get update
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install git
```

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## Установка дополнительных средств разработки для Node.js
Используйте [gulp.js](http://gulpjs.com) для автоматизации развертывания примера приложения на устройстве Pi. Используйте средство [device-discovery-cli](https://github.com/Azure/device-discovery-cli), чтобы получить сведения о сети для устройств Интернета вещей.

Установите `gulp` и `device-discovery-cli`, выполнив следующую команду в окне терминала:

```bash
sudo npm install -g device-discovery-cli gulp
```

При возникновении проблем с установкой Node.js и этих дополнительных средств разработки на компьютер под управлением Ubuntu см. [руководство по устранению неполадок](iot-hub-raspberry-pi-kit-c-troubleshooting.md), где приведены способы решения распространенных проблем.

<a id="install-visual-studio-code" class="xliff"></a>

## Установка Visual Studio Code
[Скачайте](https://code.visualstudio.com/docs/setup/linux) и установите Visual Studio Code. Visual Studio Code — это легковесный, но мощный редактор исходного кода для платформ Windows, Linux и macOS. Вы будете использовать этот редактор позже, чтобы изменить кода примера.

<a id="summary" class="xliff"></a>

## Сводка
Вы установили требуемые средства разработки и программное обеспечение для работы с примером приложения. Следующей задачей является создание, развертывание и запуск примера приложения на устройстве Pi.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
[Создание и развертывание приложения для включения индикатора](iot-hub-raspberry-pi-kit-c-lesson1-deploy-blink-app.md)


