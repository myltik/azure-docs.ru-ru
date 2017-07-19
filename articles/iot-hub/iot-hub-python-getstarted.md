---
title: "Приступая к работе с Центром Интернета вещей Azure (Python) | Документация Майкрософт"
description: "Узнайте, как отправлять сообщения из устройства на облако в Центр Интернета вещей Azure с помощью пакетов SDK Интернета вещей для Python. Создайте виртуальное устройство и приложения службы для регистрации устройства, отправки сообщений и чтения сообщений из Центра Интернета вещей."
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/22/2017
ms.author: dkshir
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 05268924a182575b3df66fb6dad6bcac2700ec0c
ms.contentlocale: ru-ru
ms.lasthandoff: 07/13/2017

---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Подключение виртуального устройства к Центру Интернета вещей с помощью Python
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

По завершении работы с этим руководством у вас будет два приложения Python:

* **CreateDeviceIdentity.py** — создает удостоверение устройства и соответствующий ключ безопасности для подключения к приложению виртуального устройства.
* **SimulatedDevice.py** — подключается к Центру Интернета вещей с созданным ранее удостоверением устройства и периодически отправляет сообщения телеметрии с использованием протокола MQTT.

> [!NOTE]
> Статья о [пакетах SDK для Центра Интернета вещей Azure][lnk-hub-sdks] содержит сведения о различных пакетах SDK, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения.
> 
> 

Для работы с этим учебником требуется:

* [Python 2.x или 3.x][lnk-python-download]. Обязательно используйте 32-разрядную или 64-разрядную версию установки согласно требованиям программы настройки. При появлении запроса во время установки обязательно добавьте Python в переменную среды соответствующей платформы. Если вы используете Python 2.x, может потребоваться [установка или обновление *pip* — системы управления пакетами Python][lnk-install-pip].
* При использовании ОС Windows потребуется [распространяемый пакет Visual C++][lnk-visual-c-redist], чтобы разрешить использовать встроенные библиотеки DLL из Python.
* [Node.js 4.0 или более поздней версии][lnk-node-download]. Обязательно используйте 32-разрядную или 64-разрядную версию установки согласно требованиям программы настройки. Это необходимо для установки [средства обозревателя Центра Интернета вещей][lnk-iot-hub-explorer].
* Активная учетная запись Azure. Если у вас нет учетной записи, можно создать [бесплатную учетную запись][lnk-free-trial] всего за несколько минут.

> [!NOTE]
> Сейчас пакеты *pip* для `azure-iothub-service-client` и `azure-iothub-device-client` доступны только для ОС Windows. Сведения для ОС Linux и Mac см. в соответствующих разделах статьи [Prepare your development environment][lnk-python-devbox] (Подготовка среды разработки).
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Теперь центр IoT создан Используйте имя узла и строку подключения Центра Интернета вещей в оставшейся части этого руководства.

> [!NOTE]
> Центр Интернета вещей также можно легко создать в командной строке с помощью Python или Node.js на основе Azure CLI. Дополнительные сведения о том, как это сделать быстро, см. в статье [Создание Центра Интернета вещей с помощью Azure CLI 2.0][lnk-azure-cli-hub]. 
> 

## <a name="create-a-device-identity"></a>Создание удостоверения устройства
В этом разделе приведены инструкции по написанию консольного приложения Python, создающего удостоверение устройства в реестре удостоверений Центра Интернета вещей. Устройство может подключиться к Центру Интернета вещей, только если в реестре удостоверений есть соответствующая запись. Дополнительные сведения см. в разделе, посвященном **реестру удостоверений**, в [руководстве разработчика по Центру Интернета вещей Azure][lnk-devguide-identity]. При запуске этого консольного приложения создается уникальный идентификатор устройства и ключ, с помощью которых выполняется идентификация во время отправки сообщений из устройства в облако для центра IoT.

1. Откройте окно командной строки и установите **пакет SDK службы Центра Интернета вещей Azure для Python**. После установки пакета SDK закройте окно командной строки.

    ```
    pip install azure-iothub-service-client
    ```

2. Создайте файл Python с именем **CreateDeviceIdentity.py**. Откройте его в [редакторе или интегрированной среде разработки Python][lnk-python-ide-list] (например, [IDLE][lnk-idle] по умолчанию).

3. Добавьте следующий код для импорта необходимых модулей из пакета SDK службы:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. Добавьте следующий код, заменив заполнитель `[IoTHub Connection String]` строкой подключения для Центра Интернета вещей, созданного в предыдущем разделе. Вы можете использовать любое имя, например `DEVICE_ID`.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. Добавьте следующую функцию для вывода некоторых сведений об устройстве.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. Добавьте следующую функцию, чтобы установить идентификацию устройства с помощью диспетчера реестра. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. Теперь добавьте функцию main, как показано ниже, и сохраните файл.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. В командной строке запустите **CreateDeviceIdentity.py** следующим образом:

    ```python
    python CreateDeviceIdentity.py
    ```
