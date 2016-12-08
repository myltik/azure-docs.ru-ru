## <a name="create-a-device-identity"></a>Создание удостоверения устройства
Для работы с руководством необходимо создать удостоверение устройства. В этом разделе рассматривается средство Node.js под названием [IoT Hub Explorer][iot-hub-explorer], или обозреватель устройств Центра Интернета вещей.

1. В среде командной строки выполните следующую команду:
   
    npm install -g iothub-explorer@latest
2. Затем выполните следующую команду для входа в Центр. Не забудьте заменить `{service connection string}` ранее скопированной строкой подключения к Центру Интернета вещей.
   
    iothub-explorer login "{строка подключения службы}"
3. Теперь создайте новое удостоверение устройства с именем `myDeviceId` при помощи следующей команды:
   
    iothub-explorer create myDeviceId --строка подключения

Запишите строку подключения устройства из результата. Строка подключения используется приложением устройства для подключения к Центру Интернета вещей как устройство.

![][img-identity]

Способы создания удостоверения устройства программными средствами описаны в статье [Приступая к работе с центром Azure IoT][lnk-getstarted].

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md


<!--HONumber=Nov16_HO3-->


