---
title: "Приступая к работе с имитацией устройства и шлюзом Azure IoT | Документация Майкрософт"
description: "Приступите к работе с начальным набором для шлюза Azure IoT, создайте Центр Интернета вещей Azure и подключите шлюз к нему"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Центр Интернета вещей Azure, шлюз Интернета вещей, приступая к работе с Интернетом вещей, инструментарий Интернета вещей"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 916fa40d9ac857dfa72197b40c232834593d3891
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017

---

<a id="get-started-with-iot-gateway-starter-kit-with-a-simulated-device" class="xliff"></a>

# Приступая к работе с начальным набором шлюза Интернета вещей с имитацией устройства

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Имитация устройства](iot-hub-gateway-kit-c-sim-get-started.md)

В этом руководстве вы начнете с того, что узнаете основы работы с [начальным набором для шлюза Интернета вещей](https://aka.ms/gateway-kit). Вы будете работать с Intel NUC под управлением Wind River Linux. Кроме того, вы узнаете, как можно легко подключать устройства к облаку с помощью Центра Интернета вещей Azure.

***
**Нет начального набора?** Щелкните [здесь](https://aka.ms/gateway-kit).
***

<a id="lesson-1-configure-your-nuc" class="xliff"></a>

## Урок 1. Настройка NUC
![Урок 1. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

На этом уроке вы настроите Intel NUC (Next Unit of Computing) из набора в качестве шлюза Интернета вещей Azure, установите пакет Edge Интернета вещей Azure на NUC и запустите пример приложения, чтобы проверить работоспособность шлюза.

*Предполагаемое время выполнения: 15 минут.*

Перейдите к статье [Настройка Intel NUC в качестве шлюза Интернета вещей](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md).

<a id="lesson-2-create-your-iot-hub" class="xliff"></a>

## Урок 2. Создание Центра Интернета вещей
![Урок 2. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

В ходе этого урока вы установите инструменты и программное обеспечение на главном компьютере. Затем создадите бесплатную учетную запись Azure, подготовите Центр Интернета вещей Azure и создадите в нем первое устройство.

Прежде чем продолжать, завершите урок 1.

<a id="get-the-tools" class="xliff"></a>

### Получить инструменты
Установите инструменты и программное обеспечение на главном компьютере.

*Предполагаемое время выполнения: 20 минут*

Ознакомьтесь со статьей [Get the tools](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md) (Получение инструментов).

<a id="create-an-iot-hub-and-register-your-device" class="xliff"></a>

### Создание Центра Интернета вещей и регистрация устройства
Создайте группу ресурсов, подготовьте Центр Интернета вещей Azure и добавьте в него первое устройство, используя Azure CLI.

*Предполагаемое время выполнения: 10 минут*

Перейдите к разделу [Создание Центра Интернета вещей и регистрация устройства](iot-hub-gateway-kit-c-sim-lesson2-register-device.md).

<a id="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub" class="xliff"></a>

## Урок 3. Получение сообщений из имитации устройства и чтение сообщений из вашего Центра Интернета вещей
В ходе этого урока вы используете сценарии для автоматизации настройки и выполнения приложения для имитации устройства в шлюзе. Приложение для имитации устройства создает пример данных температуры и отправляет его в модуль Центра Интернета вещей. Модуль Центра Интернета вещей упаковывает полученные данные и отправляет их в Центр Интернета вещей через платформу шлюза, предоставленную в Edge Интернета вещей Azure.

![Урок 3. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

<a id="configure-and-run-a-simulated-device" class="xliff"></a>

### Настройка и запуск имитации устройства
Подготовьте примеры кодов. Затем настройте и запустите пример приложения для имитации устройства.

*Предполагаемое время выполнения: 15 минут.*

Перейдите к статье [Настройка и запуск имитации устройства](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).

<a id="read-messages-from-your-iot-hub" class="xliff"></a>

### Чтение сообщений из Центра Интернета вещей
Запустите пример кода на главном компьютере для чтения сообщений из Центра Интернета вещей.

*Предполагаемое время выполнения: 15 минут.*

Перейдите к статье [Чтение сообщений из Центра Интернета вещей](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md).

<a id="lesson-4-save-messages-to-azure-table-storage" class="xliff"></a>

## Урок 4. Сохранение сообщений в Хранилище таблиц Azure
Создайте приложение-функцию Azure, которое получает входящие сообщения из Центра Интернета вещей и записывает их в Хранилище таблиц Azure.

![Урок 4. Сквозная схема](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

<a id="create-an-azure-function-app-and-azure-storage-account" class="xliff"></a>

### Создание учетной записи хранения Azure и приложения-функции Azure
С помощью шаблона Azure Resource Manager создайте приложение-функцию Azure и учетную запись хранения Azure.

*Предполагаемое время выполнения: 10 минут*

Перейдите к статье [Создание приложения-функции Azure и учетной записи хранения Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md).

<a id="read-messages-persisted-in-azure-table-storage" class="xliff"></a>

### Чтение сообщений, сохраненных в Хранилище таблиц Azure
Отслеживайте сообщения, передаваемые из шлюза в облако, по мере их записывания в Хранилище таблиц Azure.

*Предполагаемое время выполнения: 5 минут*

Ознакомьтесь со статьей [Чтение сообщений, сохраненных в Хранилище таблиц Azure](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md).

<a id="troubleshooting" class="xliff"></a>

## Устранение неполадок
Если в ходе какого-либо урока у вас возникнут проблемы, то решение можно найти в статье [Troubleshooting](iot-hub-gateway-kit-c-sim-troubleshooting.md) (Устранение неполадок).

<a id="explore-more" class="xliff"></a>

## Изучить подробнее
Дополнительные сведения см. на странице сообщества [Developer Zone комплекта Intel IoT Gateway](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit).