6. Виртуальное устройство должно начать создаваться. Запишите значения устройства **deviceId** и **primaryKey**. Эти значения понадобятся вам позже, когда вы будете создавать приложение, которое подключается к центру IoT как устройство.

    ![Успешное создание устройства][1]

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только идентификаторы устройств, необходимые для безопасного доступа к Центру Интернета вещей. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок включения или выключения, который позволяет вам отключить доступ для отдельного устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве для разработчиков Центра Интернета вещей][lnk-devguide-identity].
> 
> 


## <a name="create-a-simulated-device-app"></a>Создание приложения виртуального устройства
В этом разделе приведены инструкции по созданию консольного приложения Python, имитирующего устройство и отправляющего сообщения с устройства в облако в Центре Интернета вещей.

1. Откройте новое окно командной строки и установите пакет SDK устройства Центра Интернета вещей Azure для Python указанным ниже образом. После установки закройте окно командной строки.

    ```
    pip install azure-iothub-device-client
    ```
2. Создайте файл с именем **SimulatedDevice.py**. Откройте его в редакторе или интегрированной среде разработки Python (например, IDLE).

3. Добавьте следующий код для импорта необходимых модулей из пакета SDK устройства.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. Добавьте следующий код и замените заполнитель `[IoTHub Device Connection String]` строкой подключения для устройства. Обычно строка подключения устройства указывается в формате `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`. Используйте значения **deviceId** и **primaryKey** устройства, созданного в предыдущем разделе, чтобы заменить `<deviceId>` и `<primaryKey>` соответственно. Замените `<hostName>` именем узла Центра Интернета вещей (обычно это `<IoT hub name>.azure-devices.net`).

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. Добавьте следующий код, чтобы определить обратный вызов подтверждения отправки. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. Добавьте следующий код для инициализации клиента устройства.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        return client
    ```
7. Добавьте следующую функцию для форматирования и отправки сообщения из виртуального устройства в Центр Интернета вещей.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. Теперь добавьте функцию main. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. Сохраните и закройте файл **SimulatedDevice.py**. Теперь все готово к запуску приложения.

> [!NOTE]
> Для простоты в этом руководстве не реализуются политики повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временного сбоя][lnk-transient-faults] на сайте MSDN.
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>Получение сообщений с виртуального устройства
Для получения сообщений телеметрии с устройства необходимо использовать совместимую с [концентраторами событий][lnk-event-hubs-overview] конечную точку, предоставляемую Центром Интернета вещей, которая считывает сообщения, отправляемые с устройства в облако. Ознакомьтесь с руководством по [началу работы с концентраторами событий][lnk-eventhubs-tutorial], чтобы узнать, как обрабатываются сообщения из концентраторов событий для конечной точки Центра Интернета вещей, совместимой с концентраторами событий. Концентраторы событий пока не поддерживают данные телеметрии на языке Python. Таким образом, вы можете создать консольное приложение [Node.js](iot-hub-node-node-getstarted.md#D2C_node) или [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) на основе концентраторов событий для чтения сообщений, отправляемых с устройства в облако, из Центра Интернета вещей. В этом руководстве описывается, как использовать [средство обозревателя Центра Интернета вещей][lnk-iot-hub-explorer] для чтения этих сообщений с устройства.

1. Откройте окно командной строки и установите обозреватель Центра Интернета вещей. 

    ```
    npm install -g iothub-explorer
    ```

2. В командной строке выполните следующую команду, чтобы начать мониторинг сообщений, отправляемых с устройства в облако. Используйте строку подключения Центра Интернета вещей в заполнителе после `--login`.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. Откройте новое окно командной строки и перейдите к каталогу, содержащему файл **SimulatedDevice.py**.

4. Запустите файл **SimulatedDevice.py**, который периодически отправляет данные телеметрии в Центр Интернета вещей. 
   
    ```
    python SimulatedDevice.py
    ```
5. Просмотрите сообщения с устройства в командной строке, запущенной обозревателем Центра Интернета вещей, из предыдущего раздела. 

    ![Сообщения Python, отправляемые с устройства в облако][2]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Это удостоверение позволяет приложению виртуального устройства отправлять в Центр Интернета вещей сообщения, передаваемые из устройства в облако. Мы просмотрели сообщения, полученные Центром Интернета вещей, с помощью средства обозревателя. 

Чтобы узнать больше об использовании пакета SDK Python для Центра Интернета вещей Azure, перейдите на страницу [этого репозитория GitHub][lnk-python-github]. Чтобы просмотреть возможности обмена сообщениями пакета SDK службы Центра Интернета вещей Azure для Python, можно скачать и запустить [iothub_messaging_sample.py][lnk-messaging-sample]. Для имитации устройства с помощью пакета SDK устройства Центра Интернета вещей Azure для Python можно скачать и запустить [iothub_client_sample.py][lnk-client-sample].

Чтобы продолжить знакомство с центром IoT и изучить другие сценарии IoT, см. следующие ресурсы:

* [Подключение устройства к Azure IoT][lnk-connect-device]
* [How to get started with device management (Node)][lnk-device-management] (Начало работы с управлением устройствами (Node))
* [Explore Azure IoT Edge architecture on Linux][lnk-iot-edge] (Приступая к работе с архитектурой Azure IoT Edge в Linux)

Сведения о том, как расширить решение для Интернета вещей и обрабатывать сообщения, отправляемые с устройства в облако в большом количестве, см. [здесь][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

