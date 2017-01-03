---
title: "Подключение устройства с помощью Node.js | Документация Майкрософт"
description: "Описывает, как подключить устройство к предварительно настроенному решению для удаленного мониторинга из набора Azure IoT Suite с помощью приложения, написанного на Node.js."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 58c0d6590e2fd1684ebeee473029ca4544e223f6


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Выполнение сборки и запуск примера решения node.js
1. Чтобы клонировать репозиторий GitHub *пакетов SDK для Интернета вещей Microsoft Azure* и установить *пакет SDK для устройств Microsoft Azure IoT для Node.js* в вашей среде рабочего стола, следуйте инструкциям в статье [Подготовка среды разработки][lnk-github-prepare].
2. Из папки node/device/samples в локальной копии репозитория [azure-iot-sdks][lnk-github-repo] скопируйте в папку на устройстве следующие два файла:
   
   * packages.json
   * remote_monitoring.js
3. Откройте файл remote-monitoring.js и найдите следующие переменные:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
4. Замените **[IoT Hub device connection string]** строкой подключения устройства. Значения имени узла центра IoT, идентификатора устройства и ключа устройства можно найти на панели мониторинга решения для удаленного мониторинга. Строка подключения имеет следующий формат:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Если имя узла Центра Интернета вещей — **contoso**, а идентификатор устройства — **mydevice**, то строка подключения будет выглядеть следующим образом:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
5. Сохраните файл. Выполните следующие команды в командной строке в папке, содержащей эти файлы, для установки необходимых пакетов, а затем запустите пример приложения:
   
    ```
    npm install --save
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md



<!--HONumber=Nov16_HO5-->


