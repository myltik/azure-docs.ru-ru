---
title: Поддержка MQTT в центре IoT | Microsoft Docs
description: Описание поддержки MQTT в центре IoT
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett

---
# <a name="iot-hub-mqtt-support"></a>Поддержка MQTT в центре IoT
Центр Интернета вещей позволяет устройствам взаимодействовать с конечными точками устройств Центра Интернета вещей с помощью протокола [MQTT версии 3.1.1][lnk-mqtt-org] и порта 8883. Центр IoT требует того, чтобы весь обмен данными с устройствами был защищен с помощью протокола TLS/SSL.

## <a name="connecting-to-iot-hub"></a>Подключение к центру IoT
Устройство может подключаться к Центру Интернета вещей по протоколу MQTT с помощью библиотек в [пакетах SDK для Интернета вещей Microsoft Azure][lnk-device-sdks] или напрямую с помощью протокола MQTT.

## <a name="using-the-device-client-sdks"></a>Использование пакетов SDK для клиентов устройств
Доступны [пакеты SDK для клиентов устройств][lnk-device-sdks], поддерживающие протокол MQTT, для Java, Node.js, C и C#. Для установки подключения к центру IoT клиентские пакеты SDK устройств используют стандартную строку подключения к центру IoT. Чтобы использовать протокол MQTT, параметр протокола клиента должен быть задан как **MQTT**. По умолчанию клиентские пакеты SDK устройств подключаются к Центру Интернета вещей, если для флага **CleanSession** установлено значение **0**, и используют **качество обслуживания первого уровня** для обмена сообщениями с Центром Интернета вещей.

Когда устройство подключено к центру IoT, клиентский пакет SDK устройства предоставляет методы, позволяющие устройству отправлять сообщения в центр IoT и получать сообщения из центра IoT.

В следующей таблице содержатся ссылки на примеры кода для каждого поддерживаемого языка и указаны параметры, используемые для подключения к центру IoT по протоколу MQTT.

| Язык | Параметр протокола |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrate-a-device-app-from-amqp-to-mqtt"></a>Переход от AMQP на MQTT в приложении устройства
При использовании [пакетов SDK для клиента устройства][lnk-device-sdks] для перехода с протокола AMQP на MQTT требуется изменить параметр протокола в инициализации клиента, как указано выше.

При этом обязательно проверьте следующее:

* AMQP возвращает ошибки для многих условий, а MQTT завершает подключение. В результате может потребоваться изменить логику обработки исключений.
* MQTT не поддерживает операции *отклонения* при получении [сообщений из облака в устройство][lnk-messaging]. Если нужно, чтобы серверная часть получала ответы от приложения устройства, стоит использовать [прямые методы][lnk-methods].
* В настоящее время MQTT невозможно использовать через WebSockets.

## <a name="using-the-mqtt-protocol-directly"></a>Непосредственное использование протокола MQTT
Если устройство не может использовать клиентские пакеты SDK, оно может подключаться к общедоступным конечным точкам устройства по протоколу MQTT. В пакете **CONNECT** устройство должно использовать следующие значения.

* В поле **Идентификатор клиента** укажите значение **идентификатор устройства**. 
* В поле **Имя пользователя** укажите значение `{iothubhostname}/{device_id}`, где {iothubhostname} — это полная запись CName Центра Интернета вещей.
  
    Например, если имя Центра Интернета вещей — **contoso.azure-devices.net**, а имя устройства — **MyDevice01**, то полное поле **Имя пользователя** должно содержать `contoso.azure-devices.net/MyDevice01`.
* В поле **Пароль** укажите маркер SAS. Формат маркера SAS аналогичен описанному для протоколов HTTP и AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.
  
    Дополнительные сведения о способах создания маркеров SAS см. в разделе описания устройств раздела [Использование маркеров безопасности Центра Интернета вещей][lnk-sas-tokens].
  
    При тестировании также можно использовать [обозреватель устройств][lnk-device-explorer] для быстрого создания маркера SAS, который можно скопировать и вставить в собственный код:
  
  1. Перейдите на вкладку **Управление** в обозревателе устройства.
  2. Щелкните **Маркер SAS** (вверху справа).
  3. В разделе **SASTokenForm** выберите свое устройство в раскрывающемся списке **DeviceID**. Задайте значение **срока жизни**.
  4. Щелкните **Создать** , чтобы создать маркер.
     
     Созданный маркер SAS имеет такую структуру:   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.
     
     Его часть используется в поле **Пароль** для подключения с использованием MQTT:   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Для пакетов подключения и отключения MQTT центр IoT создает событие в канале **мониторинга операций** .

### <a name="sending-messages-to-iot-hub"></a>Отправка сообщений в центр IoT
После успешного подключения устройство может отправлять сообщения в Центр Интернета вещей, используя `devices/{device_id}/messages/events/` или `devices/{device_id}/messages/events/{property_bag}` в качестве значения параметра **Имя раздела**. Элемент `{property_bag}` позволяет устройству отправлять сообщения с дополнительными свойствами в формате URL-адреса. Например:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> В этом элементе `{property_bag}` используется та же кодировка, что и для строк запросов в протоколе HTTP.
> 
> 

Клиентское приложение устройства может использовать `devices/{device_id}/messages/events/{property_bag}` в качестве значения параметра **Will topic name** (Будущее имя раздела) для определения *будущих сообщений* , которые будут пересылаться как сообщения телеметрии.

### <a name="receiving-messages"></a>Получение сообщений
Для получения сообщений из Центра Интернета вещей устройство должно подписаться с использованием `devices/{device_id}/messages/devicebound/#”` в качестве значения параметра **Topic Filter** (Фильтр разделов). Центр Интернета вещей передает сообщения с **именем раздела** `devices/{device_id}/messages/devicebound/` или `devices/{device_id}/messages/devicebound/{property_bag}` при наличии свойств сообщения. `{property_bag}` содержит закодированные в формате URL-адреса пары "ключ-значение" свойств сообщения. В контейнер свойств входят только свойства приложений и задаваемые пользователем системные свойства (такие как **messageId** или **correlationId**). Имена системных свойств имеют префикс **$**, свойства приложений используют исходное имя свойства без префикса.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в разделе [Примечания о поддержке протокола MQTT][lnk-mqtt-devguide] руководства разработчика по Центру Интернета вещей Azure.

Дополнительные сведения о протоколе MQTT см. в [документации по MQTT][lnk-mqtt-docs].

Дополнительные сведения о планировании развертывания центра IoT см. в следующих руководствах:

* [Поддерживаемые устройства][lnk-devices]
* [Поддержка дополнительных протоколов для центра IoT][lnk-protocols]
* [Сравнение центра IoT и концентраторов событий][lnk-compare]
* [Scaling, HA, and DR][lnk-scaling] (Масштабирование, высокая доступность и аварийное восстановление)

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Developer guide][lnk-devguide] (Руководство разработчика)
* [Simulating a device with the Gateway SDK][lnk-gateway] (Имитация устройства с помощью пакета SDK)

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


