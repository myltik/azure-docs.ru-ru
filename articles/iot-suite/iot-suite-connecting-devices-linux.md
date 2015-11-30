<properties
   pageTitle="Подключение устройства с помощью C в Linux | Microsoft Azure"
   description="Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения на C в среде Linux."
   services=""
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# Подключение устройства к предварительно настроенному решению для удаленного мониторинга IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Выполнение сборки и запуск примера решения на C в Linux

1. Чтобы клонировать репозиторий GitHub *пакетов SDK для Microsoft Azure IoT* и установить *пакет SDK для устройств Microsoft Azure IoT для C* в среде рабочего стола Linux, следуйте инструкциям в разделе [Настройка среды разработки Linux][lnk-setup-linux].

2. Откройте файл **c/serializer/samples/remote\_monitoring/remote\_monitoring.c** в текстовом редакторе.

3. Найдите в файле следующий код:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. Замените **[Device Id]** и **[Device Key]** значениями для устройства, отображенными на панели мониторинга решения для удаленного мониторинга.

5. Используйте **имя узла центра IoT** на панели мониторинга, чтобы заменить **[IoTHub Name]** и **[IoTHub Suffix, i.e. azure-devices.net]**. Например, если **имя узла центра IoT** — **contoso.azure-devices.net**, замените **[IoTHub Name]** значением **contoso**, а **[IoTHub Suffix, i.e. azure-devices.net]** — значением **azure-devices.net**, как показано ниже.

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. Сохраните изменения и соберите примеры. Чтобы выполнить сборку примера, запустите сценарий build.sh из папки **build\_all/c/linux**. Сценарий сборки создает папку **cmake** для хранения скомпилированных примеров программ.

7. Запустите пример приложения **cmake/serializer/samples/remote\_monitoring/remote\_monitoring**.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=Nov15_HO4-->