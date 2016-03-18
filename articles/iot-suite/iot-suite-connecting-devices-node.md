<properties
   pageTitle="Подключение устройства с помощью Node.js | Microsoft Azure"
   description="Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения, написанного на Node.js."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="dobett"/>


# Подключение устройства к предварительно настроенному решению для удаленного мониторинга IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Выполнение сборки и запуск примера решения node.js

1. Чтобы клонировать репозиторий GitHub *пакетов SDK для Microsoft Azure IoT* и установить *пакет SDK для устройств Microsoft Azure IoT для Node.js* в вашей среде рабочего стола, следуйте инструкциям в разделе [Подготовка среды разработки][lnk-github-prepare].

2. Из папки node/device/samples в локальной копии репозитория [azure-iot-sdks][lnk-github-repo] скопируйте в папку на устройстве следующие два файла:

  - packages.json
  - remote\_monitoring.js

3. Откройте файл remote-monitoring.js и найдите следующие переменные:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Замените **[IoT Hub device connection string]** строкой подключения устройства. Значения имени узла центра IoT, идентификатора устройства и ключа устройства можно найти на панели мониторинга решения для удаленного мониторинга. Строка подключения имеет следующий формат:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Если имя узла центра IoT — **contoso**, а код устройства — **mydevice**, то строка подключения будет выглядеть так:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Сохраните файл. Выполните следующие команды в командной строке в папке, содержащей эти файлы, для установки необходимых пакетов, а затем запустите пример приложения:

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-node-installers]: https://nodejs.org/download/
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

<!---HONumber=AcomDC_0211_2016-->