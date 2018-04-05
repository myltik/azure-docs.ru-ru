---
title: Получение сообщений из Twitter с помощью решения "Функции Azure" | Документация Майкрософт
description: Используйте датчик движения для отслеживания встряхиваний и извлекайте случайный твит с выбранным хэштегом с помощью решения "Функции Azure".
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
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: a84393c5c53b8f8e4a8b688a462f433b2d611b0e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Shake, Shake: получение сообщений из Twitter с помощью решения "Функции Azure"

В этом проекте вы научитесь использовать датчик движения для активации событий с помощью решения "Функции Azure" Это приложение извлекает случайный твит с определенным хэштегом, который вы настроите в эскизе Arduino. Затем оно выводит твит на экран DevKit.

## <a name="what-you-need"></a>Необходимые элементы

Выполните указания в [руководстве по началу работы](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started), чтобы перейти к таким этапам:

* Подключение DevKit к сети Wi-Fi.
* Подготовка среды разработки.

Активная подписка Azure. Если у вас ее нет, зарегистрируйтесь одним из следующих способов:

* Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azure.microsoft.com/en-us/free/).
* Запросите [деньги на счете в Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), если у вас есть подписка MSDN или Visual Studio.

## <a name="open-the-project-folder"></a>Открытие папки проекта

### <a name="start-vs-code"></a>Запуск VS Code

- Убедитесь, что плата DevKit **не** подключена к компьютеру.
- Запустите VSCode.
- Подключите DevKit на компьютере.

VS Code автоматически найдет DevKit и отобразит вводную страницу.

![Минирешение: VS Code](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_start.png)

> [!NOTE]
> При запуске VS Code может появиться сообщение об ошибке о том, что не обнаружена среда IDE Arduino или связанный пакет платы. В этом случае закройте VS Code и снова запустите среду IDE Arduino. Теперь VS Code правильно определит путь к ней.

### <a name="open-arduino-examples-folder"></a>Открытие папки с примерами Arduino

Разверните раздел **Arduino Examples** (Примеры Arduino) слева, перейдите в папку **Examples for MXCHIP AZ3166 (Примеры для MXCHIP AZ3166) > AzureIoT** и выберите **ShakeShake**. Откроется новое окно VS Code с папкой проекта.

