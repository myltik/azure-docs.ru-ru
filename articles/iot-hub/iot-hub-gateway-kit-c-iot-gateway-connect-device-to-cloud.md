---
title: "Использование шлюза Интернета вещей для подключения устройства к Центру Интернета вещей Azure | Документация Майкрософт"
description: "Узнайте, как использовать Intel NUC в качестве шлюза Интернета вещей для подключения TI SensorTag и отправлять данные датчиков в Центр Интернета вещей Azure в облаке."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "шлюз Интернета вещей для подключения устройства к облаку"
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: e8efc3204512b7213c44a15c25b38287465975df
ms.lasthandoff: 04/13/2017


---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>Использование шлюза Интернета вещей для подключения объектов к облаку (подключение SensorTag к Центру Интернета вещей Azure)

> [!NOTE]
> Перед началом работы с руководством убедитесь, что вы завершили [настройку Intel NUC в качестве шлюза Интернета вещей](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md). Из учебника [Настройка Intel NUC в качестве шлюза Интернета вещей](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) вы узнали, как настроить устройство Intel NUC таким образом.

## <a name="what-you-will-learn"></a>Новые знания

Вы научитесь использовать шлюз Интернета вещей для подключения устройства Texas Instruments SensorTag (CC2650STK) к Центру Интернета вещей Azure. Шлюз Центра Интернета вещей отправляет данные о температуре и влажности, собранные с устройства SensorTag, в Центр Интернета вещей Azure.

## <a name="what-you-will-do"></a>Выполняемая задача

- Создайте центр IoT.
- Зарегистрируйте устройство в Центре Интернета вещей для SensorTag.
- Включите подключение между шлюзом Интернета вещей и SensorTag.
- Запустите пример приложения BLE для отправки данных SensorTag в Центр Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

- Пройден учебник [Настройка Intel NUC в качестве шлюза Интернета вещей](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md), в котором рассказывается, как настроить устройство Intel NUC в качестве шлюза Интернета вещей.
- Клиент SSH, который выполняется на главном компьютере. В Windows рекомендуется использовать PuTTY. Клиент SSH изначально входит в состав ОС Linux и Mac OS.
- IP-адрес, имя пользователя и пароль для доступа к шлюзу из клиента SSH.
- Подключение к Интернету.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> Здесь следует зарегистрировать новое устройство для SensorTag.

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>Включение подключения между шлюзом Интернета вещей и SensorTag

В этом разделе описывается выполнение следующих задач:

- Получите MAC-адрес SensorTag для подключения Bluetooth.
- Инициируйте подключение Bluetooth из шлюза Интернета вещей к SensorTag.

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>Получение MAC-адреса SensorTag для подключения Bluetooth

1. На главном компьютере запустите клиент SSH и установите подключение к шлюзу Интернета вещей.
1. Разблокируйте Bluetooth, выполнив следующую команду:

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. Запустите службу Bluetooth на шлюзе Интернета вещей и войдите в оболочку Bluetooth для настройки Bluetooth, выполнив следующие команды:

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. Включите питание на контроллере Bluetooth, выполнив следующую команду в оболочке Bluetooth:

   ```bash
   power on
   ```

   ![Включение питания на контроллере Bluetooth в шлюзе Интернета вещей с помощью bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. Начните поиск находящихся поблизости устройств Bluetooth, выполнив следующую команду:

   ```bash
   scan on
   ```

   ![Поиск находящихся поблизости устройств Bluetooth с помощью bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. Нажмите кнопку связывания на SensorTag. На SensorTag начнет мигать зеленый индикатор.
1. В оболочке Bluetooth должно отобразиться найденное устройство SensorTag. Запишите MAC-адрес устройства SensorTag. В данном примере MAC-адрес устройства SensorTag — `24:71:89:C0:7F:82`.
1. Выключите поиск, выполнив следующую команду:

   ```bash
   scan off
   ```

   ![Остановка поиска находящихся поблизости устройств Bluetooth с помощью bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>Инициализация подключения Bluetooth к SensorTag из шлюза Интернета вещей

1. Подключитесь к SensorTag с помощью следующей команды:

   ```bash
   connect <MAC address>
   ```

   ![Подключение к SensorTag с помощью bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. Отключитесь от SensorTag и выйдите из оболочки Bluetooth с помощью следующей команды:

   ```bash
   disconnect
   exit
   ```

   ![Отключение от SensorTag с помощью bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

Вы успешно включили подключение между SensorTag и шлюзом Интернета вещей.

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>Запуск примера приложения BLE для отправки данных SensorTag в Центр Интернета вещей

Пример приложения для подключения по Bluetooth с низким энергопотреблением (BLE) предоставляется в пакете SDK шлюза Azure IoT. Этот пример приложения собирает данные, поступающие через подключение BLE, и отправляет их в центр Интернета вещей. Для запуска примера приложения необходимо выполнить следующие действия:

1. настроить пример приложения;
1. запустить его на шлюзе Интернета вещей.

### <a name="configure-the-sample-application"></a>Настройка примера приложения

1. Перейдите в папку примера приложения, выполнив следующую команду:

   ```bash
   cd /user/share/azureiotgatewaysdk/samples
   ```

1. Откройте файл конфигурации, выполнив такую команду:

   ```bash
   vi ble_gateway.json
   ```

1. В файле конфигурации задайте такие значения:

   **IoTHubName** — имя Центра Интернета вещей.

   **IoTHubSuffix** — получение IoTHubSuffix из первичного ключа в записанной ранее строке подключения устройства. Убедитесь, что вы используете первичный ключ из строки подключения устройства, а не из строки подключения Центра Интернета вещей. Первичный ключ строки подключения устройства указывается в формате `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY`.

   **Transport** По умолчанию используется значение `amqp`. Это значение указывает протокол во время транспортировки. Оно может быть равным `http`, `amqp` или `mqtt`.

   **macAddress** MAC-адрес SensorTag, который вы записали ранее.

   **deviceID** Идентификатор устройства, которое вы создали в Центре Интернета вещей.

   **deviceKey** Первичный ключ строки подключения устройства.

   ![Заполнение файла конфигурации примера приложения BLE](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. Нажмите клавишу `ESC` и введите `:wq`, чтобы сохранить файл.

### <a name="run-the-sample-application"></a>Запуск примера приложения

1. Убедитесь, что устройство SensorTag включено.
1. Выполните следующую команду:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Дальнейшие действия

[Использование шлюза IoT для преобразования данных датчиков с помощью пакета SDK для шлюза Azure IoT](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)

