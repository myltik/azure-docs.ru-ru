---
title: "Приступая к работе с начальным набором для шлюза Azure IoT | Документация Майкрософт"
description: "Приступите к работе с начальным набором для шлюза Azure IoT, создайте Центр Интернета вещей Azure и подключите шлюз к нему"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Центр Интернета вещей Azure, шлюз Интернета вещей, приступая к работе с Интернетом вещей, инструментарий Интернета вещей"
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/21/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: c62e0cc560af18945c371af000ec08c0ddc7cd5a


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-simulated-device"></a>Приступая к работе с начальным набором шлюза Интернета вещей с имитацией устройства

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Имитация устройства](iot-hub-gateway-kit-c-sim-get-started.md)

В этом руководстве вы начнете с того, что узнаете основы работы с [начальным набором для шлюза Интернета вещей](https://aka.ms/gateway-kit). Вы будете работать с Intel NUC под управлением Wind River Linux. Кроме того, вы узнаете, как можно легко подключать устройства к облаку с помощью Центра Интернета вещей Azure.

***
**Нет начального набора?** Щелкните [здесь](https://aka.ms/gateway-kit).
***

## <a name="lesson-1-configure-your-nuc"></a>Урок 1. Настройка NUC
![Урок 1. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

На этом уроке вы настроите Intel NUC (Next Unit of Computing) из набора в качестве шлюза Azure IoT, установите пакет SDK для шлюза Azure IoT на NUC и запустите пример приложения, чтобы проверить работоспособность шлюза.

*Предполагаемое время выполнения: 15 минут.*

Перейдите к статье [Настройка Intel NUC в качестве шлюза Интернета вещей](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md).

## <a name="lesson-2-create-your-iot-hub"></a>Урок 2. Создание Центра Интернета вещей
![Урок 2. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

В ходе этого урока вы установите инструменты и программное обеспечение на главном компьютере. Затем создадите бесплатную учетную запись Azure, подготовите Центр Интернета вещей Azure и создадите в нем первое устройство.

Прежде чем продолжать, завершите урок 1.

### <a name="get-the-tools"></a>Получить инструменты
Установите инструменты и программное обеспечение на главном компьютере.

*Предполагаемое время выполнения: 20 минут*

Ознакомьтесь со статьей [Get the tools](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md) (Получение инструментов).

### <a name="create-an-iot-hub-and-register-your-device"></a>Создание Центра Интернета вещей и регистрация устройства
Создайте группу ресурсов, подготовьте Центр Интернета вещей Azure и добавьте в него первое устройство, используя Azure CLI.

*Предполагаемое время выполнения: 10 минут*

Перейдите к разделу [Создание Центра Интернета вещей и регистрация устройства](iot-hub-gateway-kit-c-sim-lesson2-register-device.md).

## <a name="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub"></a>Урок 3. Получение сообщений из имитации устройства и чтение сообщений из вашего Центра Интернета вещей
В ходе этого урока вы используете сценарии для автоматизации настройки и выполнения приложения для имитации устройства в шлюзе. Приложение для имитации устройства создает пример данных температуры и отправляет его в модуль Центра Интернета вещей. Модуль Центра Интернета вещей упаковывает полученные данные и отправляет их в Центр Интернета вещей через платформу шлюза, предоставленную в пакете SDK для шлюза Azure IoT.

![Урок 3. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

### <a name="configure-and-run-a-simulated-device"></a>Настройка и запуск имитации устройства
Подготовьте примеры кодов. Затем настройте и запустите пример приложения для имитации устройства.

*Предполагаемое время выполнения: 15 минут.*

Перейдите к статье [Настройка и запуск имитации устройства](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).

### <a name="read-messages-from-your-iot-hub"></a>Чтение сообщений из Центра Интернета вещей
Запустите пример кода на главном компьютере для чтения сообщений из Центра Интернета вещей.

*Предполагаемое время выполнения: 15 минут.*

Перейдите к статье [Чтение сообщений из Центра Интернета вещей](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md).

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>Урок 4. Сохранение сообщений в Хранилище таблиц Azure
Создайте приложение-функцию Azure, которое получает входящие сообщения из Центра Интернета вещей и записывает их в Хранилище таблиц Azure.

![Урок 4. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Создание учетной записи хранения Azure и приложения-функции Azure
С помощью шаблона Azure Resource Manager создайте приложение-функцию Azure и учетную запись хранения Azure.

*Предполагаемое время выполнения: 10 минут*

Перейдите к статье [Создание приложения-функции Azure и учетной записи хранения Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md).

### <a name="read-messages-persisted-in-azure-table-storage"></a>Чтение сообщений, сохраненных в Хранилище таблиц Azure
Отслеживайте сообщения, передаваемые из шлюза в облако, по мере их записывания в Хранилище таблиц Azure.

*Предполагаемое время выполнения: 5 минут*

Ознакомьтесь со статьей [Чтение сообщений, сохраненных в Хранилище таблиц Azure](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md).

## <a name="troubleshooting"></a>Устранение неполадок
Если в ходе какого-либо урока у вас возникнут проблемы, то решение можно найти в статье [Troubleshooting](iot-hub-gateway-kit-c-sim-troubleshooting.md) (Устранение неполадок).

## <a name="explore-more"></a>Изучить подробнее
Дополнительные сведения см. на странице сообщества [Developer Zone комплекта Intel IoT Gateway](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit).


<!--HONumber=Dec16_HO3-->


