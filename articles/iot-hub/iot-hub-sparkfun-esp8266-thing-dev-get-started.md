---
title: Подключение Sparkfun ESP8266 Thing Dev к Центру Интернета вещей Azure | Документация Майкрософт
description: Узнайте, как настроить и подключить плату Sparkfun ESP8266 Thing Dev к Центру Интернета вещей Azure и передавать с нее данные в облако Azure.
author: rangv
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 75ff53d5be29af08bb8e9c1b41f61040e5710cf7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635327"
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Подключение Sparkfun ESP8266 Thing Dev к Центру Интернета вещей в облаке Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Подключение DHT22 и Thing Dev к Центру Интернета вещей](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Выполняемая задача

Подключение Sparkfun ESP8266 Thing Dev к создаваемому Центру Интернета вещей. Затем запустим пример приложения на ESP8266, чтобы собрать данные о температуре и влажности, зафиксированные датчиком DHT22. И наконец, отправим данные датчика в Центр Интернета вещей.

> [!NOTE]
> При использовании других плат ESP8266 вы по-прежнему можете выполнить эти действия, чтобы подключиться к Центру Интернета вещей. В зависимости от используемой платы ESP8266 вам может потребоваться изменить параметр `LED_PIN`. Например, при использовании ESP8266 от AI-Thinker вы можете изменить его значение с `0` на `2`. Нет начального набора? Щелкните [здесь](http://azure.com/iotstarterkits).

## <a name="what-you-will-learn"></a>Новые знания

* Как создать Центр Интернета вещей и зарегистрировать устройство Thing Dev.
* Как подключить Thing Dev к датчику и компьютеру.
* Как собирать данные датчиков, запустив пример приложения на Thing Dev.
* Как отправить данные датчиков в Центр Интернета вещей.

## <a name="what-you-will-need"></a>Необходимые условия

![Элементы, необходимые в рамках руководства](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Чтобы выполнить эту операцию, вам понадобятся следующие элементы из начального набора Thing Dev:

* плата Sparkfun ESP8266 Thing Dev;
* кабель micro-USB (тип A).

Кроме того, вам понадобятся следующие элементы среды разработки:

* Активная подписка Azure. Если ее нет, можно создать [бесплатную пробную учетную запись Azure](https://azure.microsoft.com/free/) всего за несколько минут.
* ПК или компьютер Mac под управлением Windows или Ubuntu;
* Беспроводная сеть, к которой будет подключаться ESP8266 Sparkfun Thing Dev.
* подключение к Интернету для скачивания средства настройки;
* [Arduino IDE](https://www.arduino.cc/en/main/software) версии 1.6.8 или более поздней (более ранние версии несовместимы с библиотекой Центра Интернета вещей Azure).

Ниже приведены необязательные элементы, используемые в случае отсутствия датчика. Вы также можете использовать симулированные данные датчика.

* Датчик температуры и влажности Adafruit DHT22.
* Монтажная плата.
* Многомодовый оптоволоконный кабель с разъемами на обоих концах.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Подключение ESP8266 Thing Dev к датчику и компьютеру

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Подключение датчика температуры и влажности DHT22 к ESP8266 Thing Dev

Чтобы установить подключение, используйте монтажную плату и оптоволоконный кабель с разъемами на обоих концах, как показано ниже. Если у вас нет датчика, пропустите этот раздел, так как вместо него вы можете использовать симулированные данные.

![Справочные материалы по подключению](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Чтобы подключить датчик, используйте следующие кабели:

| Начало (датчик)           | Конец (плата)           | Цвет кабеля   |
| -----------------------  | ---------------------- | ------------: |
| VDD (вывод 27F)            | 3V (вывод 8A)           | Красный кабель     |
| DATA (вывод 28F)           | GPIO 2 (вывод 9A)       | Белый кабель    |
| GND (вывод 30F)            | GND (вывод 7J)          | Черный кабель   |


- Дополнительные сведения см. в [документации по установке датчика DHT22](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) и в [спецификации Sparkfun ESP8266 Thing Dev](https://www.sparkfun.com/products/13711)

Теперь работающий датчик должен быть подключен к Sparkfun ESP8266 Thing Dev.

![Подключение dht22 к ESP8266 Thing Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Подключение Sparkfun ESP8266 Thing Dev к компьютеру

Чтобы подключить Sparkfun ESP8266 Thing Dev к своему компьютеру, используйте кабель Micro-USB — USB типа A, как показано ниже.

![Подключение Feather HUZZAH к компьютеру](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Добавление разрешений для последовательного порта (только в Ubuntu)

Если вы используете Ubuntu, предоставьте непривилегированному пользователю разрешения для работы с USB-портом, к которому подключена Sparkfun ESP8266 Thing Dev. Чтобы добавить разрешения для работы с последовательным портом, сделайте следующее:

1. В окне терминала выполните следующие команды:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Вы получите выходные данные одного из следующих типов:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   В выходных данных обратите внимание на параметр `uucp` или `dialout`. Это имя владельца группы USB-порта.

1. Добавьте пользователя в группу, выполнив следующую команду:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` — это имя владельца группы, полученное на предыдущем этапе. `<username>` — это имя пользователя Ubuntu.

1. Чтобы изменения вступили в силу, выйдите из системы Ubuntu и войдите в нее снова.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Сбор данных датчиков и их отправка в Центр Интернета вещей

В этом разделе вы развернете и запустите пример приложения на плате Sparkfun ESP8266 Thing Dev. Пример приложения включает и отключает светодиодный индикатор на плате Sparkfun ESP8266 Thing Dev, а также отправляет в Центр Интернета вещей данные о температуре и влажности, собранные с датчика DHT22.

### <a name="get-the-sample-application-from-github"></a>Получение примера приложения из GitHub

Пример приложения находится на сайте GitHub. Клонируйте репозиторий, содержащий пример приложения с GitHub. Чтобы клонировать пример репозитория, сделайте следующее:

1. Откройте окно командной строки или терминала.
1. Перейдите к папке, в которую необходимо сохранить пример приложения.
1. Выполните следующую команду:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Установите пакет для Sparkfun ESP8266 Thing Dev с помощью Arduino IDE:

1. Откройте папку, где хранится пример приложения.
1. Откройте файл app.ino в папке приложения в Arduino IDE.

   ![Открытие примера приложения в Arduino IDE](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. В Arduino IDE щелкните **Файл** > **Настройки**.
1. В диалоговом окне **Настройки** щелкните значок рядом с текстовым полем **Дополнительные ссылки для менеджера плат**.
1. Во всплывающем окне введите приведенный ниже URL-адрес и нажмите кнопку **ОК**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Указание URL-адреса пакета в Arduino IDE](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. В диалоговом окне **Настройки** нажмите кнопку **ОК**.
1. Щелкните **Инструменты** > **Платы** > **Менеджер плат**, а затем выполните поиск по esp8266.
   Должен быть установлен пакет ESP8266 версии 2.2.0 или более поздней.

   ![Завершение установки пакета ESP8266](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Щелкните **Инструменты** > **Платы** > **Sparkfun ESP8266 Thing Dev**.

### <a name="install-necessary-libraries"></a>Установка необходимых библиотек

1. В Arduino IDE щелкните **Скетч** > **Подключить библиотеку** > **Управлять библиотеками**.
1. Выполните поиск приведенных ниже имен библиотек по очереди. Для каждой найденной библиотеки нажмите кнопку **Установить**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>У вас нет датчика DHT22?

Если у вас нет датчика DHT22, пример приложения может смоделировать данные о температуре и влажности. Чтобы включить использование смоделированных данных в примере приложения, сделайте следующее:

1. Откройте файл `config.h` в папке `app`.
1. Найдите приведенную ниже строку кода и измените значение `false` на `true`.
   ```c
   define SIMULATED_DATA true
   ```
   ![Настройка использования смоделированных данных в примере приложения](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Сохраните изменения с помощью `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Развертывание примера приложения на Sparkfun ESP8266 Thing Dev

1. В Arduino IDE щелкните **Tool** > **Port** (Средства > Порт), а затем выберите последовательный порт для Sparkfun ESP8266 Thing Dev.
1. Чтобы создать и развернуть пример приложения на Sparkfun ESP8266 Thing Dev, щелкните **Sketch** > **Upload** (Скетч > Загрузка).

> [!Note]
> Если вы используете macOS, то во время передачи данных могут отображаться следующие сообщения. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Откройте окно терминала выполните приведенные ниже действия, чтобы устранить эту проблему.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Ввод учетных данных

После завершения загрузки введите учетные данные следующим образом:

1. В Arduino IDE щелкните **Инструменты** > **Монитор последовательного порта**.
1. В окне монитора последовательного порта обратите внимание на два раскрывающихся списка в нижнем правом углу.
1. В раскрывающемся списке слева выберите **No line ending** (Ничего не добавлять к отправляемой строке).
1. В раскрывающемся списке справа выберите **115200 baud** (115200 бод).
1. При появлении запроса в поле ввода, расположенном в верхней части окна монитора последовательного порта, введете приведенные ниже сведения, а затем нажмите кнопку **Отправить**.
   * Идентификатор SSID для подключения Wi-Fi.
   * Пароль Wi-Fi.
   * Строка подключения к устройству.

> [!Note]
> Учетные данные хранятся в памяти EEPROM платы Sparkfun ESP8266 Thing Dev. Если вы нажмете кнопку сброса на плате Sparkfun ESP8266 Thing Dev, в примере приложения отобразится сообщение с запросом на удаление информации. Чтобы удалить информацию, нажмите клавишу `Y`. После этого отобразится запрос на повторный ввод данных.

### <a name="verify-the-sample-application-is-running-successfully"></a>Проверка работоспособности примера приложения

Если в окне монитора последовательного порта отобразились приведенные ниже выходные данные, а на плате Sparkfun ESP8266 Thing Dev мигает светодиодный индикатор, значит пример приложения запущен успешно.

![Окончательные выходные данные в Arduino IDE](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Дополнительная информация

Вы успешно подключили плату Sparkfun ESP8266 Thing Dev к Центру Интернета вещей и отправили данные, полученные от датчика. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
