---
title: "Запуск примера приложения для отправки сообщений с устройства в облако в Центр Интернета вещей Azure | Документация Майкрософт"
description: "Развертывание и запуск на устройстве Intel Edison примера приложения, которое отправляет сообщения в Центр Интернета вещей и заставляет светодиодный индикатор мигать."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "облачная служба Интернета вещей, отправка данных в облако с помощью Arduino"
ms.assetid: 12672b64-795a-4dfc-86fd-df53ed3eeef7
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: cb18052f74528e245264bb2f400b625fe550ed85
ms.openlocfilehash: 59db4ca0afcfd2757a725f5d56c0810f6547c924


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Запуск примера приложения для отправки сообщений с устройства в облако
## <a name="what-you-will-do"></a>Выполняемая задача
В этой статье показано, как развернуть и запустить на устройстве Intel Edison пример приложения, которое отправляет сообщения в Центр Интернета вещей. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

## <a name="what-you-will-learn"></a>Новые знания
Вы узнаете, как с помощью инструмента Gulp развернуть и запустить пример приложения C на устройстве Edison.

## <a name="what-you-need"></a>Необходимые элементы
* К выполнению этой задачи следует приступать только после выполнения действий, описанных в статье [Создание приложения-функции Azure и учетной записи хранения Azure][process-and-store-iot-hub-messages].

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Получение строк подключения Центра Интернета вещей и устройства
Строка подключения устройства используется для подключения устройства Edison к Центру Интернета вещей. Строка подключения Центра Интернета вещей используется для его подключения к удостоверению устройства, которое представляет устройство Edison в Центре Интернета вещей.

* Для вывода списка всех Центров Интернета вещей в группе ресурсов выполните следующую команду Azure CLI:

```bash
az iot hub list -g iot-sample --query [].name
```

Используйте `iot-sample` в качестве значения `{resource group name}`, если вы не меняли это значение.

* Для получения строки подключения Центра Интернета вещей выполните следующую команду интерфейса командной строки Azure:

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` — это имя, которое вы указали при создании Центра Интернета вещей и регистрации устройства Edison.

* Для получения строки подключения устройства выполните следующую команду:

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myinteledison
```

Используйте `myinteledison` в качестве значения `{device id}`, если вы не меняли это значение.

## <a name="configure-the-device-connection"></a>Настройка подключения устройства
1. Запустите файл конфигурации, выполнив приведенную ниже команду.

   ```bash
   npm install
   gulp init
   ```
   > [!NOTE]
   > Выполните также **gulp install-tools**, если это не было сделано на уроке 1.

2. Откройте файл конфигурации устройства `config-edison.json` в Visual Studio Code, выполнив приведенную ниже команду.

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

   ![config.json](media/iot-hub-intel-edison-lessons/lesson3/config.png)

3. В файле `config-edison.json` выполните следующие замены.

   * Замените **[device hostname or IP address]** IP-адресом, указанным при настройке устройства.
   * Замените **[строка подключения устройства Интернета вещей]** на полученное значение `device connection string`.
   * Замените **[IoT hub connection string]** (строка подключения Центра Интернета вещей) полученным значением `iot hub connection string`.

   > [!NOTE]
   > В этой статье не требуется `azure_storage_connection_string`. Оставьте эту настройку без изменений.

## <a name="deploy-and-run-the-sample-application"></a>Развертывание и запуск примера приложения
Разверните и запустите пример приложения в Edison, выполнив следующую команду:

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>Проверка работы примера приложения
Светодиодный индикатор, подключенный к устройству Edison, должен мигать с частотой в две секунды. Каждый раз, когда индикатор мигает, пример приложения отправляет сообщение в Центр Интернета вещей и проверяет, что сообщение успешно отправлено. Кроме того, каждое сообщение, полученное Центром Интернета вещей, выводится в окне консоли. Пример приложения автоматически завершает работу после отправки 20 сообщений.

![Пример приложения с отправленными и полученными сообщениями][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>Сводка
Вы развернули и запустили новый пример приложения для включения индикатора на устройстве Edison для отправки сообщений с устройства в облако, то есть в Центр Интернета вещей. Теперь можно отслеживать сообщения по мере их записывания в учетную запись хранения.

## <a name="next-steps"></a>Дальнейшие действия
[Чтение сообщений, сохраненных в службе хранилища Azure][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-intel-edison-lessons/lesson3/gulp_run_c.png
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-c-lesson3-read-table-storage.md


<!--HONumber=Dec16_HO2-->


