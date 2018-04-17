---
title: Использование свойств двойников устройств Центра Интернета вещей Azure (Python) | Документация Майкрософт
description: Настройка устройств с помощью двойников устройств Центра Интернета вещей Azure. Используйте пакеты SDK для устройств Azure IoT для Python, чтобы реализовать приложение имитации устройства и приложение службы, которое изменяет конфигурацию устройства с помощью двойника устройства.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: e6f4aa61e37769dc4851f8284d07a7991ee38c91
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>Настройка устройств с помощью требуемых свойств (Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

По завершении работы с этим руководством у вас будет два консольных приложения Python:

* **SimulateDeviceConfiguration.py** — приложение имитации устройства, которое ожидает обновления требуемой конфигурации и сообщает о состоянии обновления имитации конфигурации.
* **SetDesiredConfigurationAndQuery.py** — внутреннее приложение Python, которое задает требуемую конфигурацию на устройстве и выполняет запрос к процессу обновления конфигурации.

> [!NOTE]
> Статья [IoT Hub SDKs][lnk-hub-sdks] (Пакеты SDK для Центра Интернета вещей) содержит сведения о разных пакетах SDK для Azure IoT, с помощью которых можно создать приложения для устройств и внутренние приложения.
> 
> 

Для работы с этим учебником требуется:

* [Python 2.x или 3.x][lnk-python-download]. Обязательно используйте 32-разрядную или 64-разрядную версию установки согласно требованиям программы настройки. При появлении запроса во время установки обязательно добавьте Python в переменную среды соответствующей платформы. Если вы используете Python 2.x, может потребоваться [установка или обновление *pip* — системы управления пакетами Python][lnk-install-pip].
* При использовании ОС Windows потребуется [распространяемый пакет Visual C++][lnk-visual-c-redist], чтобы разрешить использовать встроенные библиотеки DLL из Python.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

Если вы выполнили указания руководства [Приступая к работе с двойниками устройств (предварительная версия)][lnk-twin-tutorial], то у вас уже есть Центр Интернета вещей и удостоверение устройства **myDeviceId**. Вы можете перейти к разделу [Создание приложения имитации устройства][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>Создание приложения имитации устройства
В этом разделе вы создадите консольное приложение Python, которое подключается к центру как **myDeviceId**, ожидает обновления требуемой конфигурации и сообщает об обновлениях в ходе обновления имитации конфигурации.

1. Установите **пакет SDK для устройств Azure IoT для Python**, выполнив следующую команду в командной строке:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. В текстовом редакторе создайте файл **SimulateDeviceConfiguration.py**.

1. Добавьте следующий код в файл **SimulateDeviceConfiguration.py** и замените заполнитель **{device connection string}** строкой подключения устройства, скопированной при создании удостоверения устройства **myDeviceId**.

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    Объект **CLIENT** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. Далее будет присоединен обработчик для обновления требуемых свойств, затем будет добавлен дополнительный обработчик, чтобы проверить, что запрос на изменение конфигурации действительно существует. Для этого он сравнивает идентификаторы конфигураций, а затем вызывает метод, который запускает изменение конфигурации. Для простоты приведенный выше код использует жестко заданное значение по умолчанию для выполнения начальной конфигурации. Настоящее приложение, вероятно, скачало бы эту конфигурацию из локального хранилища.
   
   > [!IMPORTANT]
   > События изменения требуемых свойств всегда создаются единожды при подключении устройства. Прежде чем что-либо делать, обязательно убедитесь, что требуемые свойства действительно изменились.
   > 

1. Добавьте следующий код в конец файла **SimulateDeviceConfiguration.py**:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    Метод **device_twin_callback** обновляет сообщаемые свойства в объекте локального двойника устройства с помощью запроса на обновление конфигурации и устанавливает _идентификатор конфигурации_. После успешного обновления двойника устройства он выдает сообщение об обновлении. Для сокращения нагрузки на полосу пропускания при обновлении сообщаемых свойств указываются только свойства, которые нужно изменить (**TWIN_PAYLOAD** в коде выше), а не заменяется весь документ.
   
   > [!NOTE]
   > В этом руководстве не имитируется поведение при одновременном обновлении конфигураций. В некоторых случаях обновление конфигурации может пройти в соответствии с изменениями целевой конфигурации, в других может потребоваться поместить изменения в очередь, а в остальных они могут быть отклонены с ошибкой. Следует учитывать желаемое поведение для конкретной конфигурации и добавить соответствующую логику перед ее изменением.
   > 

1. Добавьте следующий код в конец файла **SimulateDeviceConfiguration.py**: 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Запустите приложение устройства:
   
    ```cmd/sh
    node SimulateDeviceConfiguration.js
    ```
   
    Отобразится сообщение `Device twins updated.`. Не отключайте приложение.


## <a name="create-the-service-app"></a>Создание приложения службы
В этом разделе вы создадите консольное приложение Python, которое обновляет *требуемые свойства* двойника устройства, связанного с **myDeviceId**, с использованием нового объекта конфигурации телеметрии. Затем оно выполняет запрос к двойникам устройств, которые хранятся в Центре Интернета вещей, и показывает разницу между требуемой и сообщаемой конфигурацией устройства.

1. Установите **пакет SDK для службы Azure IoT для Python**, выполнив следующую команду в командной строке:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. В текстовом редакторе создайте файл **SetDesiredAndQuery.py**.

1. Добавьте следующий код в файл **SetDesiredAndQuery.py** и замените заполнитель **{IoTHubConnectionString}** на строку подключения Центра Интернета вещей, скопированную при создании центра, а заполнитель **{deviceId}** — на имя вашего устройства:

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Добавьте в конец файла **SetDesiredAndQuery.py** приведенный ниже код.

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. Когда файл **SimulateDeviceConfiguration.py** будет выполняться, запустите приложение в новой командной строке, используя следующую команду:

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    Идентификатор передаваемой конфигурации изменится с **1** на **2**, а значение частоты отправки изменится с 24 часов на 5 минут.


## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы установили требуемую конфигурацию в качестве *требуемых свойств* из внутреннего приложения и написали код приложения для имитации устройства, которое обнаруживает изменения и имитирует процесс обновления, о состоянии которого сообщается двойнику устройства в качестве *сообщаемых свойств*.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* отправить данные телеметрии с устройств (руководство по [началу работы с Центром Интернета вещей][lnk-iothub-getstarted]);
* запланировать или выполнить операции на больших наборах устройств (см. руководство [Планирование и трансляция заданий (Node)][lnk-schedule-jobs]).
* управлять устройствами в интерактивном режиме, например включить вентилятор из управляемого пользователем приложения (руководство по [использованию прямых методов][lnk-methods-tutorial]).

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
