---
title: "IoT DevKit в облаке: подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure | Документация Майкрософт"
description: "Узнайте, как настроить и подключить плату IoT DevKit AZ3166 к Центру Интернета вещей Azure и передавать с нее данные в облако Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 122fac584ac5b954ef7b33a3121bee2c502ebc63
ms.contentlocale: ru-ru
ms.lasthandoff: 08/16/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure в облаке

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Плату [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) можно использовать для разработки и прототипирования решений Интернета вещей, использующих службы Microsoft Azure. Он содержит совместимую с Arduino плату со множеством периферийных устройств и датчиков, пакет ПО для платы с открытым кодом и расширяющийся [каталог проектов](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>В рамках этого руководства мы:
Подключим [плату DevKit](https://microsoft.github.io/azure-iot-developer-kit/) к созданному Центру Интернета вещей Azure, чтобы собирать данные о температуре и влажности с датчиков и отправлять эти данные в Центр Интернета вещей.

У вас еще нет платы DevKit? Вы можете новую плату DevKit [здесь](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Что вы узнаете

* Как подключить плату IoT DevKit к беспроводной точке доступа и подготовить среду разработки.
* Как создать Центр Интернета вещей и зарегистрировать устройство для MXChip IoT DevKit.
* Как собирать данные датчиков, запустив пример приложения на MXChip IoT DevKit.
* Как отправить данные датчиков в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

* Плата MXChip IoT DevKit с кабелем micro USB. Вы можете [получить ее прямо сейчас](https://aka.ms/iot-devkit-purchase).
* Компьютер под управлением Windows 10 или macOS 10.10 (или более поздней версии).
* активная подписка Azure;
  * Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).

## <a name="prepare-your-hardware"></a>Подготовка оборудования

Подключите оборудование к компьютеру.

### <a name="hardware-you-need"></a>Необходимое оборудование

* Плата DevKit.
* Кабель micro USB.

![Приступая к работе: оборудование](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

### <a name="connect-devkit-to-your-computer"></a>Подключение платы DevKit к компьютеру

1. Подключите разъем USB кабеля к компьютеру.
2. Подключите разъем micro USB кабеля к плате DevKit.
3. Зеленый светодиодный индикатор рядом с надписью "Power" (Питание) будет означать, что подключение установлено.

![Приступая к работе: подключение](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wifi"></a>Настройка Wi-Fi

Для работы проектов Центра Интернета вещей требуется подключение к Интернету. Следуйте приведенным ниже инструкциям, чтобы настроить плату DevKit для подключения к сети Wi-Fi.

### <a name="enter-ap-mode"></a>Переключение в режим точки беспроводного доступа

Удерживая нажатой кнопку "B", нажмите и отпустите кнопку "Reset" (Сброс), а затем отпустите кнопку "B". Плата DevKit переключится в режим точки беспроводного доступа для настройки Wi-Fi. На экране будет отображен идентификатор SSID платы DevKit, а также IP-адрес портала настройки.

![Приступая к работе: настройка Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Подключение к точке беспроводного доступа DevKit

Теперь используйте другое устройство с модулем Wi-Fi (компьютер или мобильный телефон), чтобы подключиться к плате DevKit по идентификатору SSID (выделен на снимке экрана выше). Поле пароля оставьте пустым.

![Приступая к работе: идентификатор SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wifi-for-devkit"></a>Настройка Wi-Fi для платы DevKit

Откройте IP-адрес, отображенный на экране DevKit в браузере на компьютере или в мобильном телефоне, выберите сеть Wi-Fi для подключения платы DevKit, а затем введите пароль. Чтобы завершить настройку, щелкните **Connect** (Подключить).

![Приступая к работе: портал настройки Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Через несколько секунд после успешного подключения плата DevKit перезагрузится. Если прошло успешно, на экране появится имя Wi-Fi и IP-адрес.

![Приступая к работе: IP-адрес для Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
IP-адрес на фотографии может не совпадать с фактическим IP-адресом, который будет назначен и отображен на экране DevKit. Это нормально, так как сеть Wi-Fi использует протокол DHCP для динамического назначения IP-адресов.

После настройки Wi-Fi учетные данные для этого подключения сохранятся на устройстве, даже если оно будет отсоединено. Например, если вы настроили плату DevKit для домашней сети Wi-Fi и затем принесли ее на работу, потребуется перенастроить режим точки беспроводного доступа (начиная с шага **Переключение в режим точки беспроводного доступа**), чтобы подключить плату DevKit к сети Wi-Fi в офисе. 

## <a name="start-using-devkit"></a>Начало использования платы DevKit

Приложение по умолчанию, запущенное на плате DevKit, проверит наличие последней версии встроенного ПО и отобразит некоторые диагностические данные датчиков.

### <a name="upgrade-to-the-latest-firmware"></a>Обновление до последней версии встроенного ПО

При необходимости установить обновление на экране будут показаны текущая и последняя версии встроенного ПО. Следуйте указаниям по [обновлению встроенного ПО](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/).

![Приступая к работе: встроенное ПО](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
Это действие выполняется один раз. Когда вы начнете разработку с помощью платы DevKit и передадите свое приложение, вместе с ним будет передана последняя версия встроенного ПО.

### <a name="test-various-sensors"></a>Тестирование различных датчиков

Нажмите кнопку "B", чтобы проверить датчики. Нажимайте и отпускайте кнопку "B", чтобы обойти по очереди все датчики.

![Приступая к работе: датчики](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-development-environment"></a>Подготовка среды разработки

Пришло время настроить среду разработки: инструменты и пакеты для создания отличных приложений Интернета вещей. Можно выбрать версию для Windows или macOS в соответствии с используемой операционной системой.


### <a name="windows"></a>Windows

Мы рекомендуем использовать пакет установки для подготовки среды разработки. В случае каких-либо проблем можно выполнить подготовку [вручную](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/).

#### <a name="download-latest-package"></a>Скачивание последней версии пакета

Скачиваемый файл с расширением `.zip` содержит все инструменты и пакеты, которые необходимы для разработки для платы DevKit.

> [!div class="button"]
[Загрузить](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


> [!NOTE] 
> Файл с расширением `.zip` содержит приведенные ниже инструменты и пакеты. Если вы уже установили некоторые из этих компонентов, сценарий обнаружит их и пропустит.
> * Node.js и Yarn: среда выполнения для сценария установки и автоматизированных задач.
> * [Azure CLI 2.0 (MSI-файл)](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): кроссплатформенный интерфейс командной строки для управления ресурсами Azure. Этот MSI-файл содержит зависимые компоненты Python и pip.
> * [Visual Studio Code](https://code.visualstudio.com/): упрощенный редактор кода для разработки для платы DevKit.
> * [Расширение Visual Studio Code для Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): обеспечивает разработку для Arduino в VS Code.
> * [Интегрированная среда разработки Arduino](https://www.arduino.cc/en/Main/Software): этот инструмент используется расширением Arduino.
> * Пакет ПО для платы DevKit: цепочки инструментов, библиотеки и проекты для DevKit.
> * Служебная программа ST-Link: необходимые служебные программы и драйверы.

#### <a name="run-installation-script"></a>Запуск сценария установки

В проводнике Windows найдите скачанный файл с расширением `.zip` и извлеките его содержимое. Найдите файл `install.cmd`, щелкните его правой кнопкой мыши и выберите **Запустить от имени администратора**, чтобы выполнить его.

![Приступая к работе: запуск от имени администратора](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Во время установки отображается ход ее выполнения для каждого инструмента или пакета.

![Приступая к работе: установка](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="confirm-to-install-drivers"></a>Подтверждение установки драйверов

Расширение VS Code для Arduino использует интегрированную среду разработки Arduino. Если вы впервые устанавливаете интегрированную среду разработки Arduino, то вам будет предложено установить соответствующие драйверы.

![Приступая к работе: драйвер](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Для завершения установки в зависимости от скорости подключения к Интернету требуется около 10 минут. После завершения установки на рабочем столе должны появиться ярлыки Visual Studio Code и интегрированной среды разработки Arduino.

> [!NOTE] 
В некоторых случаях при запуске VS Code появляется сообщение об ошибке. В нем сообщается, что не удается найти интегрированную среду разработки Arduino или связанный пакет ПО для платы. Чтобы устранить эту проблему, закройте VS Code, запустите интегрированную среду разработки Arduino один раз, и VS Code правильно определит путь к ней.


### <a name="macos-preview"></a>macOS (предварительная версия)

Выполните инструкции ниже, чтобы подготовить среду разработки в macOS.

#### <a name="install-azure-cli-20"></a>Установка Azure CLI 2.0

Следуйте указаниям в [официальном руководстве](https://docs.microsoft.com//cli/azure/install-azure-cli), чтобы установить Azure CLI 2.0.

Azure CLI 2.0 можно установить с помощью одной команды `curl`.

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

Чтобы изменения вступили в силу, перезапустите командную оболочку.

```bash
exec -l $SHELL
```

#### <a name="install-arduino-ide"></a>Установка интегрированной среды разработки Arduino

Расширение Visual Studio Code для Arduino использует интегрированную среду разработки Arduino. Скачайте и установите [интегрированную среду разработки Arduino для macOS](https://www.arduino.cc/en/Main/Software).

#### <a name="install-visual-studio-code"></a>Установка Visual Studio Code

Скачайте и установите [Visual Studio Code для macOS](https://code.visualstudio.com/). Это будет основным инструментом разработки для создания приложений для DevKit IoT.

####  <a name="download-latest-package"></a>Скачивание последней версии пакета

1. Установите Node.js. Для установки можно использовать популярный диспетчер пакетов macOS [Homebrew](https://brew.sh/) или [встроенный установщик](https://nodejs.org/en/download/).

2. Скачайте файл с расширением `.zip`, содержащий сценарии задач, необходимые для разработки для DevKit в VS Code.

   > [!div class="button"]
   [Загрузить](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

   Найдите скачанный файл с расширением `.zip` и извлеките его содержимое. Затем запустите приложение **Терминал** и выполните приведенные ниже команды для настройки.

   Переместите извлеченную папку в свою папку пользователя macOS.
   ```bash
   mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
   ```
  
   Установите пакеты `npm`.
   ```
   npm install
   ```

#### <a name="install-vs-code-extension-for-arduino"></a>Расширение VS Code для Arduino

Visual Studio Code позволяет непосредственно устанавливать расширения из Marketplace. Просто щелкните значок расширения в меню в левой области и выполните поиск `Arduino` для установки.

![Установка: расширения](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-devkit-board-package"></a>Установка пакета ПО для платы DevKit

Необходимо будет добавить плату DevKit с помощью Board Manager в Visual Studio Code.

1. Используйте `Cmd+Shift+P` для вызова палитры команд. Введите **Arduino**, затем найдите и выберите **Arduino: Board Manager** (Диспетчер плат Arduino).

2. Щелкните **Additional URLs** (Дополнительные URL-адреса) внизу справа.
   ![Установка: дополнительные URL-адреса](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. Добавьте в файл `settings.json` строку в нижней части области `USER SETTINGS`, затем сохраните его.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![Установка: JSON-файл параметров](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. Теперь в Board Manager выполните поиска "az3166" и установите последнюю версию.
   ![Установка: az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

Теперь вы установили все необходимые инструменты и пакеты для macOS.


## <a name="open-project-folder"></a>Открытие папки проекта

### <a name="launch-vs-code"></a>Запустите VSCode.

Убедитесь, что плата DevKit не подключена. Сначала запустите VS Code и подключите плату DevKit к компьютеру. VS Code автоматически найдет ее, и отобразится вводная страница.

![Минирешение: VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
В некоторых случаях при запуске VS Code появляется сообщение об ошибке. В нем сообщается, что не удается найти интегрированную среду разработки Arduino или связанный пакет ПО для платы. Чтобы устранить эту проблему, закройте VS Code, еще раз запустите интегрированную среду разработки Arduino, и VS Code правильно определит путь к ней.

### <a name="open-arduino-examples-folder"></a>Открытие папки с примерами Arduino

Перейдите на вкладку **Arduino Examples** (Примеры Arduino), перейдите в папку `Examples for MXCHIP AZ3166 > AzureIoT` и щелкните `GetStarted`.

![Минирешение: примеры](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Если вы закрыли эту область, то, чтобы перезагрузить ее, нажмите клавиши `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) для вызова палитры команд и введите **Arduino**, чтобы найти и выбрать **Arduino: Examples** (Arduino: примеры).

## <a name="provision-azure-services"></a>Подготовка служб Azure

В окне решения выполните свою задачу с помощью клавиш `Ctrl+P` (macOS: `Cmd+P`), введя команду task cloud-provision.

В терминале VS Code с помощью указаний в интерактивной командной строке подготовьте необходимые службы Azure.

![Минирешение: подготовка облачных служб](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-arduino-sketch"></a>Создание и передача эскиза Arduino

### <a name="install-required-library"></a>Установка необходимой библиотеки

1. Нажмите клавишу `F1` или `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) для вызова палитры команд и введите **Arduino**. Затем найдите и выберите **Arduino: Library Manager** (Arduino: диспетчер библиотек).

2. Найдите библиотеку `ArduinoJson` и щелкните **Install** (Установить).

### <a name="build-and-upload-the-device-code"></a>Создание и передача кода устройства

Нажмите клавиши `Ctrl+P` (macOS: `Cmd+P`), чтобы выполнить команду task device-upload. Терминал предложит перейти в режим настройки. Чтобы сделать это, удерживая нажатой кнопку "A", нажмите и отпустите кнопку "Reset" (Сброс). На экране отобразится надпись "Configuration" (Настройка). Этот режим необходим, чтобы задать строку подключения, полученную при выполнении команды task cloud-provision.

Затем начнется проверка и передача эскиза Arduino.

![Минирешение: передача кода устройства](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

## <a name="test-the-project"></a>Тестирование проекта

В VS Code щелкните значок вилки в строке состояния, чтобы открыть Serial Monitor.

Пример приложения успешно выполняется, если отображаются следующие результаты.

* Serial Monitor отображает те же сведения, что показаны на снимке экрана ниже.
* Светодиодный индикатор на плате MXChip IoT DevKit мигает.

![Окончательный результат в VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если вы столкнулись с проблемами, то можете ознакомиться с [часто задаваемыми вопросами](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) или обратиться к нам по одному из каналов, указанных ниже.

## <a name="next-steps"></a>Дальнейшие действия

Вы успешно подключили плату MXChip IoT DevKit к Центру Интернета вещей и отправили в него собранные данные датчика.

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

- [Управление обменом сообщениями между устройством и облаком с помощью обозревателя Центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Сохранение сообщений Центра Интернета вещей в хранилище данных Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Визуализация данных, поступающих от датчиков в реальном времени, из Центра Интернета вещей с помощью Power BI](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Визуализация данных, поступающих от датчиков в реальном времени, из Центра Интернета вещей с помощью веб-приложений службы приложений Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Weather forecast using the sensor data from your IoT hub in Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning) (Прогнозирование погоды с помощью датчиков Центра Интернета вещей в машинном обучении Azure)
- [Управление устройствами с помощью средства iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Remote monitoring and notifications with Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) (Удаленный мониторинг и уведомления Logic Apps)
