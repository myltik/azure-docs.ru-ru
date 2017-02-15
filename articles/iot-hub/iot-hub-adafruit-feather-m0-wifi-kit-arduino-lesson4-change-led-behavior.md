---
title: "Изменение режима мигания индикатора с помощью сообщений из Центра Интернета вещей Azure | Документация Майкрософт"
description: "Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "управление светодиодным индикатором с помощью Arduino"
ms.assetid: d7a25430-450e-43c4-a3ed-1eed995b8b7e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: fa69c49028c20ce657d3f0050c98ebaae7a56051


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Изменение режима включения и отключения светодиодного индикатора
## <a name="what-you-will-do"></a>Выполняемая задача
Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора. Если возникнут какие-либо проблемы в работе платы Adafruit Feather M0 WiFi Arduino, решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания
Использование дополнительных функций Arduino для изменения режима включения и отключения светодиодного индикатора.

## <a name="what-you-need"></a>Необходимые элементы
Необходимо успешно выполнить [запуск примера приложения на плате Arduino для получения сообщений из облака на устройство][receive-cloud-to-device-messages].

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>Добавление функций в файлы main.c и gulpfile.js
1. Откройте пример приложения в Visual Studio Code, выполнив следующие команды:

   ```bash
   cd Lesson4
   code .
   ```
2. Откройте файл `app.ino` и после функции blinkLED() добавьте следующие функции:

   ```arduino
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![Файл app.ino с добавленными функциями][app-ino-file]
3. Добавьте следующие условия перед блоком `else if` функции `receiveMessageCallback`:

   ```arduino
   else if (strcmp((const char*)value, "\"on\"") == 0)
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\"") == 0)
   {
       turnOffLED();
   }
   ```

   Теперь пример приложения настроен для ответа на дополнительные инструкции с помощью сообщений. Инструкция on включает светодиодный индикатор, а инструкция off его отключает.
4. Откройте файл gulpfile.js и добавьте новую функцию перед функцией `sendMessage`:

   ```javascript
   var buildCustomMessage = function (messageId) {
     if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
       return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
     } else if (messageId < MAX_MESSAGE_COUNT) {
       return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
     } else {
       return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
     }
   };
   ```

   ![Файл gulpfile.js с добавленной функцией][gulp-file-js]
5. В функции `sendMessage` замените строку `var message = buildMessage(sentMessageCount);` новой строкой, показанной в следующем фрагменте:

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Сохраните все изменения.

### <a name="deploy-and-run-the-sample-application"></a>Развертывание и запуск примера приложения
Разверните и запустите пример приложения на плате Arduino, выполнив следующую команду:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

Светодиодный индикатор должен включиться на две секунды, а затем на две секунды выключиться. Последнее сообщение stop останавливает выполнение примера приложения.

![on и off][on-and-off]

Поздравляем! Вы успешно настроили сообщения, отправляемые на плату Arduino из Центра Интернета вещей.

### <a name="summary"></a>Сводка
В этом дополнительном разделе показано, как настроить сообщения таким образом, чтобы пример приложения мог управлять включением и отключением светодиодного индикатора разными способами.

<!-- Images and links -->

[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[app-ino-file]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_app_ino.png
[gulp-file-js]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_gulpfile_js.png
[on-and-off]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_on_and_off_arduino.png


<!--HONumber=Dec16_HO2-->


