---
title: "Приступая к работе с имитацией устройства и шлюзом Azure IoT. Урок 2. Получение инструментов (Windows) | Документация Майкрософт"
description: "Установите инструменты и программное обеспечение на главном компьютере под управлением Windows, создайте Центр Интернета вещей и зарегистрируйте устройство в нем."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "разработка для Интернета вещей, облачная служба Интернета вещей, программное обеспечение Интернета вещей, Azure CLI, ПО Интернета вещей, установка git в Windows, запуск инструмента Gulp, установка Node.js в Windows, установка Npm в Windows, установка Python в Windows"
ms.assetid: c16eee4c-8756-454b-82bf-3eb0dd51aa5f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: b07c6c383f31f77a78628593a60b7b76a49bca4f


---
# <a name="get-the-tools-windows-7-and-later"></a>Получение инструментов (Windows 7 и более поздние версии)
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
- Компьютер Windows.

## <a name="install-git-and-nodejs"></a>Установка Git и Node.js

Воспользуйтесь приведенными ниже ссылками, чтобы скачать и установить Git и LTS Node.js для Windows.

- [Скачать Git для Windows](https://git-scm.com/download/win/)
- [Скачать Node.js LTS для Windows](https://nodejs.org/en/)

## <a name="install-nodejs-development-tools"></a>Установка инструментов разработки Node.js

Используйте [gulp.js](http://gulpjs.com/) для автоматизации развертывания и выполнения сценариев.

Нажмите клавиши `Windows + R`, введите `cmd`, а затем нажмите клавишу `Enter`, чтобы открыть окно командной строки, и выполните следующую команду:

```cmd
npm install -g gulp
```

Если при установке возникают проблемы, способы их устранения см. в [этом руководстве по устранению неполадок](iot-hub-gateway-kit-c-sim-troubleshooting.md).

> [!Note]
> Для выполнения сценариев автоматизации, разработанных в Node.js, требуются Node, NPM и Gulp.

## <a name="install-python"></a>Установка Python

Вы можете выбрать одну из следующих версий Python: 2.7, 3.4 или 3.5. В этом руководстве мы используем Python 2.7. Если вы уже установили Python, перейдите к следующему разделу.

[Скачать Python для Windows](https://www.python.org/downloads/)

Кроме того, в системную переменную среды `PATH` необходимо добавить путь к папкам установки python.exe и pip.exe. По умолчанию файл python.exe устанавливается в папку `C:\Python27`, а pip.exe — в `C:\Python27\Scripts`.

## <a name="install-the-azure-cli"></a>Установка Azure CLI

Для установки Azure CLI выполните следующие действия:

1. Откройте окно командной строки с правами администратора.

2. Установите Azure CLI, выполнив следующую команду:

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```

   Установка может занять 5 минут.

3. Выполните следующую команду, чтобы проверить установку:

   ```cmd
   az iot -h
   ```

   Если установка прошла успешно, отобразятся следующие сведения.

   ![Проверка установки Azure CLI](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_win.png)

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code

Позже в этом руководстве вы используете Visual Studio Code, чтобы изменить файлы конфигурации.

[Скачайте](https://code.visualstudio.com/docs/setup/windows) и установите Visual Studio Code.

## <a name="summary"></a>Сводка

Вы установили на главном компьютере все необходимые инструменты и программное обеспечение. Следующая задача заключается в создании Центра Интернета вещей и регистрации в нем устройства с помощью Azure CLI.

## <a name="next-steps"></a>Дальнейшие действия
[Создание Центра Интернета вещей и регистрация устройства](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)



<!--HONumber=Jan17_HO4-->


