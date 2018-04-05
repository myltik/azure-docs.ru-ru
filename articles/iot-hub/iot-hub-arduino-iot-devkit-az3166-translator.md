---
title: Создание переводчика IoT DevKit при помощи решения "Функции Azure" и Cognitive Services | Документация Майкрософт
description: Использование микрофона в IoT DevKit для получения голосовых сообщений и их преобразование в текст на английском языке при помощи Azure Cognitive Services.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hube
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: d17f117d71eb0616201df18aea6dc48749ae24a8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Использование IoT DevKit AZ3166 с решением "Функции Azure" и Cognitive Services для создания переводчика

Из этой статьи вы узнаете, как создать переводчик на основе IoT DevKit с использованием [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Это средство записывает голосовое сообщение, которое преобразуется в текст на английском языке, отображающийся на экране DevKit.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) — это универсальная совместимая с Arduino плата со множеством периферийных устройств и датчиков. Вы можете выполнить для нее разработку с помощью [расширения Visual Studio Code для Arduino](https://aka.ms/arduino). Она предоставляется с расширяющимся [каталогом проектом](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/), чтобы помочь вам выполнить прототипирование решений Интернета вещей, использующих службы Microsoft Azure.

## <a name="what-you-need"></a>Необходимые элементы

Выполните указания в [руководстве по началу работы](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started), чтобы перейти к таким этапам:

* Подключение DevKit к сети Wi-Fi.
* Подготовка среды разработки

Активная подписка Azure. Если у вас еще нет ее, вы можете зарегистрироваться одним из двух способов:

* Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azure.microsoft.com/en-us/free/).
* Запросите [деньги на счете в Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), если у вас есть подписка MSDN или Visual Studio.

## <a name="step-1-open-the-project-folder"></a>Шаг 1. Открытие папки проекта

### <a name="a-start-vs-code"></a>О. Запуск VS Code

- Убедитесь, что плата DevKit не подключена к компьютеру.
- Запуск VS Code
- Подключите DevKit на компьютере.

VS Code автоматически найдет ее и отобразится вводная страница.

![Страница "Введение"](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_start.png)

### <a name="b-open-the-arduino-examples-folder"></a>B. Открытие папки с примерами Arduino

Справа последовательно выберите **Arduino Examples (Примеры Arduino) > Examples for MXCHIP AZ3166 (Примеры для MXCHIP AZ3166) > AzureIoT** и щелкните **DevKitTranslator**. Откроется новое окно VS Code с папкой проекта DEVKITTRANSLATOR.

![Примеры IoT DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

Если вы закрыли эту область, вы можете открыть ее снова. Нажмите `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) для вызова палитры команд. Введите **Arduino**, затем найдите и выберите **Arduino: Examples** (Arduino: примеры).

## <a name="step-2-provision-azure-services"></a>Шаг 2. Подготовка служб Azure

В окне решений введите `Ctrl+P` (macOS: `Cmd+P`), а затем — `task cloud-provision`.

В терминале VS Code с помощью указаний в интерактивной командной строке подготовьте все необходимые службы Azure:

![Задача подготовки облака](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>Шаг 3. Развертывание решения "Функции Azure"

Чтобы запустить `task cloud-deploy` для развертывания кода решения "Функции Azure", используйте `Ctrl+P` (macOS: `Cmd+P`). Обычно этот процесс занимает от 2 до 5 минут:

![Задача развертывания облака](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

После успешного развертывания решения "Функции Azure" укажите имя приложения-функции в файле azure_config.h. Имя можно найти на [портале Azure](https://portal.azure.com/):

![Расположение имени приложения-функция Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Если решение "Функции Azure" работает неправильно, просмотрите раздел [часто задаваемых вопросов](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function), чтобы устранить проблему.

## <a name="step-4-build-and-upload-the-device-code"></a>Шаг 4. Создание и передача кода устройства

1. Запустите `task config-device-connection` при помощи `Ctrl+P` (macOS: `Cmd+P`).

2. В терминале отобразится запрос на использование строки подключения, полученной на шаге выполнения `task cloud-provision`. Также вы можете ввести в строке подключения устройства другую необходимую команду, выбрав для этого **Create New...** (Создать).

3. Терминал предложит перейти в режим настройки. Чтобы сделать это, удерживая нажатой кнопку "A", нажмите и отпустите кнопку "Reset" (Сброс). На экране появится идентификатор DevKit и надпись "Configuration" (Настройка).
  ![Проверка и передача эскиза Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. После выполнения `task config-device-connection` нажмите клавишу `F1`, чтобы загрузить команды, и выберите `Arduino: Upload`. VSCode начнет проверку и отправку эскиза Arduino: ![Проверка и отправка эскиза Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

## <a name="test-the-project"></a>Тестирование проекта

После инициализации приложения следуйте инструкциям на экране DevKit. По умолчанию исходный язык — китайский.

Чтобы выбрать другой язык для перевода:

1. Нажмите кнопку A, что перейти в режим настройки.
2. Нажмите кнопку B, чтобы прокрутить все поддерживаемые исходные языки.
3. Нажмите кнопку A, чтобы подтвердить выбор исходного языка.
4. Нажмите и удерживайте кнопку B при произнесении фразы, затем отпустите кнопку B, чтобы начать перевод.
5. На экране появится текст, переведенный на английский язык.

![Прокрутка для выбора языка](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Результат перевода](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

На экране результатов перевода можно выполнять следующие действия:

- Нажмите кнопки A и B, чтобы прокрутить список исходных языков и выбрать нужный.
- Нажмите кнопку B при произнесении фразы, а затем отпустите ее, чтобы отправить данные голосового ввода и получить переведенный текст.

## <a name="how-it-works"></a>Принцип работы

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

В эскизе Arduino записывается ваш голос. Затем отправляется HTTP-запрос для активации решения "Функции Azure". В решении "Функции Azure" для перевода вызывается API переводчика речи Cognitive Services. Когда в решение "Функции Azure" поступает текст перевода, на устройство отправляется сообщение C2D. После этого перевод отображается на экране.

## <a name="change-device-id"></a>Изменение идентификатора устройства

**AZ3166** — это стандартный идентификатор устройства, зарегистрированный в Центре Интернета вещей Azure. Если вы хотите его изменить, выполните [эти инструкции](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, то можете ознакомиться с [часто задаваемыми вопросами](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или обратиться к нам по одному из таких каналов:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Дальнейшие действия

Теперь плата IoT DevKit настроена для перевода при помощи решения "Функции Azure" и Cognitive Services. Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * использовать Visual Studio Code, чтобы автоматизировать подготовку облака;
> * настроить строку подключения для устройства Интернета вещей Azure;
> * развернуть решение "Функции Azure";
> * протестировать перевод голосовых сообщений.

Перейдите к следующему руководству:

> [!div class="nextstepaction"]
> [Подключение IoT DevKit AZ3166 к Azure IoT Suite для удаленного мониторинга](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)