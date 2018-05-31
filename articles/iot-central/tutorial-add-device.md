---
title: Добавление реального устройства в приложение Azure IoT Central | Документация Майкрософт
description: Как оператор вы можете добавить реальное устройство в приложение Azure IoT Central.
services: iot-central
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: f95d9ec8cf22c287169a8de077ff9eb5907a8e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201937"
---
# <a name="add-a-real-device-to-your-azure-iot-central-application"></a>Добавление реального устройства в приложение Azure IoT Central

В этом руководстве показано, как добавить реальное устройство в приложение Microsoft Azure IoT Central и настроить его.

Это руководство состоит из двух частей:

1. Во-первых, вы, как оператор, добавите реальное устройство в приложение Azure IoT Central, а затем настроите его. В конце этой части вы извлечете строку подключения для использования во второй части.
1. Затем, как разработчик устройства, вы ознакомитесь со сведениями о коде в вашем реальном устройстве. Вы добавите строку подключения из первой части в пример кода.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Добавление нового реального устройства.
> * Настройка нового устройства.
> * Получение строки подключения для реального устройства из приложения.
> * Сопоставление клиентского кода с приложением.
> * Настройка клиентского кода для реального устройства.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем вы начнете, конструктор должен выполнить инструкции по крайней мере в первом руководстве по созданию приложения Azure IoT Central:

* [Определение типа нового устройства в приложении в Azure IoT Central](tutorial-define-device-type.md) (обязательно).
* [Настройка правил и действий для устройства в Azure IoT Central](tutorial-configure-rules.md).
* [Настройка представлений оператора Azure IoT Central](tutorial-customize-operator.md) (необязательно).

## <a name="add-a-real-device"></a>Добавление реального устройства

Чтобы добавить реальное устройство в приложение, используете шаблон устройства **подключенного кондиционера**, который вы создали в руководстве по [определению типа нового устройства](tutorial-define-device-type.md).

1. Чтобы в качестве оператора добавить новое устройство, выберите **Device Explorer** в меню навигации слева:

    ![Шаблон подключенного кондиционера на странице Device Explorer](media/tutorial-add-device/explorer.png)

    В **Device Explorer** отображается шаблон устройства **подключенного кондиционера** и имитированное устройство, которое было автоматически создано, когда конструктор создал шаблон устройства.

1. Чтобы подключить реальный кондиционер, выберите **Создать**, а затем — **Реальный**:

    ![Первые шаги добавления нового реального подключенного кондиционера](media/tutorial-add-device/newreal.png)

