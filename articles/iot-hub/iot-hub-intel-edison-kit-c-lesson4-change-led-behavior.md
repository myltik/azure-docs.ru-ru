---
title: "Подключение Intel Edison (C) к Интернету вещей Azure. Урок 4. Включение и отключение светодиодного индикатора | Документация Майкрософт"
description: "Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "управление светодиодным индикатором с помощью Arduino"
ms.assetid: 9826c55a-0e24-4296-ae54-29b7fe66436a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 278bdf74e2fa8f7074bb8f5ed8eae2d47402b299


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Изменение режима включения и отключения светодиодного индикатора
## <a name="what-you-will-do"></a>Выполняемая задача
Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-you-will-learn"></a>Новые знания
Использование дополнительных функций для изменения режима включения и отключения светодиодного индикатора.

## <a name="what-you-need"></a>Необходимые элементы
Необходимо успешно выполнить [запуск примера приложения на устройстве Intel Edison для получения сообщений из облака на устройство][receive-cloud-to-device-messages].

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>Добавление функций в файлы main.c и gulpfile.js
1. Откройте пример приложения в Visual Studio Code, выполнив следующие команды:

   ```bash
   cd Lesson4
   code .
   ```
2. Откройте файл `main.c` и после функции blinkLED() добавьте следующие функции:

   ```c
   static void turnOnLED()
   {
     mraa_gpio_write(context, 1);
   }

   static void turnOffLED()
   {
     mraa_gpio_write(context, 0);
   }
   ```

   ![Файл main.c с добавленными функциями](media/iot-hub-intel-edison-lessons/lesson4/updated_app_c.png)

3. Добавьте следующие условия перед блоком `else if` функции `receiveMessageCallback`:

   ```c
   else if (0 == strcmp((const char*)value, "\"on\""))
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\""))
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

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_c.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_c.png



<!--HONumber=Jan17_HO4-->


