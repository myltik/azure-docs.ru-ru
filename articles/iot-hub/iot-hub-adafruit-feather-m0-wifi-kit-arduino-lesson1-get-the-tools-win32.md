---
title: "Подключение Arduino к Интернету вещей Azure. Урок 1. Получение инструментов (Windows) | Документация Майкрософт"
description: "Скачайте и установите необходимые инструменты и программное обеспечение для работы с первым примером приложения на плате Adafruit Feather M0 WiFi под управлением Windows 7 и более поздних версий."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "инструменты для разработки Arduino, разработка для Интернета вещей, программное обеспечение Интернета вещей, ПО Интернета вещей, установка git в Windows, установка Node.js в Windows"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 9cfb8cd2-eafb-4ba2-b23e-d94e114ff3a6
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: dbf4be49bf806415c7a7817acb37c7e33be6267d
ms.contentlocale: ru-ru
ms.lasthandoff: 01/24/2017

---
<a id="get-the-tools-windows-7-or-later" class="xliff"></a>

# Получение инструментов (Windows 7 или более поздние версии)

> [!div class="op_single_selector"]
> * [Windows 7 или более поздние версии][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## Выполняемая задача

Скачайте инструменты для разработки и программное обеспечение для работы с первым примером приложения на плате Adafruit Feather M0 WiFi Arduino.

Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

> [!NOTE]
> Хотя Arduino является языком программирования основного приложения логики, в уроках используются инструменты Node.js, позволяющие ознакомиться со списком устройств, а также выполнить сборку и развертывание примеров приложений.

<a id="what-you-will-learn" class="xliff"></a>

## Новые знания
В этой статье вы узнаете следующее:

* Как установить Git и Node.js.
  * [Git](https://git-scm.com) — это распределенная система управления версиями с открытым исходным кодом. Пример приложения, используемый в этой статье, хранится в репозитории Git.
  * [Node.js](https://nodejs.org/en/) — это среда выполнения JavaScript, характеризующаяся экосистемой пакета с широкими возможностями.
* Как использовать NPM, чтобы установить дополнительные средства разработки для Node.js.
  * Минимальная требуемая версия Node.js — 4.5 LTS.
  * [NPM](https://www.npmjs.com) — это один из диспетчеров пакетов для Node.js.

<a id="what-you-need" class="xliff"></a>

## Необходимые элементы

Для выполнения этой операции требуется:

* Подключение к Интернету для скачивания средств разработки и программного обеспечения.
* Компьютер под управлением Windows.

<a id="install-git-and-nodejs" class="xliff"></a>

## Установка Git и Node.js

Щелкните ссылки ниже, чтобы скачать и установить Git и LTS Node.js для Windows.

* [Скачать Git для Windows](https://git-scm.com/download/win/)
* [Скачать Node.js LTS для Windows](https://nodejs.org/en/)

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## Установка дополнительных средств разработки для Node.js

Используйте [gulp.js](http://gulpjs.com) для автоматизации развертывания примера приложения на плате Arduino.

Запустите командную строку от имени администратора. Установите `gulp` и `device-discovery-cli`, выполнив следующую команду в окне терминала:

```bash
npm install -g gulp device-discovery-cli
```

Если у вас возникли проблемы с установкой Node.js и этих дополнительных инструментов для разработки для Node.js на компьютер, способы решения распространенных проблем см. [руководство по устранению неполадок][troubleshooting]. Там приведены способы решения распространенных проблем.

<a id="install-visual-studio-code" class="xliff"></a>

## Установка Visual Studio Code

[Скачайте](https://code.visualstudio.com/docs/setup/windows) и установите Visual Studio Code. Visual Studio Code — это легковесный, но мощный редактор исходного кода для платформ Windows, Linux и macOS. Вы будете использовать этот редактор позже, чтобы изменить кода примера.

<a id="summary" class="xliff"></a>

## Сводка

Вы установили требуемые средства разработки и программное обеспечение для работы с примером приложения. Следующей задачей является создание, развертывание и запуск примера приложения на плате Arduino.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

[Создание и развертывание приложения для включения индикатора][create-and-deploy-the-blink-sample-application]
<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-mac.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[create-and-deploy-the-blink-sample-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
