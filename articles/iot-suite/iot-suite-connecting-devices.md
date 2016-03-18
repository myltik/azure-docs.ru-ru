<properties
   pageTitle="Подключение устройства с помощью C в Windows | Microsoft Azure"
   description="Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения на C в среде Windows."
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

## Выполнение сборки и запуск примера решения на C в Windows

1. Чтобы клонировать репозиторий GitHub *пакетов SDK для Microsoft Azure IoT* и установить *пакет SDK для устройств Microsoft Azure IoT для C* в среде рабочего стола Windows, следуйте инструкциям в разделе [Настройка среды разработки Windows][lnk-setup-windows].

2. В Visual Studio 2015 откройте решение **remote\_monitoring.sln**, расположенное в папке **c\\serializer\\samples\\remote\_monitoring\\windows** в локальной копии репозитория.

3. В **обозревателе решений** в проекте **remote\_monitoring** откройте файл **remote\_monitoring.c**.

4. Найдите в файле следующий код:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

5. Замените **[Device Id]** и **[Device Key]** значениями для устройства, отображенными на панели мониторинга решения для удаленного мониторинга.

6. Используйте **имя узла центра IoT** на панели мониторинга, чтобы заменить **[IoTHub Name]** и **[IoTHub Suffix, i.e. azure-devices.net]**. Например, если **имя узла центра IoT** — **contoso.azure-devices.net**, замените **[IoTHub Name]** значением **contoso**, а **[IoTHub Suffix, i.e. azure-devices.net]** — значением **azure-devices.net**, как показано ниже.

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

7. В **обозревателе решений** щелкните правой кнопкой мыши проект **remote\_monitoring**, нажмите кнопку **Отладка**, а затем щелкните **Запустить новый экземпляр**, чтобы выполнить сборку примера и запустить его. В консоли отображаются сообщения об отправке приложением примеров данных телеметрии в центр IoT.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=AcomDC_0211_2016-->