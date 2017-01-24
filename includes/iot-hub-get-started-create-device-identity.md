## <a name="create-a-device-identity"></a>Создание удостоверения устройства
В этом разделе рассматривается создание удостоверения устройства для этого руководства с помощью средства Node.js под названием [обозреватель Центра Ин ернета вещей][iot-hub-explorer].

1. В среде командной строки выполните следующую команду:
   
    npm install -g iothub-explorer@latest
2. Затем выполните следующую команду для входа в Центр. Не забудьте заменить `{iot hub connection string}` ранее скопированной строкой подключения к Центру Интернета вещей.
   
    iothub-explorer login "{строка подключения Центра Интернета вещей}"
3. Теперь создайте новое удостоверение устройства с именем `myDeviceId` при помощи следующей команды:
   
    iothub-explorer create myDeviceId --строка подключения

Запишите строку подключения устройства из результата. Строка подключения этого устройства используется приложением устройства для подключения к Центру Интернета вещей в качестве устройства.

![][img-identity]

Способы создания удостоверений устройства программными средствами описаны в статье [Getting started with IoT Hub][lnk-getstarted] (Приступая к работе с Центром Интернета вещей Azure).

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md


<!--HONumber=Dec16_HO1-->


