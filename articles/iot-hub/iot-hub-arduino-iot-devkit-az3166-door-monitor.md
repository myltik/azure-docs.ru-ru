---
title: Получение сообщения электронной почты в случае открытия двери с помощью службы SendGrid и решения "Функции Azure" | Документация Майкрософт
description: Наблюдайте за магнитным датчиком, чтобы обнаружить, когда дверь открыта, и используйте решение "Функции Azure" для отправки уведомления по электронной почте.
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
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: ba02e877fe7152d759085e3d25090459cd2459fc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="door-monitor"></a>Мониторинг двери          

Плата MXChip IoT DevKit содержит встроенный магнитный датчик. В этом проекте необходимо обнаружить присутствие или отсутствие поблизости мощного магнитного поля, которое в этом случае исходит от небольшого постоянного магнита.

## <a name="what-you-learn"></a>Что вы узнаете

В этом проекте вы узнаете:
- как использовать магнитный датчик MXChip IoT DevKit для обнаружения движения расположенного рядом магнита;
- как использовать службу SendGrid для отправки уведомления на адрес электронной почты.

> [!NOTE]
> Для практического использования этого проекта:
> - установите магнит на краю двери;
> - установите DevKit на дверной косяк рядом с магнитом. Открытие и закрытие двери активирует датчик, что приведет к получению уведомления о событии на электронную почту.

## <a name="what-you-need"></a>Необходимые элементы

Выполните указания в [руководстве по началу работы]({{"/docs/get-started/" | absolute_url }}), чтобы перейти к таким этапам:

* Подключение DevKit к сети Wi-Fi.
* Подготовка среды разработки

Активная подписка Azure. Если у вас еще нет ее, вы можете зарегистрироваться одним из этих способов:

* Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azure.microsoft.com/en-us/free/).
* Запросите [деньги на счете в Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), если у вас есть подписка MSDN или Visual Studio.

## <a name="deploy-sendgrid-service-in-azure"></a>Развертывание службы SendGrid в Azure