![Минирешение: примеры](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Если вы закрыли эту область, вы можете открыть ее снова. Нажмите `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) для вызова палитры команд. Введите **Arduino**, затем найдите и выберите **Arduino: Examples** (Arduino: примеры).

## <a name="provision-azure-services"></a>Подготовка служб Azure

В окне решения запустите свою задачу с помощью клавиш `Ctrl+P` (macOS: `Cmd+P`), введя `task cloud-provision`.

В терминале VS Code с помощью указаний в интерактивной командной строке подготовьте необходимые службы Azure.

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Если страница зависает в состоянии загрузки на этапе входа в Azure, ознакомьтесь с [часто задаваемыми вопросами] ({{"/docs/faq/#page-hangs-when-log-in-azure" | 
 
## <a name="modify-the-hashtag"></a>Изменение хэштега

Откройте `ShakeShake.ino` и найдите следующую строку кода:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Замените строку `iot` в фигурных скобках любым интересующим вас хэштегом. DevKit будет извлекать случайные твиты, содержащие хэштег, указанный вами на этом шаге.

## <a name="deploy-azure-functions"></a>Развертывание решения "Функции Azure"

Чтобы запустить `task cloud-deploy` и начать развертывание кода решения "Функции Azure", используйте команду `Ctrl+P` (`Cmd+P` в macOS).

![cloud-deploy](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Иногда функция Azure может выполняться неправильно. Если возникнет такая проблема, ознакомьтесь со [списком часто задаваемых вопросов](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Создание и передача кода устройства

### <a name="windows"></a>Windows

1. Нажмите `Ctrl+P`, чтобы запустить задачу `task device-upload`.
2. Терминал предложит перейти в режим настройки. Для этого выполните следующие действия:

   * Нажмите и удерживайте кнопку A.
   * Нажмите и отпустите кнопку сброса.

3. На экране появится идентификатор DevKit и надпись "Configuration" (Настройка).
4. Это действие задает строку подключения, полученную при выполнении команды `task cloud-provision`.
5. Теперь VS Code начинает проверку и отправку эскиза Arduino на DevKit (![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)).
6. Плата DevKit перезагрузится и начнет выполнение кода.

> [!NOTE]
> Может появиться сообщение об ошибке "Error: AZ3166: Unknown package" (Ошибка AZ3166: неизвестный пакет). Такая ошибка возникает, если индекс пакета платы неправильно обновлен. Чтобы устранить ее, ознакомьтесь со [списком часто задаваемых вопросов](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

### <a name="macos"></a>macOS

1. Переведите плату DevKit в режим настройки. Для этого, удерживая нажатой кнопку A, нажмите и отпустите кнопку сброса. На экране отобразится надпись "Configuration" (Настройка).
2. С помощью `Cmd+P` запустите `task device-upload`, чтобы задать строку подключения, полученную на этапе `task cloud-provision`.
3. Теперь VS Code начинает проверку и отправку эскиза Arduino на DevKit (![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)).
4. Плата DevKit перезагрузится и начнет выполнение кода.

> [!NOTE]
> Может появиться сообщение об ошибке "Error: AZ3166: Unknown package" (Ошибка AZ3166: неизвестный пакет). Такая ошибка возникает, если индекс пакета платы неправильно обновлен. Чтобы устранить ее, ознакомьтесь со [списком часто задаваемых вопросов](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Тестирование проекта

Когда инициализация приложения завершится, отпустите кнопку A и аккуратно встряхните плату DevKit. Произойдет получение случайного твита с хэштегом, который вы указали ранее. Через несколько секунд этот твит отобразится на экране DevKit:

### <a name="arduino-application-initializing"></a>Инициализация приложения Arduino...
![Инициализация приложения Arduino](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Нажмите кнопку A, чтобы встряхнуть...
![Нажмите кнопку A, чтобы встряхнуть](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Готов к встряхиванию...
![Готов к встряхиванию](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Обработка...
![Обработка](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Нажмите кнопку B, чтобы прочитать...
![Нажмите кнопку B, чтобы прочитать](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Отображение случайного твита...
![Отображение случайного твита](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Снова нажмите кнопку A и повторно встряхните устройство, чтобы получить новый твит.
- С помощью кнопки B вы можете прокрутить твит до конца.

## <a name="how-it-works"></a>Принцип работы

![Схема](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Эскиз Arduino отправляет событие в Центр Интернета вещей Azure. Это событие запускает приложение решения "Функции Azure". Приложение решения "Функции Azure" содержит логику для подключения к API Twitter и получения твитов. Полученный твит упаковывается в сообщение C2D (из облака на устройство) и отправляется на вызывающее устройство.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Необязательный шаг: настройка собственного маркера носителя Twitter

Для тестирования в этом примере используется предварительно настроенный тестовый маркер носителя Twitter. Но для каждой учетной записи Twitter существует [ограничение скорости](https://dev.twitter.com/rest/reference/get/search/tweets). Если вы хотите применить собственный маркер, выполните следующие действия:

1. Откройте [портал Twitter для разработчиков](https://dev.twitter.com/), чтобы зарегистрировать новое приложение Twitter.

2. [Получите ключ и секреты объекта-получателя](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) для этого приложения.

3. С помощью подходящей [служебной программы](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) создайте из этих двух ключей маркер носителя Twitter.

4. На [портале Azure](https://portal.azure.com/){:target="_blank"} откройте **группу ресурсов** и найдите функцию Azure (тип: служба приложений) для проекта "Shake, Shake". Это имя обязательно содержит строку "shake...".
  ![Функция Azure](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Измените код `run.csx` в разделе **Функции > shakeshake-cs**, включив в него собственный маркер:
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Сохраните файл и щелкните **Запустить**.


## <a name="problems-and-feedback"></a>Проблемы и обратная связь

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>Все этапы выполнены успешно, но на экране отображается "No Tweets" (Нет твитов)

Это обычно происходит при первом запуске примера после развертывания, поскольку для "холодного" запуска приложения-функции нужно от нескольких секунд до целой минуты. Иногда при выполнении кода могут возникать сбои, приводящие к перезапуску приложения. В таких случаях приложению устройства потребуется некоторое время для получения очередного твита. Чтобы устранить такую проблему, примените любой из следующих методов или оба сразу:

1. Нажмите на DevKit кнопку сброса, чтобы перезапустить приложение устройства.

2. Найдите созданное приложение решения "Функции Azure" на [портале Azure](https://portal.azure.com/) и перезапустите его: ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png).

### <a name="feedback"></a>Отзыв

Если вы столкнулись с другими проблемами, изучите [часто задаваемые вопросы](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или свяжитесь с нами по любому из доступных каналов:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Дополнительная информация

Теперь вы знаете, как подключить устройство DevKit к Azure IoT Suite и получить твит, а значит вы готовы к следующему шагу.

* [Обзор общих сведений об Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)