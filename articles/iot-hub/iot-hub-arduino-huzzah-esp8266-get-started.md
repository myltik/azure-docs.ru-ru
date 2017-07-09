---
title: "Подключение Feather HUZZAH ESP8266 к Центру Интернета вещей Azure в облаке | Документация Майкрософт"
description: "В этой статье объясняется, как подключить устройство Arduino под названием Adafruit Feather HUZZAH ESP8266 к Центру Интернета вещей Azure — облачной службе Майкрософт, которая помогает управлять ресурсами Интернета вещей."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 0969e78a69c73c29ecfddcf0de0ebeeeed8acd60
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017


---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Подключение Adafruit Feather HUZZAH ESP8266 к Центру Интернета вещей Azure в облаке

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Подключение между DHT22, Feather HUZZAH ESP8266 и Центром Интернета вещей](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>В рамках этого руководства мы:


В рамках этого руководства мы подключим плату Adafruit Feather HUZZAH ESP8266 к созданному Центру Интернета вещей Azure. Затем мы запустим пример приложения на ESP8266, чтобы собрать данные о температуре и влажности, зафиксированные датчиком DHT22. После этого отправим данные с датчика в Центр Интернета вещей.

> [!NOTE]
> При использовании других плат ESP8266 вы по-прежнему можете выполнить эти действия, чтобы подключиться к Центру Интернета вещей. В зависимости от используемой платы ESP8266 вам может потребоваться изменить параметр `LED_PIN`. Например, если вы используете ESP8266 от AI-Thinker, вы можете изменить его значение с `0` на `2`. Нет начального набора? Получите его на [веб-сайте Azure](http://azure.com/iotstarterkits).




## <a name="what-you-learn"></a>Что вы узнаете

* Как создать Центр Интернета вещей и зарегистрировать устройство для Feather HUZZAH ESP8266.
* Как подключить Feather HUZZAH ESP8266 к датчику и компьютеру.
* Как собирать данные датчиков, запустив пример приложения на Feather HUZZAH ESP8266.
* Как отправить данные датчиков в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

![Элементы, необходимые в рамках руководства:](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Чтобы выполнить эту операцию, вам понадобятся следующие элементы из начального набора Feather HUZZAH ESP8266:

* плата Feather HUZZAH ESP8266;
* кабель micro-USB (тип A).

Кроме того, вам понадобятся следующие элементы среды разработки:

* Активная подписка Azure. Если ее нет, можно создать [бесплатную пробную учетную запись Azure](https://azure.microsoft.com/free/) всего за несколько минут.
* ПК или компьютер Mac под управлением Windows или Ubuntu;
* беспроводная сеть, к которой подключается Feather HUZZAH ESP8266;
* подключение к Интернету для скачивания средства настройки;
* [Arduino IDE](https://www.arduino.cc/en/main/software) версии 1.6.8 или более новой. Более ранние версии несовместимы с библиотекой Центра Интернета вещей Azure.

Ниже приведены необязательные элементы, используемые в случае отсутствия датчика. Вы также можете использовать симулированные данные датчика.

* Датчик температуры и влажности Adafruit DHT22.
* Монтажная плата.
* Многомодовый оптоволоконный кабель с разъемами на обоих концах.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Подключение Feather HUZZAH ESP8266 к датчику и компьютеру
В этом разделе рассказывается о том, как подключить датчики к плате. Затем устройство нужно подключить к компьютеру для дальнейшего использования.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Подключение датчика температуры и влажности DHT22 к Feather HUZZAH ESP8266

Чтобы установить подключение, используйте монтажную плату и оптоволоконный кабель с разъемами на обоих концах, как показано ниже. Если у вас нет датчика, пропустите этот раздел, так как вместо него вы можете использовать симулированные данные.

![Справочные материалы по подключению](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)


Чтобы подключить выводы датчика, используйте следующие кабели:


| Начало (датчик)           | Конец (плата)           | Цвет кабеля   |
| -----------------------  | ---------------------- | ------------: |
| VDD (вывод 31F)            | 3V (вывод 58H)           | Красный кабель     |
| DATA (вывод 32F)           | GPIO 2 (вывод 46A)       | Синий кабель    |
| GND (вывод 34F)            | GND (вывод 56I)          | Черный кабель   |

Дополнительные сведения см. в статьях о [подключении к датчику Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) и [выводах Adafruit Feather HUZZAH Esp8266](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).



Теперь работающий датчик должен быть подключен к Feather HUZZAH ESP8266.

![Подключение DHT22 к Feather Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Подключение Feather HUZZAH ESP8266 к компьютеру

Чтобы подключить Feather HUZZAH ESP8266 к своему компьютеру, используйте кабель micro-USB типа A, как показано ниже.

![Подключение Feather Huzzah к компьютеру](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Добавление разрешений для последовательного порта (только в Ubuntu)


При использовании Ubuntu получите разрешения для работы с USB-портом платы Feather HUZZAH ESP8266. Чтобы добавить разрешения для работы с последовательным портом, сделайте следующее:


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

1. Чтобы изменения стали видны, выйдите из Ubuntu и войдите снова.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Сбор данных датчиков и их отправка в Центр Интернета вещей

В этом разделе вы развернете и запустите пример приложения на плате Feather HUZZAH ESP8266. Пример приложения включает и отключает светодиодный индикатор на плате Feather HUZZAH ESP8266 и отправляет данные о температуре и влажности, собранные с датчика DHT22, в Центр Интернета вещей.

### <a name="get-the-sample-application-from-github"></a>Получение примера приложения из GitHub

Пример приложения находится на сайте GitHub. Клонируйте репозиторий, содержащий пример приложения с GitHub. Чтобы клонировать пример репозитория, сделайте следующее:

1. Откройте окно командной строки или терминала.
1. Перейдите к папке, в которую необходимо сохранить пример приложения.
1. Выполните следующую команду:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Установите пакет для Feather HUZZAH ESP8266 в Arduino IDE:

1. Откройте папку, где хранится пример приложения.
1. Откройте файл app.ino в папке приложения в Arduino IDE.

   ![Открытие примера приложения в Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. В Arduino IDE щелкните **Файл** > **Настройки**.
1. В диалоговом окне **Настройки** щелкните значок рядом с текстовым полем **Additional Boards Manager URLs** (Дополнительные URL-адреса для менеджера плат).
1. Во всплывающем окне введите приведенный ниже URL-адрес и нажмите кнопку **ОК**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Указание URL-адреса пакета в Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. В диалоговом окне **Настройки** нажмите кнопку **ОК**.
1. Щелкните **Инструменты** > **Платы** > **Менеджер плат**, а затем выполните поиск по esp8266.

   Диспетчер плат показывает, что установлена плата ESP8266 2.2.0 или более поздней версии.

   ![Завершение установки пакета ESP8266](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. Щелкните **Инструменты** > **Платы** > **Adafruit HUZZAH ESP8266**.

### <a name="install-necessary-libraries"></a>Установка необходимых библиотек

1. В Arduino IDE щелкните **Скетч** > **Подключить библиотеку** > **Управлять библиотеками**.
1. Выполните поиск приведенных ниже имен библиотек по очереди. Для каждой найденной библиотеки щелкните **Установить**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>У вас нет датчика DHT22?

Если у вас нет датчика DHT22, пример приложения может смоделировать данные о температуре и влажности. Чтобы настроить пример приложения для использования имитации данных, сделайте следующее:

1. Откройте файл `config.h` в папке `app`.
1. Найдите приведенную ниже строку кода и измените значение `false` на `true`.
   ```c
   define SIMULATED_DATA true
   ```
   ![Настройка примера приложения для использования имитации данных](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. Сохраните файл с помощью клавиш `Control-s`.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Развертывание примера приложения для Feather HUZZAH ESP8266

1. В Arduino IDE щелкните **Инструменты** > **Порт**, а затем выберите последовательный порт для Feather HUZZAH ESP8266.
1. Чтобы создать и развернуть пример приложения для Feather HUZZAH ESP8266, щелкните **Скетч** > **Загрузка**.

### <a name="enter-your-credentials"></a>Ввод учетных данных

После успешного завершения загрузки введите учетные данные следующим образом:

1. В Arduino IDE щелкните **Инструменты** > **Монитор последовательного порта**.
1. В окне монитора последовательного порта обратите внимание на два раскрывающихся списка в нижнем правом углу.
1. В раскрывающемся списке слева выберите **No line ending** (Ничего не добавлять к отправляемой строке).
1. В раскрывающемся списке справа выберите **115200 baud** (115200 бод).
1. При появлении запроса в поле ввода, расположенном в верхней части окна монитора последовательного порта, введете приведенные ниже сведения, а затем нажмите кнопку **Отправить**.
   * Идентификатор SSID для подключения Wi-Fi.
   * Пароль Wi-Fi.
   * Строка подключения к устройству.

> [!Note]
> Учетные данные хранятся в EEPROM платы Feather HUZZAH ESP8266. При нажатии кнопки сброса на плате Feather HUZZAH ESP8266 в примере приложения отобразится сообщение с запросом на удаление информации. Введите `Y` для удаления данных. Вам будет предложено предоставить их еще раз.

### <a name="verify-the-sample-application-is-running-successfully"></a>Проверка работоспособности примера приложения

Если в окне монитора последовательного порта отобразились приведенные ниже выходные данные и на плате Feather HUZZAH ESP8266 мигает светодиодный индикатор, значит пример приложения запущен успешно.

![Конечные выходные данные в Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Дальнейшие действия

Вы успешно подключили плату Feather HUZZAH ESP8266 к Центру Интернета вещей и отправили в него собранные данные датчика. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


