---
title: "Подключение Intel Edison (Node) к Интернету вещей Azure. Урок 4. Включение и отключение светодиодного индикатора | Документация Майкрософт"
description: "Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "управление светодиодным индикатором с помощью Arduino"
ms.assetid: 387cd97e-b05e-43c4-b252-f68ad45d524a
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 9a2c6dfe5d85a4da9714f9b8942cb2baf1a98466
ms.lasthandoff: 01/25/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Изменение режима включения и отключения светодиодного индикатора
## <a name="what-you-will-do"></a>Выполняемая задача
Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-you-will-learn"></a>Новые знания
Использование дополнительных функций для изменения режима включения и отключения светодиодного индикатора.

## <a name="what-you-need"></a>Необходимые элементы
Необходимо успешно выполнить [запуск примера приложения на устройстве Intel Edison для получения сообщений из облака на устройство][receive-cloud-to-device-messages].

## <a name="add-functions-to-appjs-and-gulpfilejs"></a>Добавление функций в файлы app.js и gulpfile.js
1. Откройте пример приложения в Visual Studio Code, выполнив следующие команды:

   ```bash
   cd Lesson4
   code .
   ```
2. Откройте файл `app.js` и после функции blinkLED() добавьте следующие функции:

   ```javascript
   function turnOnLED() {
     myLed.write(1);
   }

   function turnOffLED() {
     myLed.write(0);
   }
   ```

   ![Файл app.js с добавленными функциями](media/iot-hub-intel-edison-lessons/lesson4/updated_app_node.png)
3. Добавьте следующие условия перед блоком включения индикатора в блоке переключения функции `receiveMessageCallback`:

   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
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
   }
   ```

   ![Файл gulpfile.js с добавленной функцией][gulpfile]
5. В функции `sendMessage` замените строку `var message = buildMessage(sentMessageCount);` новой строкой, показанной в следующем фрагменте:

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Сохраните все изменения.

### <a name="deploy-and-run-the-sample-application"></a>Развертывание и запуск примера приложения
Разверните и запустите пример приложения в Edison, выполнив следующую команду:

```bash
gulp deploy && gulp run
```

Светодиодный индикатор должен включиться на две секунды, а затем на две секунды выключиться. Последнее сообщение stop останавливает выполнение примера приложения.

![on и off][on-and-off]

Поздравляем! Вы успешно настроили сообщения, отправляемые на устройство Edison из Центра Интернета вещей.

### <a name="summary"></a>Сводка
В этом дополнительном разделе показано, как настроить сообщения таким образом, чтобы пример приложения мог управлять включением и отключением светодиодного индикатора разными способами.

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_node.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_node.png