[SendGrid](https://sendgrid.com/) — это облачная платформа отправки сообщений электронной почты. Эта служба будет использоваться для отправки уведомлений по электронной почте.

> [!NOTE]
> Если служба SendGrid уже развернута, можно перейти напрямую к разделу [Развертывание Центра Интернета вещей в Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Развертывание SendGrid

Чтобы подготовить службы Azure, нажмите кнопку **Развертывание в Azure**. С помощью этой кнопки можно быстро и легко развернуть проекты с открытым исходным кодом в Microsoft Azure.

Нажмите кнопку **Развертывание в Azure** ниже. 

[![Развертывание в Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Затем появится следующая страница.

> [!NOTE]
> Если вы не видите следующую страницу, может потребоваться сначала войти в учетную запись Azure.

Заполните форму регистрации:

  * **Группа ресурсов**. Создайте группу ресурсов для размещения SendGrid или используйте имеющуюся. Дополнительные сведения см. в статье [Управление ресурсами Azure через портал](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal).

  * **Имя**. Имя службы SendGrid. Выберите уникальное имя, отличающееся от других служб, которые могут быть у вас.

  * **Пароль**. Службе необходим пароль, не используемый где-либо в этом проекте.

  * **Адрес электронной почты**. Служба SendGrid отправит запрос на проверку на этот адрес электронной почты.

  > [!NOTE]
  > Установите флажок **Закрепить на панели мониторинга**, чтобы упростить поиск этого приложения в будущем.
 
![Развертывание SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Создание ключа API SendGrid

После успешного завершения развертывания щелкните его и нажмите кнопку **Управление**. Вы перейдете на страницу SendGrid, где необходимо подтвердить адрес электронной почты.

![Управление SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

На странице SendGrid щелкните **Параметры** > **Ключи API** > **Создание ключа API**. Введите **имя ключа API** и щелкните **Create & View** (Создать и просмотреть).

![Первый снимок экрана создания API SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![Второй снимок экрана создания API SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Ключ API отображается только один раз. Не забудьте скопировать и сохранить его в надежном месте, так как он используется в следующем шаге.

## <a name="deploy-iot-hub-in-azure"></a>Развертывание Центра Интернета вещей в Azure

Ниже показано, как подготовить другие связанные с Центром Интернета вещей службы и развернуть решение "Функции Azure" для этого проекта.

Нажмите кнопку **Развертывание в Azure** ниже. 

[![Развертывание в Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Затем появится следующая страница.

> [!NOTE]
> Если вы не видите следующую страницу, может потребоваться сначала войти в учетную запись Azure.

Заполните форму регистрации:

  * **Группа ресурсов**. Создайте группу ресурсов для размещения SendGrid или используйте имеющуюся. Дополнительные сведения см. в статье [Управление ресурсами Azure через портал](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal).

  * **Имя Центра Интернета вещей**. Имя Центра Интернета вещей. Выберите уникальное имя, отличающееся от других служб, которые могут быть у вас.

  * **Iot Hub Sku** (SKU Центра Интернета вещей). F1 (ограничено, по одному на подписку) является бесплатным. Дополнительные сведения о ценах см. на странице о [ценовых категориях и категориях масштабирования](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **Электронная почта отправителя**. Это должен быть тот же адрес электронной почты, который использовался при настройке службы SendGrid.

  > [!NOTE]
  > Установите флажок **Закрепить на панели мониторинга**, чтобы упростить поиск этого приложения в будущем.
 
![Развертывание Центра Интернета вещей](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Создание и передача кода

### <a name="start-vs-code"></a>Запуск VS Code

- Убедитесь, что плата DevKit **не** подключена к компьютеру.
- Запустите VSCode.
- Подключите DevKit на компьютере.

VS Code автоматически обнаружит DevKit и откроет вводную страницу:

![VSCode](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-start.png)

> [!NOTE]
> При запуске VS Code может появиться сообщение об ошибке о том, что не обнаружена среда IDE Arduino или связанный пакет платы. Если возникла эта ошибка, закройте VS Code, запустите интегрированную среду разработки Arduino, и VS Code правильно определит путь к ней.

### <a name="open-arduino-examples-folder"></a>Открытие папки с примерами Arduino

Разверните раздел **ARDUINO EXAMPLES** (ПРИМЕРЫ ARDUINO) слева, перейдите в папку **Examples for MXCHIP AZ3166 (Примеры для MXCHIP AZ3166) > AzureIoT** и щелкните **DoorMonitor**. Откроется новое окно VSCode с папкой проекта.

![Минирешение: примеры](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Если вы закрыли эту область, вы можете открыть ее снова. Нажмите `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) для вызова палитры команд. Введите **Arduino**, затем найдите и выберите **Arduino: Examples** (Arduino: примеры).

### <a name="provision-azure-services"></a>Подготовка служб Azure

В окне решения выполните задачу подготовки в облаке:
- нажмите клавиши `Ctrl+P` (macOS: `Cmd+P`);
- введите `task cloud-provision` в соответствующем тестовом поле.

В терминале VS Code с помощью указаний в интерактивной командной строке подготовьте необходимые службы Azure. Выберите те же элементы из показанного списка, которые вы предоставили ранее в разделе [Развертывание Центра Интернета вещей в Azure](#deploy-iot-hub-in-azure).

![Подготовка в облаке](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Если страница зависает в состоянии загрузки на этапе входа в Azure, ознакомьтесь с разделом [Page hangs when log in Azure](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) (Страница зависает при входе в Azure). 

### <a name="build-and-upload-the-device-code"></a>Создание и передача кода устройства

#### <a name="windows"></a>Windows

1. Нажмите `Ctrl+P`, чтобы запустить задачу `task device-upload`.
2. Терминал предложит перейти в режим настройки. Чтобы сделать это, удерживая нажатой кнопку "A", нажмите и отпустите кнопку "Reset" (Сброс). На экране отображается идентификационный номер DevKit и слово *Configuration*.

Эта процедура задает строку подключения, полученную на шаге [подготовки служб Azure](#provision-azure-services).

Теперь VS Code начинает проверку и отправку эскиза Arduino на DevKit.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

> [!NOTE]
> В некоторых случаях может появиться сообщение об ошибке "Error: AZ3166: Unknown package" (Ошибка AZ3166: неизвестный пакет). Такая ошибка возникает, если индекс пакета платы неправильно обновлен. Чтобы устранить эту ошибку, ознакомьтесь с этими [часто задаваемыми вопросами](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Переведите плату DevKit в режим настройки. Для этого, удерживая нажатой кнопку A, нажмите и отпустите кнопку сброса. На экране отобразится надпись "Configuration" (Настройка).
2. Нажмите `Cmd+P`, чтобы запустить задачу `task device-upload`.

Эта процедура задает строку подключения, полученную на шаге [подготовки служб Azure](#provision-azure-services).

Теперь VS Code начинает проверку и отправку эскиза Arduino на DevKit.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

> [!NOTE]
> В некоторых случаях может появиться сообщение об ошибке "Error: AZ3166: Unknown package" (Ошибка AZ3166: неизвестный пакет). Такая ошибка возникает, если индекс пакета платы неправильно обновлен. Чтобы устранить эту ошибку, ознакомьтесь с этими [часто задаваемыми вопросами](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Тестирование проекта

Изначально программа инициализируется, когда DevKit находится в присутствии стабильного магнитного поля.

После инициализации на экране отображается `Door closed`. При изменении в магнитном поле состояние изменяется на `Door opened`. При каждом изменении состояния двери вы получаете уведомление по электронной почте. (Получение этих сообщений электронной почты может занять до пяти минут.)

![Магниты близко к датчику: дверь закрыта](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Магниты близко к датчику: дверь закрыта")

![Магниты отдалились от датчика: дверь открыта](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Магниты отдалились от датчика: дверь открыта")

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, то можете ознакомиться с [часто задаваемыми вопросами](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или попросить помощь по одному из таких каналов:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как подключить устройство DevKit к Azure IoT Suite и как использовать службу SendGrid для отправки сообщений электронной почты. Ниже приведены предлагаемые дальнейшие действия:

* [Обзор общих сведений об Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Microsoft IoT Central application](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit) (Подключение устройства MXChip IoT DevKit к приложению Microsoft IoT Central)
