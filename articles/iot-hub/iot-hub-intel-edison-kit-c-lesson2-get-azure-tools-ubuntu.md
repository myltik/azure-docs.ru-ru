---
title: "Подключение Intel Edison (C) к Интернету вещей Azure. Урок 2. Получение инструментов Azure (Ubuntu) | Документация Майкрософт"
description: "Установка Python и интерфейса командной строки Azure (Azure CLI) на компьютер под управлением Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Azure CLI, облачная служба Интернета вещей, облако Arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 2463cb8e-5758-4d72-af98-62520d41f2f7
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: e3b1c4dd3ff9a361c6c32cabd04fec523f8d6578
ms.contentlocale: ru-ru
ms.lasthandoff: 01/25/2017

---
<a id="get-azure-tools-ubuntu-1604" class="xliff"></a>

# Получение инструментов Azure (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 и более поздние версии][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## Выполняемая задача
Установите интерфейс командной строки Azure (Azure CLI). Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## Новые знания
В этой статье вы узнаете следующее:
* Как установить интерфейс командной строки Azure.
* Как добавить подгруппу команд интерфейса командной строки Azure для Интернета вещей.

<a id="what-you-need" class="xliff"></a>

## Необходимые элементы
* Компьютер под управлением Ubuntu с подключением к Интернету.
* Активная подписка Azure. Если у вас нет учетной записи, можно создать [бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

<a id="install-the-azure-cli" class="xliff"></a>

## Установка Azure CLI
Azure CLI — это кроссплатформенное средство для подготовки ресурсов и управлении ими непосредственно в командной строке Azure.

Чтобы установить последнюю версию Azure CLI, выполните следующие действия:

1. Выполните следующие команды в окне терминала. Установка интерфейса командной строки Azure может занять около пяти минут.

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```
2. Выполните следующую команду, чтобы проверить установку:

   ```bash
   az iot -h
   ```

Если установка прошла успешно, отобразятся следующие сведения.

![Выходные данные, указывающие на успешное выполнение](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_ubuntu.png)

<a id="summary" class="xliff"></a>

## Сводка
Вы установили интерфейс командной строки Azure. Перейдите к следующей задаче: создайте Центр Интернета вещей Azure и удостоверение устройства с помощью интерфейса командной строки Azure.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
[Создание Центра Интернета вещей и регистрация Intel Edison][create-your-iot-hub-and-register-intel-edison]


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-mac.md

