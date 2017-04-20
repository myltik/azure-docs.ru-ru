---
title: "Подключение Intel Edison (Node) к Интернету вещей Azure. Урок 3. Отслеживание сообщений | Документация Майкрософт"
description: "Отслеживайте сообщения, передаваемые с устройства в облако, по мере их записывания в хранилище таблиц Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "данные в облаке, коллекция облачных данных, облачная служба Интернета вещей, данные Интернета вещей"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: fa2c7efe-7e34-4e39-bb70-015c15ac69ed
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 5100e6dbd74095f419c1017a3784fac547bf200b
ms.lasthandoff: 01/25/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>Чтение сообщений, сохраненных в службе хранилища Azure
## <a name="what-you-will-do"></a>Выполняемая задача
Отслеживание сообщений, передаваемых с устройства в облако (то есть с устройства Intel Edison в центр Интернета вещей), по мере их записывания в Хранилище таблиц Azure. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете, как использовать задачу gulp read-message для чтения сообщений, сохраненных в хранилище таблиц Azure.

## <a name="what-you-need"></a>Необходимые элементы
Прежде чем начать, необходимо успешно выполнить инструкции, изложенные в статье [Run a sample application to send device-to-cloud messages][run-the-azure-blink-sample-application-on-intel-edison] (Запуск примера приложения для отправки сообщений с устройства в облако).

## <a name="read-new-messages-from-your-storage-account"></a>Чтение новых сообщений из учетной записи хранения
В предыдущей статье вы запустили пример приложения на устройстве Edison. Пример приложения отправлял сообщения в центр Интернета вещей Azure. Сообщения, отправленные в центр Интернета вещей, сохраняются в хранилище таблиц Azure с помощью приложения-функции Azure. Для чтения сообщений из хранилища таблиц Azure потребуется строка подключения к хранилищу Azure.

Чтобы прочитать сообщения, сохраненные в хранилище таблиц Azure, выполните следующие действия:

1. Получите строку подключения, выполнив следующую команду:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   Первая команда получает имя `storage name`, которое используется во второй команде для получения строки подключения. Используйте `iot-sample` в качестве значения `{resource group name}`, если вы не меняли это значение.
2. Откройте файл конфигурации `config-edison.json` в Visual Studio Code, выполнив следующую команду.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```
3. Замените `[Azure storage connection string]` строкой подключения, полученной на шаге 1.
4. Сохраните файл `config-edison.json`.
5. Отправьте сообщения еще раз и считайте их из хранилища таблиц Azure, выполнив следующую команду.

   ```bash
   gulp run --read-storage
   ```

   Логика чтения из хранилища таблиц Azure содержится в файле `azure-table.js`.

   ![gulp run --read-storage][gulp run]

## <a name="summary"></a>Сводка
Вы успешно подключили устройство Edison к Центру Интернета вещей в облаке и с помощью примера приложения для включения индикатора отправили сообщения с устройства в облако. Также вы использовали приложение-функцию Azure для сохранения входящих сообщений из Центра Интернета вещей в хранилище таблиц Azure. Теперь можно перейти к отправке сообщений из облака на устройство, т. е. из Центра Интернета вещей на устройство Edison.

## <a name="next-steps"></a>Дальнейшие действия
[Запуск примера приложения для получения сообщений из облака на устройство][receive-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[run-the-azure-blink-sample-application-on-intel-edison]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
[gulp run]: media/iot-hub-intel-edison-lessons/lesson3/gulp_read_message.png
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
