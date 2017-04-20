---
title: "Подключение Raspberry Pi (Node) к Интернету вещей Azure. Урок 4. Изменение приложения | Документация Майкрософт"
description: "Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "управление светодиодным индикатором с помощью raspberry pi, raspberry pi управление индикатором, управлять светодиодным индикатором raspberry pi"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: 3b42a4ad-0197-42f6-8ca9-04c883e879e8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 53c9408bedc6c61cdb0e755b46b3090e8723e271
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Изменение режима включения и отключения светодиодного индикатора
## <a name="what-you-will-do"></a>Выполняемая задача
Настроив сообщения, вы сможете изменять режим включения и отключения светодиодного индикатора. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания
Использование дополнительных функций Node.js для изменения режима включения и отключения светодиодного индикатора.

## <a name="what-you-need"></a>Необходимые элементы
Необходимо успешно выполнить [запуск примера приложения на устройстве Raspberry Pi для получения сообщений из облака на устройство](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

## <a name="add-nodejs-functions"></a>Добавление функций Node.js
1. Откройте пример приложения в Visual Studio Code, выполнив следующие команды:
   
   ```bash
   cd Lesson4
   code .
   ```
2. Откройте файл `app.js` и добавьте в конце следующие функции:
   
   ```javascript
   function turnOnLED() {
     wpi.digitalWrite(CONFIG_PIN, 1);
   }
   
   function turnOffLED() {
     wpi.digitalWrite(CONFIG_PIN, 0);
   }
   ```
   
   ![Файл app.js с добавленными функциями](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)
3. Добавьте следующие условия перед условием по умолчанию в блоке переключения функции `receiveMessageCallback`:
   
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
   
   ![Файл gulpfile.js с добавленной функцией](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)
5. В функции `sendMessage` замените строку `var message = buildMessage(sentMessageCount);` новой строкой, показанной в следующем фрагменте:
   
   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Сохраните все изменения.

### <a name="deploy-and-run-the-sample-application"></a>Развертывание и запуск примера приложения
Разверните и запустите пример приложения на устройстве Pi, выполнив следующую команду.

```bash
gulp deploy && gulp run
```

Светодиодный индикатор должен включиться на две секунды, а затем на две секунды выключиться. Последнее сообщение stop останавливает выполнение примера приложения.

![Пример приложения с сообщениями включения и отключения](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

Поздравляем! Вы успешно настроили сообщения, отправляемые на устройство Pi из Центра Интернета вещей.

### <a name="summary"></a>Сводка
В этом дополнительном разделе показано, как настроить сообщения таким образом, чтобы пример приложения мог управлять включением и отключением светодиодного индикатора разными способами.


