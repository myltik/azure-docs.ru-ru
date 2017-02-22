---
title: "Подключение Arduino к Интернету вещей Azure. Урок 2. Получение инструментов (macOS) | Документация Майкрософт"
description: "Установка Python и интерфейса командной строки Azure (Azure CLI) на компьютер под управлением macOS."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Azure CLI, облачная служба Интернета вещей, облако Arduino"
ms.assetid: 9b719293-01d2-4a2d-9c49-476e67f4816d
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: ad8ee9d524dcb096ac7db4bb23ec16d56518742b


---
# <a name="get-azure-tools-macos-1010"></a>Получение инструментов Azure (macOS 10.10)

> [!div class="op_single_selector"]
> * [Windows 7 или более поздние версии][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Выполняемая задача

Установите интерфейс командной строки Azure (Azure CLI). Если возникнут какие-либо проблемы в работе платы Adafruit Feather M0 WiFi Arduino, решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:
* Как установить Azure CLI.
* Как добавить подгруппу команд интерфейса командной строки Azure для Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы
* Компьютер под управлением Mac с подключением к Интернету.
* Активная подписка Azure. Если у вас нет учетной записи Azure, можно создать [бесплатную учетную запись Azure](http://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

## <a name="install-python"></a>Установка Python
Несмотря на то, что macOS поставляется уже с установленной версией Python 2.7, рекомендуется установить Python через Homebrew. См. статью [Installing Python on Mac OS X](http://docs.python-guide.org/en/latest/starting/install/osx/) (Установка Python на Mac OS X).

Установите Python и pip, выполнив следующую команду:

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Установка Azure CLI
Azure CLI — это кроссплатформенное средство для работы с командной строкой. С его помощью можно подготавливать ресурсы для Azure и управлять ими непосредственно в командной строке.

Чтобы установить последнюю версию Azure CLI, выполните следующие действия:

1. Выполните следующие команды в окне терминала. Установка интерфейса командной строки Azure может занять около пяти минут.

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
2. Выполните следующую команду, чтобы проверить установку:

   ```bash
   az iot -h
   ```

Если установка прошла успешно, отобразятся следующие сведения.

![Выходные данные, указывающие на успешное выполнение][output]

## <a name="summary"></a>Сводка
Вы установили интерфейс командной строки Azure. Перейдите к следующей задаче: создайте Центр Интернета вещей Azure и удостоверение устройства с помощью интерфейса командной строки Azure.

## <a name="next-steps"></a>Дальнейшие действия
[Создание Центра Интернета вещей и регистрация платы Arduino][create-your-iot-hub-and-register-your-arduino-board]


<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-mac.md
[output]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson2/az_iot_help_osx.png
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md


<!--HONumber=Jan17_HO4-->


