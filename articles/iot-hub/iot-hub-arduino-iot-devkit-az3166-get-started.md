---
title: "IoT DevKit в облаке: подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure | Документация Майкрософт"
description: "В этом руководстве содержатся сведения о том, как настроить и подключить плату IoT DevKit AZ3166 к Центру Интернета вещей Azure и передавать с нее данные в облачную платформу Azure."
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
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 253a2d34ea5b3f4f8253900be27c54fe5ca1dacf
ms.contentlocale: ru-ru
ms.lasthandoff: 09/07/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Подключение платы IoT DevKit AZ3166 к Центру Интернета вещей Azure в облаке

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Плату [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) можно использовать для разработки и прототипирования решений Интернета вещей, использующих службы Microsoft Azure. Он содержит совместимую с Arduino плату со множеством периферийных устройств и датчиков, пакет ПО для платы с открытым кодом и расширяющийся [каталог проектов](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>В рамках этого руководства мы:
Подключим [плату DevKit](https://microsoft.github.io/azure-iot-developer-kit/) к созданному Центру Интернета вещей Azure, чтобы собирать данные о температуре и влажности с датчиков и отправлять эти данные в Центр Интернета вещей.

У вас еще нет платы DevKit? Вы можете приобрести ее [здесь](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Что вы узнаете

* Как подключить плату IoT DevKit к точке беспроводного доступа и подготовить среду разработки.
* Как создать Центр Интернета вещей и зарегистрировать устройство для MXChip IoT DevKit.
* Как собирать данные датчиков, запустив пример приложения на MXChip IoT DevKit.
* Как отправить данные датчиков в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

* Плата MXChip IoT DevKit с кабелем micro USB. Вы можете [получить ее прямо сейчас](https://aka.ms/iot-devkit-purchase).
* Компьютер под управлением Windows 10 или macOS 10.10 (или более поздней версии).
* Активная подписка Azure. Вы можете активировать [бесплатную 30-дневную пробную учетную запись Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Подготовка оборудования

Подключите оборудование к компьютеру.

Вам потребуется следующее оборудование:

* Плата DevKit.
* Кабель micro USB.

![Необходимое оборудование](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Чтобы подключить плату DevKit к компьютеру, сделайте следующее:

1. Подключите разъем USB кабеля к компьютеру.
2. Подключите разъем micro USB кабеля к плате DevKit.
3. Зеленый светодиодный индикатор питания будет означать, что подключение установлено.

![Подключение оборудования](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Настройка Wi-Fi

Для работы проектов Центра Интернета вещей требуется подключение к Интернету. Следуйте приведенным ниже инструкциям, чтобы настроить плату DevKit для подключения к сети Wi-Fi.

### <a name="enter-ap-mode"></a>Переключение в режим точки беспроводного доступа

Удерживая нажатой кнопку B, нажмите и отпустите кнопку Reset (Сброс), а затем отпустите кнопку B. Плата DevKit переключится в режим точки беспроводного доступа для настройки Wi-Fi. На снимке экрана ниже отображается идентификатор SSID платы DevKit и IP-адрес портала настройки.

![Кнопка Reset (Сброс), кнопка B и идентификатор SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Подключение к точке беспроводного доступа DevKit

Теперь используйте другое устройство с модулем Wi-Fi (компьютер или мобильный телефон), чтобы подключиться к плате DevKit по идентификатору SSID (выделен на снимке экрана выше). Поле пароля оставьте пустым.

![Сведения о сети и кнопка "Подключить"](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Настройка Wi-Fi для платы DevKit

Откройте IP-адрес, отображенный на экране DevKit в браузере на компьютере или в мобильном телефоне, выберите сеть Wi-Fi для подключения платы DevKit, а затем введите пароль. Нажмите кнопку **Подключиться**.

![Поле с паролем и кнопка подключения](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Через несколько секунд после успешного подключения плата DevKit перезагрузится. На экране появится имя Wi-Fi и IP-адрес.

![Имя Wi-Fi и IP-адрес](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> IP-адрес на фотографии может не совпадать с фактическим IP-адресом, который будет назначен и отображен на экране DevKit. Это нормально, так как сеть Wi-Fi использует протокол DHCP для динамического назначения IP-адресов.

После настройки Wi-Fi учетные данные для этого подключения сохранятся на устройстве, даже если оно будет отсоединено. Например, если вы настроили плату DevKit для домашней сети Wi-Fi и затем принесли ее на работу, потребуется перенастроить режим точки беспроводного доступа (начиная с шага в разделе "Переключение в режим точки беспроводного доступ"), чтобы подключить плату DevKit к сети Wi-Fi в офисе. 

## <a name="start-using-the-devkit"></a>Начало использования платы DevKit

Приложение по умолчанию, запущенное на плате DevKit, проверяет наличие последней версии встроенного ПО и отображает некоторые диагностические данные датчиков.

### <a name="upgrade-to-the-latest-firmware"></a>Обновление до последней версии встроенного ПО

При необходимости обновить встроенное ПО на экране будут показаны текущая и последняя версии. Следуйте указаниям по [обновлению встроенного ПО](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/).

![Отображение текущей и последней версии встроенного ПО](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Это действие выполняется один раз. Когда вы начнете разработку с помощью платы DevKit и передадите свое приложение, вместе с ним будет передана последняя версия встроенного ПО.

### <a name="test-various-sensors"></a>Тестирование различных датчиков

Нажмите кнопку B, чтобы проверить датчики. Нажимайте и отпускайте кнопку B, чтобы обойти по очереди все датчики.

![Отображение кнопки B и датчиков](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Подготовка среды разработки

Пришло время настроить среду разработки: инструменты и пакеты для создания отличных приложений Интернета вещей. Версию для Windows или macOS можно выбрать в соответствии с используемой операционной системой.


### <a name="windows"></a>Windows

Мы рекомендуем использовать пакет установки для подготовки среды разработки. В случае каких-либо проблем можно выполнить подготовку [вручную](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/).

#### <a name="download-the-latest-package"></a>Скачивание последней версии пакета

Скачиваемый ZIP-файл содержит все необходимые средства и пакеты для разработки платы DevKit.

> [!div class="button"]
[Загрузить](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


ZIP-файл содержит приведенные ниже средства и пакеты. Если вы уже установили некоторые из этих компонентов, сценарий обнаружит их и пропустит.

* Node.js и Yarn: среда выполнения для скрипта установки и автоматизированных задач.
* [Azure CLI 2.0 (MSI-файл).](https://docs.microsoft.com//cli/azure/install-azure-cli#windows) Кроссплатформенный интерфейс командной строки для управления ресурсами Azure. MSI-файл содержит зависимые компоненты Python и PIP.
* [Visual Studio Code (VS Code).](https://code.visualstudio.com/) Упрощенный редактор кода для разработки для платы DevKit.
* [Расширение Visual Studio Code для Arduino.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino) Расширение, обеспечивающее разработку для Arduino в Visual Studio Code.
* [Интегрированная среда разработки Arduino.](https://www.arduino.cc/en/Main/Software) Средство, которое используется расширением Arduino.
* Пакет ПО для платы DevKit. Цепочки средств, библиотеки и проекты для DevKit.
* Служебная программа ST-Link. Необходимые средства и драйверы.

#### <a name="run-the-installation-script"></a>Запуск скрипта установки

В проводнике Windows найдите ZIP-файл и извлеките его содержимое. Найдите файл install.cmd, щелкните его правой кнопкой мыши и выберите **Запустить от имени администратора**.

![Проводник](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Во время установки отображается ход ее выполнения для каждого средства или пакета.

![Ход установки](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="install-drivers"></a>Установка драйверов

Расширение VS Code для Arduino использует интегрированную среду разработки Arduino. Если вы впервые устанавливаете интегрированную среду разработки Arduino, то вам будет предложено установить соответствующие драйверы.

![Приступая к работе: драйвер](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Для завершения установки в зависимости от скорости подключения к Интернету требуется около 10 минут. После завершения установки на рабочем столе должны появиться ярлыки Visual Studio Code и интегрированной среды разработки Arduino.

> [!NOTE] 
> В некоторых случаях при запуске VS Code появляется сообщение об ошибке. В нем сообщается, что не удается найти интегрированную среду разработки Arduino или связанный пакет ПО для платы. Закройте VS Code и перезапустите интегрированную среду разработки Arduino. VS Code должен правильно определить путь к ней.


### <a name="macos-preview"></a>macOS (предварительная версия)

Выполните инструкции ниже, чтобы подготовить среду разработки в macOS.

#### <a name="install-azure-cli-20"></a>Установка Azure CLI 2.0

1. Azure CLI 2.0 можно установить с помощью одной команды `curl`.

   ```bash
   curl -L https://aka.ms/InstallAzureCli | bash
   ```

2. Чтобы изменения вступили в силу, перезапустите командную оболочку.

   ```bash
   exec -l $SHELL
   ```

Дополнительные сведения об установке Azure CLI 2.0 см. в [официальном руководстве](https://docs.microsoft.com//cli/azure/install-azure-cli).

#### <a name="install-the-arduino-ide"></a>Установка интегрированной среды разработки Arduino

Расширение Visual Studio Code для Arduino использует интегрированную среду разработки Arduino. Скачайте и установите [интегрированную среду разработки Arduino для macOS](https://www.arduino.cc/en/Main/Software).

#### <a name="install-visual-studio-code"></a>Установка Visual Studio Code

Скачайте и установите [Visual Studio Code для macOS](https://code.visualstudio.com/). Это основное средство разработки для создания приложений DevKit IoT.

####  <a name="download-the-latest-package"></a>Скачивание последней версии пакета

1. Установите Node.js. Для установки можно использовать популярный диспетчер пакетов macOS [Homebrew](https://brew.sh/) или [встроенный установщик](https://nodejs.org/en/download/).

2. Скачайте ZIP-файл, содержащий скрипты задач, необходимые для разработки DevKit в VS Code.

   > [!div class="button"]
   [Загрузить](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

3. Найдите скачанный ZIP-файл и извлеките его содержимое. Затем запустите приложение **Терминал** и выполните приведенные ниже команды.

   а. Переместите извлеченную папку в свою папку пользователя macOS.
      ```bash
      mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
      ```
  
   b. Установите пакеты NPM.
      ```
      npm install
      ```

#### <a name="install-the-vs-code-extension-for-arduino"></a>Установка расширения VS Code для Arduino

Установить расширения Microsoft Azure Marketplace можно непосредственно в Visual Studio Code. Щелкните значок расширения в левой области, выполните поиск **Arduino** и нажмите кнопку **Install** (Установить).

![Поиск расширения Arduino](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-the-devkit-board-package"></a>Установка пакета ПО для платы DevKit

Добавьте плату DevKit с помощью Board Manager в Visual Studio Code.

1. Используйте CMD+SHIFT+P для вызова палитры команд. Введите **Arduino**, затем найдите и выберите **Arduino: Board Manager** (Диспетчер плат Arduino).

2. Выберите **Additional URLs** (Дополнительные URL-адреса) в правом нижнем углу.
   ![Ссылка на дополнительные URL-адреса](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. Добавьте в файл settings.json строку в нижней части области **USER SETTINGS** (Параметры пользователя), а затем сохраните его.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![Код, добавленный в область параметров пользователя](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. В Board Manager выполните поиск **AZ3166** и установите последнюю версию.
   ![Установка AZ3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

Теперь вы установили все необходимые инструменты и пакеты для macOS.


## <a name="open-the-project-folder"></a>Открытие папки проекта

### <a name="start-vs-code"></a>Запуск VS Code

Убедитесь, что плата DevKit не подключена. Сначала запустите VS Code и подключите плату DevKit к компьютеру. VS Code автоматически найдет ее и отобразится вводная страница.

![Страница "Введение"](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
> В некоторых случаях при запуске VS Code появляется сообщение об ошибке. В нем сообщается, что не удается найти интегрированную среду разработки Arduino или связанный пакет ПО для платы. Закройте VS Code и перезапустите интегрированную среду разработки Arduino. VS Code должен правильно определить путь к ней.


### <a name="open-the-arduino-examples-folder"></a>Открытие папки с примерами Arduino

На вкладке **Arduino Examples** (Примеры Arduino) перейдите в папку **Examples for MXCHIP AZ3166** (Примеры для MXCHIP AZ3166) > **AzureIoT** и выберите **GetStarted**.

![Вкладка с примерами Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Если вы закрыли эту область, вы можете открыть ее снова. Нажмите клавиши CTRL+SHIFT+P для вызова палитры команд, введите **Arduino**, затем найдите и выберите **Arduino: Examples** (Arduino: примеры).

## <a name="provision-azure-services"></a>Подготовка служб Azure

В окне решения выполните свою задачу с помощью клавиш CTRL+P (в macOS: CMD+P), введя `task cloud-provision`.

В терминале VS Code с помощью указаний в интерактивной командной строке подготовьте необходимые службы Azure.

![Интерактивная командная строка](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Создание и передача эскиза Arduino

### <a name="install-the-required-library"></a>Установка необходимой библиотеки

1. Нажмите клавишу F1 или клавиши CTRL+SHIFT+P (в macOS: CMD+SHIFT+P) для вызова палитры команд, введите **Arduino**, затем найдите и выберите **Arduino: Library Manager** (Arduino: диспетчер библиотек).

2. Найдите библиотеку **ArduinoJson** и выберите **Install** (Установить).

### <a name="build-and-upload-the-device-code"></a>Создание и передача кода устройства

Нажмите клавиши CTRL+P (в macOS: CMD+P), чтобы выполнить команду `task device-upload`. Терминал предложит перейти в режим настройки. Чтобы сделать это, удерживая нажатой кнопку A, нажмите и отпустите кнопку Reset (Сброс). На экране отобразится надпись **Configuration** (Настройка). Этот режим необходим, чтобы задать строку подключения, полученную при выполнении команды `task cloud-provision`.

Затем в терминале начнется проверка и передача эскиза Arduino.

![Проверка и передача эскиза Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Плата DevKit перезагрузится и начнет выполнение кода.

## <a name="test-the-project"></a>Тестирование проекта

В VS Code щелкните значок вилки в строке состояния, чтобы открыть Serial Monitor.

Пример приложения успешно выполняется, если отображаются следующие результаты.

* Serial Monitor отображает те же сведения, что показаны на снимке экрана ниже.
* Светодиодный индикатор на плате MXChip IoT DevKit мигает.

![Окончательный результат в VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Проблемы и обратная связь

Если возникли проблемы, то можете ознакомиться с [часто задаваемыми вопросами](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Вы также можете оставить нам свой отзыв на этой странице.

## <a name="next-steps"></a>Дальнейшие действия

Вы успешно подключили плату MXChip IoT DevKit к Центру Интернета вещей и отправили в него собранные данные датчика.

Чтобы продолжить знакомство с Центром Интернета вещей Azure и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

- [Управление обменом сообщениями между устройством и облаком с помощью обозревателя Центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Сохранение сообщений Центра Интернета вещей в хранилище данных Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Визуализация данных, поступающих от датчиков в реальном времени, из Центра Интернета вещей с помощью Power BI](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Визуализация данных, поступающих от датчиков в реальном времени, из Центра Интернета вещей с помощью веб-приложений службы приложений Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Weather forecast using the sensor data from your IoT hub in Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning) (Прогнозирование погоды с помощью датчиков Центра Интернета вещей в машинном обучении Azure)
- [Управление устройствами с помощью средства iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Remote monitoring and notifications with Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) (Удаленный мониторинг и уведомления Logic Apps)

