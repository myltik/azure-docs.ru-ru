<properties
 pageTitle="Поддержка MQTT в центре IoT | Microsoft Azure"
 description="Описание поддержки MQTT в центре IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Поддержка MQTT в центре IoT

Центр IoT позволяет устройствам взаимодействовать с конечными точками устройств центра IoT с помощью протокола [MQTT v3.1.1][lnk-mqtt-org] и порта 8883. Центр IoT требует того, чтобы весь обмен данными с устройствами был защищен с помощью протокола TLS/SSL.

Дополнительные сведения см. в подразделе [Примечания о поддержке протокола MQTT][lnk-mqtt-devguide] руководства разработчика для центра Azure IoT.

## Подключение к центру IoT

Устройство может подключаться к центру IoT по протоколу MQTT с помощью библиотек в [пакетах SDK для Microsoft Azure IoT][lnk-device-sdks] или напрямую с помощью протокола MQTT.

## Использование пакетов SDK для клиентов устройств

Доступны [пакеты SDK для клиентов устройств][lnk-device-sdks], поддерживающие протокол MQTT, для Java, Node.js, C и C#. Для установки подключения к центру IoT клиентские пакеты SDK устройств используют стандартную строку подключения к центру IoT. Чтобы использовать протокол MQTT, должен быть задан параметр протокола клиента **MQTT**. По умолчанию пакеты SDK для клиентов устройств подключаются к центру IoT с флагом **CleanSession** со значением **0** и используют **QoS 1** для обмена сообщениями с центром IoT.

Когда устройство подключено к центру IoT, клиентский пакет SDK устройства предоставляет методы, позволяющие устройству отправлять сообщения в центр IoT и получать сообщения из центра IoT.

В следующей таблице содержатся ссылки на примеры кода для каждого поддерживаемого языка и указаны параметры, используемые для подключения к центру IoT по протоколу MQTT.

| Язык | Параметр протокола |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## Непосредственное использование протокола MQTT

Если устройство не может использовать клиентские пакеты SDK, оно может подключаться к общедоступным конечным точкам устройства по протоколу MQTT. В пакете **CONNECT** устройство должно использовать следующие значения.

- В поле **ClientId** укажите значение **deviceId**. 
- В поле **Имя пользователя** укажите `{iothubhostname}/{device_id}`, где {iothubhostname} — это полная запись CName центра IoT.

    Например, если имя центра IoT — **contoso.azure-devices.net** и имя устройства — **MyDevice01**, то полное поле **Имя пользователя** должно содержать `contoso.azure-devices.net/MyDevice01`.

- В поле **Пароль** укажите маркер SAS. [Формат маркера SAS][lnk-iothub-security] аналогичен описанному для протоколов HTTP и AMQP: <br/>`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`.

    Дополнительные сведения о способах создания маркеров SAS см. в разделе [Использование маркеров безопасности центра IoT][lnk-sas-tokens].
    
    При тестировании также можно использовать [обозреватель устройства][lnk-device-explorer] для быстрого создания маркера SAS, который можно скопировать и вставить в собственный код.
    
    1. Перейдите на вкладку **Управление** в обозревателе устройства.
    2. Щелкните **Маркер SAS** (вверху справа).
    3. В разделе **SASTokenForm** выберите свое устройство из раскрывающегося списка **DeviceID**. Задайте значение **срока жизни**.
    4. Щелкните **Создать**, чтобы создать маркер.
    
    Созданный маркер SAS выглядит следующим образом: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    Его часть используется в поле **Пароль** для подключения с использованием MQTT: `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Для пакетов подключения и отключения MQTT центр IoT создает событие в канале **мониторинга операций**.

### Отправка сообщений в центр IoT

После успешного подключения устройство может отправлять сообщения в центр IoT, используя `devices/{did}/messages/events/` или `devices/{did}/messages/events/{property_bag}` в качестве значений параметра **Имя раздела**. Элемент `{property_bag}` позволяет устройству отправлять сообщения с дополнительными свойствами в формате URL-адреса. Например:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Эта же кодировка используется для строк запросов в протоколе HTTP.

Приложение клиента устройства может использовать `devices/{did}/messages/events/{property_bag}` в качестве значения параметра **Will topic name** (Будущее имя раздела) для определения *будущих сообщений*, которые будут пересылаться как сообщения телеметрии.

### Получение сообщений

Для получения сообщений из центра IoT устройство должно оформить подписку с использованием `devices/{did}/messages/devicebound/#”` в качестве **фильтра разделов**. Центр IoT доставляет сообщения с **именем раздела** `devices/{did}/messages/devicebound/` или `devices/{did}/messages/devicebound/{property_bag}`, если имеются какие-либо свойства сообщения. `{property_bag}` содержит закодированные в формате URL-адреса пары "ключ-значение" свойств сообщения. В контейнер свойств входят только свойства приложений и задаваемые пользователем системные свойства (такие как **messageId** или **correlationId**). Имена системных свойств имеют префикс **$**, свойства приложений используют исходное имя свойства без префикса.

## Дальнейшие действия

Дополнительные сведения об использовании пакетов SDK для клиентов устройств для взаимодействия с центром IoT см. в разделе [Приступая к работе с центром Azure IoT с использованием .NET][lnk-iot-get-stated].

Дополнительные сведения о протоколе MQTT см. в [документации по MQTT][lnk-mqtt-docs].

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-iothub-security]: iot-hub-devguide.md#security
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-mqtt-devguide]: iot-hub-devguide.md#mqtt-support

<!---HONumber=AcomDC_0406_2016-->