---
title: "Приступая к работе с устройством SensorTag и шлюзом Azure IoT | Документация Майкрософт"
description: "Приступая к работе с начальным набором для шлюза Azure IoT, создание Центра Интернета вещей Azure и подключение шлюза и SensorTag к Центру Интернета вещей"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Центр Интернета вещей Azure, шлюз Интернета вещей, приступая к работе с Интернетом вещей, инструментарий Интернета вещей"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 56d05f4e-f2c1-4b22-8701-f01e14deead6
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 624bdc7877d5048da08897f868272fd8e8f3f7b6
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-sensortag"></a>Приступая к работе с начальным набором шлюза Интернета вещей

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Имитация устройства](iot-hub-gateway-kit-c-sim-get-started.md)

В этом руководстве вы начнете с того, что узнаете основы работы с [начальным набором для шлюза Интернета вещей](https://aka.ms/gateway-kit). Вы будете работать с Intel NUC под управлением Wind River Linux и устройством [SensorTag от Texas Instruments](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html#main). Кроме того, вы узнаете, как можно легко подключать устройства к облаку с помощью Центра Интернета вещей Azure.

***
**Нет начального набора?** Щелкните [здесь](https://aka.ms/gateway-kit). **Нет SensorTag?** [Начните имитацию устройства](iot-hub-gateway-kit-c-sim-get-started.md) или [купите SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/?INTC=SensorTag&HQS=sensortag).
***

## <a name="lesson-1-configure-your-nuc"></a>Урок 1. Настройка NUC
![Урок 1. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-lesson1.png)

На этом уроке вы настроите Intel NUC (Next Unit of Computing) из набора в качестве шлюза Интернета вещей Azure, установите пакет Edge Интернета вещей Azure на NUC и запустите пример приложения, чтобы проверить работоспособность шлюза.

*Предполагаемое время выполнения: 15 минут.*

Перейдите к статье [Настройка Intel NUC в качестве шлюза Интернета вещей](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md).

## <a name="lesson-2-create-your-iot-hub"></a>Урок 2. Создание Центра Интернета вещей
![Урок 2. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-lesson2.png)

В ходе этого урока вы установите инструменты и программное обеспечение на главном компьютере. Затем создадите бесплатную учетную запись Azure, подготовите Центр Интернета вещей Azure и создадите в нем первое устройство.

Прежде чем продолжать, завершите урок 1.

### <a name="get-the-tools"></a>Получить инструменты
Установите инструменты и программное обеспечение на главном компьютере.

*Предполагаемое время выполнения: 20 минут*

Ознакомьтесь со статьей [Get the tools](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md) (Получение инструментов).

### <a name="create-an-iot-hub-and-register-your-device"></a>Создание Центра Интернета вещей и регистрация устройства
Создайте группу ресурсов, подготовьте Центр Интернета вещей Azure и добавьте в него первое устройство, используя Azure CLI.

*Предполагаемое время выполнения: 10 минут*

Перейдите к разделу [Создание Центра Интернета вещей и регистрация устройства](iot-hub-gateway-kit-c-lesson2-register-device.md).

## <a name="lesson-3-receive-messages-from-sensortag-and-read-messages-from-your-iot-hub"></a>Урок 3. Получение сообщений из SensorTag и чтение сообщений из Центра Интернета вещей
На этом уроке вы будете использовать сценарии для автоматизации настройки и выполнения примера приложения BLE в шлюзе. Такие приложения используют коллекцию модулей для агрегации и преобразования данных, обработки команд или для выполнения любого количества связанных задач. Модули взаимодействуют друг с другом через брокер сообщений. Пример приложения содержит модуль BLE и Центра Интернета вещей. Модуль BLE получает данные из SensorTag BLE. Модуль Центра Интернета вещей упаковывает полученные данные и отправляет их в Центр Интернета вещей через платформу шлюза, предоставленную в Edge Интернета вещей Azure.

![Урок 3. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-lesson3.png)

### <a name="configure-and-run-the-ble-sample-app"></a>Настройка и запуск примера приложения BLE
Настройте подключение между SensorTag и шлюзом. Затем завершите настройку и запустите пример приложения BLE.

*Предполагаемое время выполнения: 15 минут.*

Перейдите к статье [Настройка и запуск примера приложения BLE](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md).

### <a name="read-messages-from-your-iot-hub"></a>Чтение сообщений из Центра Интернета вещей
Запустите пример кода на главном компьютере для чтения сообщений из Центра Интернета вещей.

*Предполагаемое время выполнения: 15 минут.*

Перейдите к статье [Чтение сообщений из Центра Интернета вещей](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md).

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>Урок 4. Сохранение сообщений в Хранилище таблиц Azure
Создайте приложение-функцию Azure, которое получает входящие сообщения из Центра Интернета вещей и записывает их в Хранилище таблиц Azure.

![Урок 4. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Создание учетной записи хранения Azure и приложения-функции Azure
С помощью шаблона Azure Resource Manager создайте приложение-функцию Azure и учетную запись хранения Azure.

*Предполагаемое время выполнения: 10 минут*

Перейдите к статье [Создание приложения-функции Azure и учетной записи хранения Azure](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md).

### <a name="read-messages-persisted-in-azure-table-storage"></a>Чтение сообщений, сохраненных в Хранилище таблиц Azure
Отслеживайте сообщения, передаваемые из шлюза в облако, по мере их записывания в Хранилище таблиц Azure.

*Предполагаемое время выполнения: 5 минут*

Ознакомьтесь со статьей [Чтение сообщений, сохраненных в Хранилище таблиц Azure](iot-hub-gateway-kit-c-lesson4-read-table-storage.md).

## <a name="troubleshooting"></a>Устранение неполадок
Если в ходе какого-либо урока у вас возникнут проблемы, то решение можно найти в статье [Troubleshooting](iot-hub-gateway-kit-c-troubleshooting.md) (Устранение неполадок).

## <a name="explore-more"></a>Изучить подробнее
Дополнительные сведения см. на странице сообщества [Developer Zone комплекта Intel IoT Gateway](http://software.intel.com/iot/microsoft-azure).
