---
title: Подключение устройства Raspberry Pi к приложению Azure IoT Central (Python) | Документация Майкрософт
description: Вы узнаете, как разработчик устройства может подключать устройство Raspberry Pi к приложению Azure IoT Central, используя язык Python.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: e9c2d18a518bd5c98fcc35efdb0dff36970a49b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629071"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Подключение устройства Raspberry Pi к приложению Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

В этой статье описано, каким образом разработчик устройства может подключить устройство Raspberry Pi к приложению Microsoft Azure IoT Central, используя язык Python.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Приложение Azure IoT Central, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits). Дополнительные сведения см. в статье [Создание приложения Azure IoT Central](howto-create-application.md).
* Устройство Raspberry Pi с операционной системой Raspbian. Вам понадобится подключить монитор, клавиатуру и мышь к устройству Raspberry Pi, чтобы получить доступ к среде графического пользовательского интерфейса. Устройство Raspberry Pi должно иметь возможность [подключения к Интернету](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Дополнительная плата [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) для Raspberry Pi (необязательно). Эта плата собирает данные телеметрии с различных датчиков, а затем отправляет эти данные в приложение Azure IoT Central. Если у вас нет платы **Sense Hat**, вместо нее можно использовать эмулятор.

Приложение, созданное на основе шаблона приложения **Sample Devkits** (Образец Devkits), включает в себя шаблон приложения **Raspberry Pi** со следующими характеристиками:

### <a name="telemetry-measurements"></a>Измерения телеметрии

| Имя поля     | Units  | Минимальная | Максимальная | Число десятичных знаков |
| -------------- | ------ | ------- | ------- | -------------- |
| Влажность       | %      | 0       | 100     | 0              |
| temp           | °C     | –40     | 120     | 0              |
| pressure       | гПа    | 260     | 1260    | 0              |
| magnetometerX  | мГс | –1000   | 1000    | 0              |
| magnetometerY  | мГс | –1000   | 1000    | 0              |
| magnetometerZ  | мГс | –1000   | 1000    | 0              |
| accelerometerX | mg     | –2000   | 2000    | 0              |
| accelerometerY | mg     | –2000   | 2000    | 0              |
| accelerometerZ | mg     | –2000   | 2000    | 0              |
| gyroscopeX     | милиградусов/с   | –2000   | 2000    | 0              |
| gyroscopeY     | милиградусов/с   | –2000   | 2000    | 0              |
| gyroscopeZ     | милиградусов/с   | –2000   | 2000    | 0              |

### <a name="settings"></a>Параметры

Числовые параметры

| Отображаемое имя | Имя поля | Units | Число десятичных знаков | Минимальная | Максимальная | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Напряжение      | setVoltage | В | 0              | 0       | 240     | 0       |
| Текущее значение      | setCurrent | Амперы  | 0              | 0       | 100     | 0       |
| Скорость вращения вентилятора    | fanSpeed   | Об/мин   | 0              | 0       | 1000    | 0       |

Параметры переключения

| Отображаемое имя | Имя поля | Включение текста | Отключение текста | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ВКЛ      | ВЫКЛ.      | Отключить     |

### <a name="properties"></a>properties

| type            | Отображаемое имя | Имя поля | Тип данных |
| --------------- | ------------ | ---------- | --------- |
| Свойство устройства | Серийный номер   | dieNumber  | number    |
| текст            | Расположение     | location   | Недоступно       |

### <a name="add-a-real-device"></a>Добавление реального устройства

В приложение Azure IoT Central добавьте реальное устройство на основе шаблона приложения **Raspberry Pi** и запишите строку подключения к устройству. Дополнительные сведения см. в статье [Добавление реального устройства в приложение Azure IoT Central](tutorial-add-device.md).

## <a name="configure-the-raspberry-pi"></a>Настройка Raspberry Pi

Далее описывается, как скачать и настроить образец приложения Python из GitHub. Этот пример приложения:

* отправляет данные телеметрии и значения свойств в Azure IoT Central;
* реагирует на изменения параметров, внесенные в Azure IoT Central.

> [!NOTE]
> Дополнительные сведения о примере Python Raspberry Pi см. в файле [Readme](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) на сайте GitHub.

1. В веб-браузере на настольном Raspberry Pi перейдите на страницу [выпусков встроенного ПО Azure IoT Central](https://github.com/Microsoft/microsoft-iot-central-firmware/releases).

1. Скачайте ZIP-файл, содержащий последнюю версию встроенного ПО, в домашнюю папку в Raspberry Pi. Имя файла будет выглядеть примерно так: `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Чтобы распаковать файл встроенного ПО, используйте **диспетчер файлов** в настольном Raspberry Pi. Щелкните ZIP-файл правой кнопкой мыши и выберите **Extract here** (Извлечь сюда). В результате будет создана папка с именем `RaspberryPi-IoTCentral-X.X.X` в домашней папке.

1. Если у вас нет платы **Sense Hat**, подключенной к Raspberry Pi, необходимо включить эмулятор:
    1. В **диспетчере файлов** в папке `RaspberryPi-IoTCentral-X.X.X` щелкните правой кнопкой мыши файл **config.iot** и выберите **текстовый редактор**.
    1. Измените строку `"simulateSenseHat": false,` на `"simulateSenseHat": true,`.
    1. Сохраните изменения и закройте **текстовый редактор**.

1. Запустите сеанс **терминала** и используйте команду `cd`, чтобы перейти в папку, созданную на предыдущем шаге.

1. Чтобы запустить выполнение примера приложения, введите `./start.sh` в окне **терминала**. Если вы используете **эмулятор Sense HAT**, отобразится его графический пользовательский интерфейс. В этом интерфейсе можно изменить значения телеметрии, отправленные в приложение Azure IoT Central.

1. В окне **терминала** отобразится сообщение, которое выглядит примерно так: `Device information being served at http://192.168.0.60:8080`. URL-адрес может отличаться в вашей среде. Скопируйте URL-адрес, откройте веб-браузер и перейдите на страницу настройки.

    ![Настройка устройства](media/howto-connect-raspberry-pi-python/configure.png)

1. Введите строку подключения к устройству, записанную при добавлении реального устройства в приложение Azure IoT Central. Выберите **Настройка устройства**. Появится сообщение **Device configured, your device should start sending data to Azure IoT Central momentarily** (Устройство настроено. Передача данных в Azure IoT Central начнется немедленно).

1. В приложении Azure IoT Central вы увидите, как код, выполняющийся в Raspberry Pi, взаимодействует с приложением:

    * На странице **Measurements** (Измерения) для реального устройства можно просмотреть данные телеметрии, отправленные из Raspberry Pi. Если вы используете **эмулятор Sense HAT**, вы можете изменить значения данных телеметрии в графическом пользовательском интерфейсе Raspberry Pi.
    * На странице **Свойства** можно просмотреть значение передаваемого свойства **Серийный номер**.
    * На странице **Параметры** можно изменить различные параметры Raspberry Pi (например, напряжение и скорость вращения вентилятора). Когда изменения подтверждаются в Raspberry Pi, параметры отображаются как **синхронизированные** в Azure IoT Central.

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как подключить устройство Raspberry Pi к Azure IoT Central, а значит вы готовы к следующему шагу.

* [Подключение универсального клиентского приложения Node.js к Azure IoT Central](howto-connect-nodejs.md)