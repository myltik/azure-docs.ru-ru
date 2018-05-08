---
title: Отправка файлов с устройств в Центр Интернета вещей с помощью Node.js | Документация Майкрософт
description: Сведения об отправке файлов с устройства в облако с помощью пакета SDK для устройств Azure IoT для Node.js. Отправленные файлы хранятся в контейнере больших двоичных объектов службы хранилища Azure.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: b28a02462fe7a5a7f831102b3707fe03f84342ad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Передача файлов с устройства в облако с помощью Центра Интернета вещей

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

В этом руководстве используется код статьи [Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (Java)](iot-hub-node-node-c2d.md), чтобы показать, как использовать [возможности передачи файлов Центра Интернета вещей](iot-hub-devguide-file-upload.md) для передачи файлов в [хранилище BLOB-объектов Azure](../storage/index.yml). В этом руководстве описаны следующие процедуры.

- как безопасно предоставить устройству универсальный код ресурса (URI) большого двоичного объекта Azure для передачи файла;
- как использовать уведомления о передаче файлов Центра Интернета вещей для активации обработки файла в серверной части приложения.

В руководстве [Подключение виртуального устройства к Центру Интернета вещей с помощью Node](iot-hub-node-node-getstarted.md) демонстрируется базовая функциональность Центра Интернета вещей при передаче сообщений с устройства в облако. Тем не менее в некоторых случаях не просто сопоставить данные, отправляемые устройством, с относительно небольшими сообщениями, отправляемыми из устройства в облако, которые принимает Центр Интернета вещей. Например: 

* Большие файлы, содержащие изображения.
* Видеоролики
* Данные вибрации с высокой частотой выборки.
* Некоторые виды предварительно обработанных данных.

Эти файлы обычно обрабатываются в виде пакета в облаке с помощью таких инструментов, как [фабрика данных Azure](../data-factory/introduction.md) или стек [Hadoop](../hdinsight/index.yml). При передаче файлов с устройства вы можете рассчитывать на безопасность и надежность Центра Интернета вещей.

По завершении работы с этим руководством вы запустите два консольных приложения Node.js:

* **SimulatedDevice.js** — передает файл в хранилище с помощью универсального кода ресурса (URI) SAS, предоставленного вашим Центром Интернета вещей.
* **ReadFileUploadNotification.js** — получает уведомления о передаче файлов от Центра Интернета вещей.

> [!NOTE]
> Существуют пакеты SDK для устройств Azure IoT, обеспечивающие поддержку многих платформ устройств и языков (включая C, .NET, JavaScript, Python и Java) в Центре Интернета вещей. Пошаговые инструкции по подключению устройства к Центру Интернета вещей Azure см. в [Центре разработчика для Центра Интернета вещей Azure].

Для работы с этим учебником требуется:

* Node.js 4.0.x или более поздней версии.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](http://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Передача файла из приложения устройства

В этом разделе вы создадите приложение для устройства, чтобы отправлять файлы в Центр Интернета вещей.

1. Создайте пустую папку с именем ```simulateddevice```.  В папке ```simulateddevice``` создайте файл package.json, указав приведенную ниже команду в командной строке.  Примите значения по умолчанию:

    ```cmd/sh
    npm init
    ```

1. В командной строке в папке ```simulateddevice``` выполните следующую команду, чтобы установить пакет SDK для устройств **azure-iot-device** и пакет **azure-iot-device-mqtt**.

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. В текстовом редакторе создайте файл **SimulatedDevice.js** в папке ```simulateddevice```.

1. Добавьте следующие инструкции ```require``` в начало файла **SimulatedDevice.js** :

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Добавьте переменную ```deviceconnectionstring```, чтобы создать с ее помощью экземпляр **клиента**.  Замените ```{deviceconnectionstring}``` именем устройства, созданного в разделе о _создании центра Интернета вещей_.

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Для простоты строка подключения добавляется в код, хотя мы не рекомендуем так делать. В зависимости от сценария использования и архитектуры могут потребоваться более безопасные способы хранения этого секрета.

1. Чтобы подключить клиент, добавьте следующий код:

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Создайте обратный вызов и отправьте файл с помощью функции **uploadToBlob**.

    ```nodejs
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);
    
        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

1. Сохраните и закройте файл **SimulatedDevice.js** .

1. Скопируйте файл образа в папку `simulateddevice` и переименуйте его на `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Получение уведомления о передачи файла

В этом разделе вам предстоит создать консольное приложение Node.js, которое получает уведомления об отправке файлов из Центра Интернета вещей.

Чтобы выполнить инструкции в этом разделе, вы можете использовать строку подключения **iothubowner** из Центра Интернета вещей. Строку подключения можно найти на [портале Azure](https://portal.azure.com/) в колонке **Политика общего доступа**.

1. Создайте пустую папку с именем ```fileuploadnotification```.  В папке ```fileuploadnotification``` создайте файл package.json, указав приведенную ниже команду в командной строке.  Примите значения по умолчанию:

    ```cmd/sh
    npm init
    ```

1. В командной строке в папке ```fileuploadnotification``` выполните следующую команду, чтобы установить пакет SDK **azure-iothub**:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. С помощью текстового редактора создайте файл **FileUploadNotification.js** в папке ```fileuploadnotification```.

1. Добавьте следующие инструкции ```require``` в начало файла **FileUploadNotification.js**:

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Добавьте переменную ```iothubconnectionstring```, чтобы создать с ее помощью экземпляр **клиента**.  Замените ```{iothubconnectionstring}``` строкой подключения к Центру Интернета вещей, созданному в _соответствующем разделе_.

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Для простоты строка подключения добавляется в код, хотя мы не рекомендуем так делать. В зависимости от сценария использования и архитектуры могут потребоваться более безопасные способы хранения этого секрета.

1. Чтобы подключить клиент, добавьте следующий код:

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Откройте клиент и воспользуйтесь функцией **getFileNotificationReceiver**, чтобы получить обновления состояния.

    ```nodejs
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Сохраните и закройте файл **FileUploadNotification.js**.

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

В командной строке в папке `fileuploadnotification` выполните следующую команду:

```cmd/sh
node FileUploadNotification.js
```

В командной строке в папке `simulateddevice` выполните следующую команду:

```cmd/sh
node SimulatedDevice.js
```

На следующем снимке экрана показаны выходные данные приложения **SimulatedDevice**:

![Выходные данные приложения simulated-device](./media/iot-hub-node-node-file-upload/simulated-device.png)

На следующем снимке экрана показаны выходные данные приложения **FileUploadNotification**:

![Выходные данные приложения read-file-upload-notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

На портале можно просмотреть отправленный файл в контейнере хранилища, который вы настроили.

![Отправленный файл](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Дополнительная информация

В этом руководство показано, как использовать возможности передачи файлов Центра Интернета вещей, чтобы упростить передачу файлов из устройств. Изучение функций и сценариев Центра Интернета вещей можно продолжить в следующих руководствах:

* [Создание Центра Интернета вещей с помощью PowerShell][lnk-create-hub]
* [Знакомство с пакетом SDK для устройств Центра Интернета вещей Azure для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

<!-- Links -->
[Центре разработчика для Центра Интернета вещей Azure]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
