---
title: 'IoT DevKit в облаке: подключение MXChip IoT DevKit к Центру Интернета вещей Azure | Документация Майкрософт'
description: В этом учебнике вы узнаете, как отправлять данные о состоянии датчиков IoT DevKit AZ3166 в Azure IoT Suite для мониторинга и визуализации.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 17b24244180436c2d1f97433e13a530dae565a10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-suite-for-remote-monitoring"></a>Подключение MXChip IoT DevKit к Azure IoT Suite для удаленного мониторинга

В этом учебнике вы узнаете, как запустить пример приложения с помощью своего набора разработки, чтобы обеспечить отправку данных датчиков в Azure IoT Suite.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) — это универсальная совместимая с Arduino плата со множеством периферийных устройств и датчиков. Вы можете выполнить для нее разработку с помощью [расширения Visual Studio Code для Arduino](https://aka.ms/arduino). Она предоставляется с расширяющимся [каталогом проектом](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/), чтобы помочь вам выполнить прототипирование решений Интернета вещей, использующих службы Microsoft Azure.

## <a name="what-you-need"></a>Необходимые элементы

Выполните указания в [руководстве по началу работы](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started), чтобы перейти к таким этапам:

* Подключение DevKit к сети Wi-Fi.
* Подготовка среды разработки

Активная подписка Azure. Если у вас еще нет ее, вы можете зарегистрироваться одним из двух способов:

* Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azure.microsoft.com/free/).
* Запросите [деньги на счете в Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), если у вас есть подписка MSDN или Visual Studio.

## <a name="create-an-azure-iot-suite"></a>Создание Azure IoT Suite

1. Перейдите на [сайт Azure IoT Suite](https://www.azureiotsuite.com/) и щелкните консоль **Создать решения**.
  ![Выбор типа Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)
  > [!WARNING]
  > По умолчанию в этом примере создается Центр Интернета вещей S2 после создания IoT Suite S2. Если этот Центр используют немного устройств, рекомендуем перейти с использования S2 на S1 и удалить IoT Suite, чтобы связанный Центр Интернета вещей можно было также удалить, если он больше не будет нужен. 

2. Щелкните **Удаленный мониторинг**.

3. Введите имя решения, выберите подписку и регион, а затем щелкните **Создать решение**. Подготовка решения к работе может занять некоторое время.
  ![Создание решения](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. По завершении подготовки нажмите кнопку **Launch** (Запуск). Во время подготовки для решения создаются имитированные устройства. Щелкните вкладку **Devices** (Устройства), чтобы просмотреть их список. ![Панель мониторинга](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  ![Консоль](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Щелкните **Add a device** (Добавить устройство).

6. Нажмите кнопку **Add New** (Добавить новое) в разделе **Пользовательское устройство**.
  ![Добавление нового устройства](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Установите переключатель **Позвольте мне определить собственный идентификатор устройства**, введите `AZ3166`, а затем нажмите кнопку **Создать**.
  ![Создание устройства с использованием идентификатора](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Запомните или запишите параметр **Имя узла Центра Интернета вещей** и нажмите кнопку **Готово**.

## <a name="open-the-remotemonitoring-sample"></a>Открытие примера RemoteMonitoring

1. Отключите DevKit на компьютере, если набор подключен.

2. Запустите VSCode.

3. Подключите DevKit на компьютере. VSCode автоматически обнаружит ваш DevKit и откроет следующие страницы:
  * Страница введения в набор разработки.
  * Примеры Arduino: практические примеры по началу работы с набором разработки.

4. Разверните раздел **Arduino Examples** (Примеры Arduino) слева, перейдите в папку **Examples for MXCHIP AZ3166 (Примеры для MXCHIP AZ3166) > AzureIoT** и щелкните **RemoteMonitoring**. Откроется новое окно VSCode с папкой проекта.
  > [!NOTE]
  > Если вы закрыли эту область, вы можете открыть ее снова. Нажмите `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) для вызова палитры команд. Введите **Arduino**, затем найдите и выберите **Arduino: Examples** (Arduino: примеры).

## <a name="provision-required-azure-services"></a>Подготовка необходимых служб Azure

В окне решения запустите свою задачу: нажмите клавиши `Ctrl+P` (в macOS — `Cmd+P`) и введите `task cloud-provision` в текстовом поле:

В терминале VS Code с помощью указаний в интерактивной командной строке подготовьте необходимые службы Azure.

![Подготовка ресурсов Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Создание и передача кода устройства

1. Нажмите клавиши `Ctrl+P` (в macOS — `Cmd + P`) и введите **task config-device-connection**.

2. В терминале отобразится запрос на использование строки подключения, полученной на шаге выполнения `task cloud-provision`. Вы можете также ввести в строке подключения устройства другую необходимую команду, щелкнув для этого "Create New..." ("Создать").

3. Терминал предложит перейти в режим настройки. Чтобы сделать это, удерживая нажатой кнопку "A", нажмите и отпустите кнопку "Reset" (Сброс). На экране появится идентификатор DevKit и надпись "Configuration" (Настройка).
  ![Ввод в строке подключения](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. После выполнения `task config-device-connection` нажмите клавишу `F1`, чтобы загрузить команды, и выберите `Arduino: Upload`. VSCode начнет проверку и отправку эскиза Arduino: ![Проверка и отправка эскиза Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

## <a name="test-the-project"></a>Тестирование проекта

При запуске примера приложения DevKit отправляет данные датчиков по сети Wi-Fi в Azure IoT Suite. Чтобы просмотреть результат, сделайте следующее:

1. Перейдите в Azure IoT Suite и щелкните вкладку **Dashboard** (Панель мониторинга).

2. В консоли решения Azure IoT Suite будет отображаться состояние датчиков DevKit.

![Данные датчиков в Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Изменение идентификатора устройства

Идентификатор устройства в Центре Интернета вещей можно изменить, выполнив шаги в [этом руководстве](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). И если вы хотите изменить жестко закодированное значение **AZ3166** на другой идентификатор устройства, [вот](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23) строка кода, в которой это можно сделать.

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, то можете ознакомиться с [часто задаваемыми вопросами](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или обратиться к нам по одному из таких каналов:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы знаете, как подключить устройство DevKit к Azure IoT Suite и визуализировать данные датчиков, можно выполнить:

* [Обзор общих сведений об Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Microsoft IoT Central application](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit) (Подключение устройства MXChip IoT DevKit к приложению Microsoft IoT Central)
