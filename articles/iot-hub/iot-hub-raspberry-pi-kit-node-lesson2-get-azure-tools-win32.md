---
title: "Получение инструментов Azure (Windows 7 и более поздние версии) | Документация Майкрософт"
description: "С помощью этой статьи вы установите Python и интерфейс командной строки Azure (Azure CLI) в Windows 7 и более поздних версиях."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "облачная служба Интернета вещей, azure cli"
ms.assetid: acfa13e3-6d2c-4e68-9a77-1cbc2cf3f9c1
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 7fbf21434952ece22468df6873e10e6f3eae1724
ms.openlocfilehash: 9fe70cbb905ff45498294abcb2893ffadc70b480


---
# <a name="get-azure-tools-windows-7-and-later"></a>Получение инструментов Azure (Windows 7 и более поздние версии)
> [!div class="op_single_selector"]
> * [Windows 7 и более поздние версии](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>Выполняемая задача
Установка Python и интерфейса командной строки Azure (Azure CLI). Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:
* Как установить Python.
* Как установить интерфейс командной строки Azure.

## <a name="what-you-need"></a>Необходимые элементы
* Компьютер под управлением Windows с подключением к Интернету.
* Активная подписка Azure. Если ее нет, можно создать [бесплатную пробную учетную запись Azure](http://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

## <a name="install-python"></a>Установка Python
[Установите Python](https://www.python.org/downloads/) на компьютере под управлением Windows. Установить можно такие версии Python: 2.7, 3.4 или 3.5. Это руководство основано на версии Python 2.7. Если вы уже установили Python, перейдите к следующему разделу и установите интерфейс командной строки Azure.

Кроме того, в системную переменную среды `PATH` необходимо добавить путь к папкам установки python.exe и pip.exe. По умолчанию файл python.exe устанавливается в папку `C:\Python27`, а pip.exe — в `C:\Python27\Scripts`.

## <a name="install-the-azure-cli"></a>Установка Azure CLI
Azure CLI — это кроссплатформенное средство для работы с командной строкой. С его помощью можно подготавливать ресурсы для Azure и управлять ими непосредственно в командной строке.

Для установки Azure CLI выполните следующие действия:

1. Откройте окно командной строки с правами администратора.
2. Выполните следующие команды:

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. Проверьте установку, выполнив следующую команду:

   ```bash
   az iot -h
   ```

Если установка прошла успешно, отобразятся следующие сведения:

![Выходные данные, указывающие на успешное выполнение](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

## <a name="summary"></a>Сводка
Вы установили интерфейс командной строки Azure. Перейдите к следующей задаче: создайте Центр Интернета вещей Azure и удостоверение устройства с помощью интерфейса командной строки Azure.

## <a name="next-steps"></a>Дальнейшие действия
[Создание Центра Интернета вещей и регистрация Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)




<!--HONumber=Dec16_HO1-->