1. При желании вы можете переименовать новое устройство, выбрав имя устройства и отредактировав значение:

    ![Переименование устройства](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Настройка реального устройства

Реальное устройство создается из шаблона устройства **Подключенный кондиционер**. Как конструктор вы можете с помощью **параметров** настроить свое устройство и задать значения свойств для записи информации об устройстве.

1. Обратите внимание, что на странице **Параметры** в качестве состояния параметра **Set Temperature** (Установить температуру) указано **no update** (не обновлен). Он остается в таком состоянии до тех пор, пока реальное устройство не подключится и не подтвердит, что использует этот параметр:

    ![Синхронизация параметров](media/tutorial-add-device/settingssyncing.png)

1. На странице **Свойства** нового реального подключенного кондиционера задайте для параметра **Серийный номер** значение **rcac0010**, а для **версии встроенного ПО** — 9.75. Затем нажмите кнопку **Сохранить**:

    ![Установка свойств для реального устройства](media/tutorial-add-device/setproperties.png)

1. Как конструктор вы можете просматривать следующие страницы своего реального устройства: **Measurements** (Измерения), **Правила** и **Панель мониторинга**.

## <a name="get-connection-string-for-real-device-from-application"></a>Получение строки подключения для реального устройства из приложения

Разработчику устройства необходимо внедрить _строку подключения_ для вашего реального устройства в код, который выполняется на устройстве. Строка подключения позволяет устройству безопасно подключаться к вашему приложению Azure IoT Central. Каждый экземпляр устройства содержит уникальную строку подключения. В следующих шагах показано, как найти строку подключения для экземпляра устройства в приложении:

1. В реальном подключенном кондиционере на экране **Устройство** выберите **Connect this device** (Подключить это устройство):

    ![Страница устройства, отображающая ссылку на сведения о подключении](media/tutorial-add-device/connectionlink.png)

1. На странице **Подключение** скопируйте и сохраните **первичную строку подключения**. Это значение используется во второй части данного руководства. Разработчик устройства использует это значение в клиентском приложении, которое выполняется на устройстве:

    ![Значения строк подключения](media/tutorial-add-device/connectionstring.png)

## <a name="prepare-the-client-code"></a>Подготовка клиентского кода

Пример кода в этой статье написан на [Node.js ](https://nodejs.org/). Этот код используется для выполнения следующих задач:

* подключение устройства к приложению Azure IoT Central;
* отправка данных телеметрии температуры из подключенного кондиционера;
* отправка ответа оператору, который использует параметр **Set Temperature** (Установить температуру).

В статьях, указанных в разделе [Следующие шаги](#next-steps), содержатся более полные примеры и сведения об использовании других языков. Дополнительные сведения см. в статье [Device connectivity in Azure IoT Central](concepts-connectivity.md) (Подключение устройств в Azure IoT Central).

В следующих шагах показано, как подготовить пример [Node.js](https://nodejs.org/):

1. Установите на компьютере [Node.js](https://nodejs.org/) версии 4.0.x или более поздней. Node.js доступен для разных операционных систем.

1. Создайте на компьютере папку с именем `connectedairconditioner`.

1. Перейдите к созданной папке `connectedairconditioner` в среде командной строки.

1. Чтобы инициализировать проект Node.js, выполните следующую команду, приняв все значения по умолчанию:

    ```cmd/sh
    npm init
    ```

1. Чтобы установить требуемые пакеты, выполните следующую команду:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. С помощью текстового редактора создайте файл **ConnectedAirConditioner.js** в папке `connectedairconditioner`.

1. Добавьте следующие инструкции `require` в начало файла **ConnectedAirConditioner.js**:

    ```javascript
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Добавьте следующие объявления переменных в файл:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    > [!NOTE]
    > Вы обновите заполнитель `{your device connection string}` позже.

1. Сохраните внесенные изменения, но не закрывайте файл.

## <a name="understand-how-client-code-maps-to-the-application"></a>Сопоставление клиентского кода с приложением

В предыдущем разделе вы создали основу проекта Node.js для приложения, которое подключается к приложению Azure IoT Central. В этом разделе вы добавляете код для выполнения следующих задач:

* подключение к приложению Azure IoT Central;
* отправка данных телеметрии в приложение Azure IoT Central;
* получение параметров из приложения Azure IoT Central.

1. Чтобы отправить данные телеметрии температуры в приложение Azure IoT Central, добавьте в файл **ConnectedAirConditioner.js** следующий код:

    ```javascript
    // Send device telemetry.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Имя поля в отправляемом файле JSON должно совпадать с именем поля, которое вы указали для данных телеметрии температуры в шаблоне устройства. В этом примере поле называется **temperature**.

1. Чтобы определить параметры, поддерживаемые вашим устройством, например **setTemperature**, добавьте следующее определение:

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Чтобы обрабатывать параметры, отправленные из Azure IoT Central, добавьте следующую функцию, которая определяет и выполняет соответствующий код устройства:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

    Эта функция выполняет следующее:

    * наблюдает за отправкой требуемого свойства из Azure IoT Central;
    * находит соответствующую функцию, которую нужно вызвать для обработки изменения параметра;
    * отправляет подтверждение обратно в приложение Azure IoT Central.

1. Добавьте следующий код, чтобы завершить подключение к Azure IoT Central, и присоедините функции в клиентском коде:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. Сохраните внесенные изменения, но не закрывайте файл.

## <a name="configure-client-code-for-real-device"></a>Настройка клиентского кода для реального устройства

<!-- Add the connection string to the sample code, build, and run -->
Чтобы настроить клиентский код для подключения к приложению Azure IoT Central, необходимо добавить строку подключения для реального устройства, которая была указана ранее в этом руководстве.

1. В файле **ConnectedAirConditioner.js** найдите следующую строку кода:

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. Замените `{your device connection string}` строкой подключения реального устройства. Вы записали строку подключения в конце раздела "Получение строки подключения для реального устройства из приложения".

1. Сохраните изменения в файле **ConnectedAirConditioner.js**.

1. Чтобы запустить пример, в среде командной строки введите следующую команду:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > При запуске этой команды убедитесь, что вы находитесь в папке `connectedairconditioner`.

1. Приложение выводит выходные данные в консоли:

    ![Выходные данные клиентского приложения](media/tutorial-add-device/output.png)

1. Примерно через 30 секунд вы увидите данные телеметрии на странице **Measurements** (Измерения) устройства:

    ![Реальные данные телеметрии](media/tutorial-add-device/realtelemetry.png)

1. Вы можете увидеть на странице **Параметры**, что теперь параметр синхронизирован. При первом подключении устройство получило значение параметра и подтвердило изменение:

    ![Синхронизированный параметр](media/tutorial-add-device/settingsynced.png)

1. На странице **Параметры** для температуры устройства задайте значение **95** и выберите **Update device** (Обновить устройство). Ваш пример приложения получает и обрабатывает это изменение:

    ![Прием и обработка параметра](media/tutorial-add-device/receivesetting.png)

    > [!NOTE]
    > Существует два вида сообщения об обновлении параметра. Одно отображается при отправке состояния `pending`, а другое — при отправке состояния `completed`.

1. На странице **Measurements** (Измерения) можно увидеть, что устройство отправляет более высокие значения температуры:

    ![Значения данных телеметрии температуры теперь выше](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="nextstepaction"]
> * Добавление нового реального устройства.
> * Настройка нового устройства.
> * Получение строки подключения для реального устройства из приложения
> * Сопоставление клиентского кода с приложением.
> * Настройка клиентского кода для реального устройства.

Теперь, когда вы подключили реальное устройство к приложению Azure IoT Central, вы можете выполнить следующие действия.

Будучи оператором, вы можете научиться, как:

* [Управление устройствами](howto-manage-devices.md)
* [использовать наборы устройств](howto-use-device-sets.md);
* [создавать настраиваемую аналитику](howto-create-analytics.md).

Как разработчик устройств вы можете научиться, как:

* [подготовить и подключить DevKit](howto-connect-devkit.md);
* [подготовить и подключить Raspberry Pi](howto-connect-raspberry-pi-python.md);
* [подключить универсальный клиент Node.js к приложению Azure IoT Central](howto-connect-nodejs.md).
