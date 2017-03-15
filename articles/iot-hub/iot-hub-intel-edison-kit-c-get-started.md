---
title: "Подключение Intel Edison (C) к Интернету вещей Azure. Приступая к работе | Документация Майкрософт"
description: "Приступите к работе с Intel Edison, создайте свой Центр Интернета вещей Azure и подключите к нему устройство Edison"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "разработка для Intel Edison, Центр Интернета вещей Azure, начало работы с Интернетом вещей, руководство по Интернету вещей, Интернет вещей Adafruit, Intel Edison Arduino, начало работы с Arduino"
ms.assetid: 4885fa2c-c2ee-4253-b37f-ccd55f92b006
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/7/2016
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: 248bc4c309ed61ae00ef144ebdb4c820f285783d
ms.lasthandoff: 03/02/2017


---
# <a name="connect-your-intel-edison-device-to-your-iot-hub-using-c"></a>Подключение устройства Intel Edison к Центру Интернета вещей с помощью C
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-intel-edison-kit-node-get-started.md)
> * [C](iot-hub-intel-edison-kit-c-get-started.md)

В этом руководстве вы начнете с того, что узнаете основы работы с Intel Edison. Также вы узнаете, как можно легко подключать устройства к облаку с помощью [Центра Интернета вещей Azure](iot-hub-what-is-iot-hub.md).

Нет начального набора? Начните [здесь](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="lesson-1-configure-your-device"></a>Урок 1. Настройка устройства
![Урок 1. Сквозная схема](media/iot-hub-intel-edison-lessons/e2e-lesson1.png)

В ходе этого урока вы настроите операционную систему на устройстве Intel Edison и среду разработки, а также развернете приложение на устройстве Edison.

### <a name="configure-your-device"></a>Настройка устройства
Настройте Intel Edison для первого использования: соберите плату, подключите ее и установите на компьютере инструмент для настройки, с помощью которого установите встроенное ПО Intel Edison, настроите пароль и подключите устройство к Wi-Fi.  

*Предполагаемое время выполнения: 30 минут*

Ознакомьтесь со статьей [Настройка устройства ][configure-your-device].

### <a name="get-the-tools"></a>Получить инструменты
Скачайте инструменты и программное обеспечение для сборки и развертывания приложения на устройстве Intel Edison.

*Предполагаемое время выполнения: 20 минут*

Ознакомьтесь со статьей [Get the tools][get-the-tools] (Получение инструментов).

### <a name="create-and-deploy-the-blink-application"></a>Создание и развертывание приложения для включения индикатора
Клонируйте пример приложения для включения индикатора c GitHub и разверните его с помощью инструмента Gulp на плате Intel Edison. Это приложение будет каждые две секунды включать и выключать светодиодный индикатор на компьютере.

*Предполагаемое время выполнения: 5 минут*

Ознакомьтесь со статьей [Создание и развертывание приложения для включения индикатора][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Урок 2. Создание Центра Интернета вещей
![Урок 2. Сквозная схема](media/iot-hub-intel-edison-lessons/e2e-lesson2.png)

В ходе этого урока вы создадите бесплатную учетную запись Azure, подготовите Центр Интернета вещей Azure и создадите в нем устройство.

Прежде чем продолжать, завершите урок 1.

### <a name="get-the-azure-tools"></a>Получение средств Azure
Установите интерфейс командной строки Azure (Azure CLI).

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Get Azure tools][get-azure-tools] (Получение инструментов Azure).

### <a name="create-your-iot-hub-and-register-intel-edison"></a>Создание Центра Интернета вещей и регистрация Intel Edison
Создайте группу ресурсов, подготовьте Центр Интернета вещей Azure и добавьте в него первое устройство, используя Azure CLI.

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Создание Центра Интернета вещей и регистрация Intel Edison](iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md).

## <a name="lesson-3-send-device-to-cloud-messages"></a>Урок 3. Отправка сообщений с устройства в облако
![Урок 3. Сквозная схема](media/iot-hub-intel-edison-lessons/e2e-lesson3.png)

В ходе этого урока вы отправите сообщения c устройства Edison в Центр Интернета вещей. Также вы создадите приложение-функцию Azure, которое получает входящие сообщения из Центра Интернета вещей и записывает их в хранилище таблиц Azure.

Прежде чем продолжить, завершите уроки 1 и 2.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Создание учетной записи хранения Azure и приложения-функции Azure
С помощью шаблона Azure Resource Manager создайте приложение-функцию Azure и учетную запись хранения Azure.

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Создание приложения-функции Azure и учетной записи хранения Azure][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Запуск примера приложения для отправки сообщений с устройства в облако
Разверните и запустите на устройстве Intel Edison пример приложения, отправляющего сообщения в Центр Интернета вещей.

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Run a sample application to send device-to-cloud messages][send-device-to-cloud-messages] (Запуск примера приложения для отправки сообщений с устройства в облако).

### <a name="read-messages-persisted-in-azure-storage"></a>Чтение сообщений, сохраненных в службе хранилища Azure
Отслеживайте сообщения, передаваемые с устройства в облако, по мере их записывания в хранилище Azure.

*Предполагаемое время выполнения: 5 минут*

Ознакомьтесь со статьей [Чтение сообщений, сохраненных в службе хранилища Azure][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Урок 4. Отправка сообщений из облака на устройство
![Урок 4. Сквозная схема](media/iot-hub-intel-edison-lessons/e2e-lesson4.png)

В ходе этого урока демонстрируется способ отправки сообщений из Центра Интернета вещей на устройство Intel Edison. С помощью этих сообщений вы будете управлять включением и отключением светодиодного индикатора, который подключен к устройству Edison. Для выполнения этой задачи подготовлен пример приложения.

Прежде чем продолжить, завершите уроки 1, 2 и 3.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Запуск примера приложения для получения сообщений из облака на устройство
Пример приложения из урока 4 выполняется на устройстве Edison и отслеживает входящие сообщения от Центра Интернета вещей. Новая задача Gulp отправляет сообщения из Центра Интернета вещей на устройство Edison для управления состоянием светодиодного индикатора.

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Запуск примера приложения для получения сообщений из облака на устройство][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Дополнительный раздел. Изменение режима включения и отключения светодиодного индикатора
Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора.

*Предполагаемое время выполнения: 10 минут*

Ознакомьтесь со статьей [Optional section: Change the on and off behavior of the LED][change-the-on-and-off-behavior-of-the-led] (Дополнительный раздел. Изменение режима включения и отключения светодиодного индикатора).

## <a name="troubleshooting"></a>Устранение неполадок
Если в ходе какого-либо урока у вас возникнут проблемы, то решение можно найти в статье [Troubleshooting][troubleshooting] (Устранение неполадок).
<!-- Images and links -->

[configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-c-lesson1-deploy-blink-app.md
[get-azure-tools]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-c-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-c-lesson3-read-table-storage.md
[receive-cloud-to-device-messages]:iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-c-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
