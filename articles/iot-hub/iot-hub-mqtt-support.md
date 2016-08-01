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
 ms.date="07/19/2016"
 ms.author="dobett"/>

# Поддержка MQTT в центре IoT

Центр IoT позволяет устройствам взаимодействовать с конечными точками устройств центра IoT с помощью протокола [MQTT v3.1.1][lnk-mqtt-org] и порта 8883. Центр IoT требует того, чтобы весь обмен данными с устройствами был защищен с помощью протокола TLS/SSL.

## Подключение к центру IoT

Устройство может подключаться к центру IoT по протоколу MQTT с помощью библиотек в [пакетах SDK для IoT Microsoft Azure][lnk-device-sdks] или напрямую с помощью протокола MQTT.

## Использование пакетов SDK для клиентов устройств

[Клиентские пакеты SDK устройств][lnk-device-sdks], поддерживающие протокол MQTT, доступны для Java, Node.js, C и C#. Для установки подключения к центру IoT клиентские пакеты SDK устройств используют стандартную строку подключения к центру IoT. Чтобы использовать протокол MQTT, параметр протокола клиента должен быть задан как **MQTT**. По умолчанию клиентские пакеты SDK устройств подключаются к центру IoT с флагом **CleanSession** со значением **0** и используют **QoS 1** для обмена сообщениями с центром IoT.

Когда устройство подключено к центру IoT, клиентский пакет SDK устройства предоставляет методы, позволяющие устройству отправлять сообщения в центр IoT и получать сообщения из центра IoT.

В следующей таблице содержатся ссылки на примеры кода для каждого поддерживаемого языка и указаны параметры, используемые для подключения к центру IoT по протоколу MQTT.

| Язык | Параметр протокола |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

## Непосредственное использование протокола MQTT

Если устройство не может использовать клиентские пакеты SDK, оно может подключаться к общедоступным конечным точкам устройства по протоколу MQTT. В пакете **CONNECT** устройство должно использовать следующие значения.

- В поле **Идентификатор клиента** укажите **идентификатор устройства**.
- В поле **Имя пользователя** укажите `{iothubhostname}/{device_id}`, где {iothubhostname} — это полная запись CName центра IoT.

    Например, если имя центра IoT — **contoso.azure-devices.net**, а имя устройства — **MyDevice01**, то полное поле **Имя пользователя** должно содержать `contoso.azure-devices.net/MyDevice01`.

- В поле **Пароль** укажите маркер SAS. Формат маркера SAS аналогичен описанному для протоколов HTTP и AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Дополнительные сведения о способах создания маркеров SAS см. в разделе описания устройств статьи [Использование маркеров безопасности центра IoT][lnk-sas-tokens].
    
    При тестировании также можно использовать [обозреватель устройства][lnk-device-explorer] для быстрого создания маркера SAS, который можно скопировать и вставить в собственный код.
    
    1. Перейдите на вкладку **Управление** в обозревателе устройства.
    2. Щелкните **Маркер SAS** (вверху справа).
    3. В разделе **SASTokenForm** выберите свое устройство из раскрывающегося списка **DeviceID**. Задайте значение **срока жизни**.
    4. Щелкните **Создать**, чтобы создать маркер.
    
    Созданный маркер SAS выглядит следующим образом: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    Его часть используется в поле **Пароль** для подключения с использованием MQTT: `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Для пакетов подключения и отключения MQTT центр IoT создает событие в канале **мониторинга операций**.

### Отправка сообщений в центр IoT

После успешного подключения устройство может отправлять сообщения в центр IoT, используя `devices/{device_id}/messages/events/` или `devices/{device_id}/messages/events/{property_bag}` в качестве значений параметра **Имя раздела**. Элемент `{property_bag}` позволяет устройству отправлять сообщения с дополнительными свойствами в формате URL-адреса. Например:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Эта же кодировка используется для строк запросов в протоколе HTTP.

Клиентское приложение устройства может использовать `devices/{device_id}/messages/events/{property_bag}` в качестве значения параметра **Will topic name** (Будущее имя раздела) для определения *будущих сообщений*, которые будут пересылаться как сообщения телеметрии.

### Получение сообщений

Для получения сообщений из центра IoT устройство должно оформить подписку с использованием `devices/{device_id}/messages/devicebound/#”` в качестве значения параметра **Topic Filter** (Фильтр разделов). Центр IoT доставляет сообщения с **именем раздела** `devices/{device_id}/messages/devicebound/` или `devices/{device_id}/messages/devicebound/{property_bag}`, если существуют какие-либо свойства сообщения. `{property_bag}` содержит закодированные в формате URL-адреса пары "ключ-значение" свойств сообщения. В контейнер свойств входят только свойства приложений и задаваемые пользователем системные свойства (такие как **messageId** или **correlationId**). Имена системных свойств имеют префикс **$**, свойства приложений используют исходное имя свойства без префикса.

## Дальнейшие действия

Дополнительные сведения о поддержке MQTT пакетами SDK для устройств IoT см. в подразделе [Примечания о поддержке протокола MQTT][lnk-mqtt-devguide] руководства разработчика по центру Azure IoT.

Дополнительные сведения о протоколе MQTT см. в [документации по MQTT][lnk-mqtt-docs].

Дополнительные сведения о планировании развертывания центра IoT см. в следующих руководствах:

- [Поддерживаемые устройства][lnk-devices]
- [Поддержка дополнительных протоколов для центра IoT][lnk-protocols]
- [Сравнение центра IoT и концентраторов событий][lnk-compare]
- [Масштабирование центра IoT][lnk-scaling]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

- [Руководство разработчика по центру Azure IoT (IoT — Интернет вещей)][lnk-devguide]
- [Обзор управления устройствами центра IoT с помощью примера пользовательского интерфейса][lnk-dmui]
- [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]
- [Управление центрами IoT через портал Azure][lnk-portal]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-sas-tokens.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide.md#mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0720_2016-->