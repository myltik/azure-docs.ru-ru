---
title: "Подключение шлюза к Azure IoT Suite с помощью Intel NUC | Документация Майкрософт"
description: "Использование набора для создания коммерческого шлюза Интернета вещей (Майкрософт) и предварительно настроенного решения для удаленного мониторинга. Подключайтесь к решению для удаленного мониторинга, отправляйте смоделированные данные телеметрии в облако, а также реагируйте на методы, вызываемые на панели мониторинга этого решения, с помощью устройства SensorTag и шлюза Edge Интернета вещей Azure."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/05/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 03c8e18636e1182b301e1bcf418c727265d8b478
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017


---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>Подключение шлюза Edge Интернета вещей Azure к предварительно настроенному решению для удаленного мониторинга и отправка данных телеметрии из SensorTag

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

В этом руководстве показано, как отправлять данные температуры и влажности из устройства SensorTag предварительно настроенному решению для удаленного мониторинга с помощью шлюза Edge Интернета вещей Azure. SensorTag подключается к шлюзу Intel NUC с помощью Bluetooth. В руководстве используются следующие ресурсы:

- Edge Интернета вещей Azure для реализации примера шлюза.
- Предварительно настроенное решение для удаленного мониторинга IoT Suite в качестве облачного сервера.

## <a name="overview"></a>Обзор

В этом руководстве выполняются следующие шаги:

- Развертывание экземпляра предварительно настроенного решения удаленного мониторинга в подписке Azure. На этом шаге автоматически разворачивается и настраивается несколько служб Azure.
- Настройка устройства шлюза Intel NUC для взаимодействия с компьютером и решением для удаленного мониторинга.
- Настройка шлюза Intel NUC для получения данных телеметрии с устройства SensorTag и их отправки на панель удаленного мониторинга.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[Texas Instruments BLE SensorTag][lnk-sensortag]. В этом руководстве данные телеметрии извлекаются с устройства SensorTag через Bluetooth.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> Решение для удаленного мониторинга подготавливает набор служб Azure в подписке Azure. Развертывание отражает реальную корпоративную архитектуру. Чтобы избежать ненужных расходов на использование ресурсов Azure, удалите экземпляр предварительно настроенного решения на сайте azureiotsuite.com после завершения работы с ним. Если предварительно настроенное решение понадобится снова, его можно легко восстановить. Дополнительные сведения о сокращении затрат во время выполнения решения для удаленного мониторинга см. в статье [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Настройка предварительно настроенных решений Azure IoT Suite для демонстрационных целей).

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Настройка подключения Bluetooth

Настройте Bluetooth на Intel NUC, чтобы включить устройство SensorTag для подключения и отправки данных телеметрии.

### <a name="find-the-mac-address-of-the-sensortag"></a>Поиск MAC-адреса устройства SensorTag

1. В оболочке Intel NUC выполните следующую команду, чтобы разблокировать службу Bluetooth:

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. Выполните следующие команды, чтобы запустить службу Bluetooth на Intel NUC и выполнить вход в оболочку Bluetooth:

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Выполните следующую команду, чтобы включить контроллер Bluetooth:

    ```bash
    power on
    ```

    При включении контроллера появится сообщение **Changing power on succeeded** (Питание успешно включено).

1. Выполните следующую команду для поиска находящихся поблизости устройств Bluetooth:

    ```bash
    scan on
    ```

1. Нажмите кнопку питания на устройстве SensorTag, чтобы его можно было обнаружить. Начнет мигать зеленый индикатор.

1. Когда в оболочке появится сообщение о том, что контроллер обнаружил SensorTag, запишите MAC-адрес устройства. MAC-адрес выглядит примерно так: **A0:E6:F8:B5:F6:00**. Он понадобится вам позже в этом руководстве при настройке шлюза.

1. Выполните следующую команду, чтобы отключить сканирование Bluetooth:

    ```bash
    scan off
    ```

1. Выполните следующую команду, чтобы убедиться, что вы можете подключиться к устройству SensorTag:

    ```bash
    connect <SensorTag MAC address>
    ```

    Если подключение выполнено успешно, в оболочке отобразится сообщение **Подключение успешно выполнено** и будут выведены сведения об устройстве SensorTag. Если вы не можете подключиться, убедитесь, что устройство SensorTag включено.

1. Теперь вы можете отключиться от SensorTag и выйти из оболочки Bluetooth, выполнив следующую команду:

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Создание пользовательского модуля Edge Интернета вещей

Теперь вы можете создать пользовательский модуль Edge Интернета вещей, который включает шлюз для отправки сообщений решению для удаленного мониторинга. Дополнительные сведения о настройке шлюза и модулей Edge Интернета вещей см. в статье [Приступая к работе с архитектурой Edge Интернета вещей в Linux][lnk-gateway-concepts].

Скачайте исходный код для модулей Edge Интернета вещей с GitHub с помощью следующих команд:

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Создайте пользовательский модуль Edge Интернета вещей с помощью следующих команд:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

Сценарий сборки помещает пользовательский модуль Edge Интернета вещей libsensor2remotemonitoring.so в папку со сборкой.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Настройка шлюза Edge Интернета вещей и его выполнение

Теперь вы можете настроить шлюз Edge Интернета вещей для отправки данных телеметрии с устройства SensorTag решению для удаленного мониторинга. Дополнительные сведения о настройке шлюза и модулей Edge Интернета вещей см. в статье [Приступая к работе с архитектурой Edge Интернета вещей в Linux][lnk-gateway-concepts].

> [!TIP]
> В этом руководстве используется стандартный текстовый редактор `vi` для Intel NUC. Если вы не использовали `vi` ранее, вам необходимо завершить вводное руководство, например [Unix — The vi Editor Tutorial][lnk-vi-tutorial] (Руководство по редактору vi в UNIX), для ознакомления с редактором. Кроме того, с помощью команды `smart install nano -y` можно установить более удобный редактор [nano](https://www.nano-editor.org/).

Откройте пример файла конфигурации в редакторе **vi**, используя следующую команду:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
```

Найдите следующие строки в конфигурации для модуля Центра Интернета вещей:

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

Замените значения заполнителей сведениями о Центре Интернета вещей, созданными и сохраненными в начале этого руководства. Обычно для IoTHubName используется значение **yourrmsolution37e08**, а для IoTSuffix — **azure-devices.net**.

Найдите следующие строки в конфигурации для модуля сопоставления:

```json
args": [
  {
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>>"
  }
]
```

Замените заполнитель **macAddress** MAC-адресом устройства SensorTag, записанным ранее. Замените заполнители **deviceID** и **deviceKey** идентификаторами и ключами двух устройств, созданными ранее для решения удаленного мониторинга.

Найдите следующие строки в конфигурации для модуля SensorTag:

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

Замените заполнитель **device\_mac\_address** MAC-адресом устройства SensorTag, записанным ранее.

Сохраните изменения.

Теперь можно запускать шлюз с помощью следующих команд:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

Шлюз Edge Интернета вещей запускается на Intel NUC и отправляет данные телеметрии из SensorTag решению для удаленного мониторинга:

![Отправка данных телеметрии с SensorTag шлюзом Edge Интернета вещей][img-telemetry]

Вы можете в любое время нажать комбинацию клавиш **CTRL+C**, чтобы выйти из программы.

## <a name="view-the-telemetry"></a>Просмотр телеметрии

Теперь шлюз отправляет данные телеметрии с устройства SensorTag решению для удаленного мониторинга. Эти данные можно просмотреть на панели мониторинга решения. Вы также можете отправлять команды на устройство SensorTag через шлюз с панели мониторинга решения.

- Перейдите к панели мониторинга решения.
- В раскрывающемся списке **Device to View** (Устройство для просмотра) выберите настроенное в шлюзе устройство, которое представляет SensorTag.
- Данные телеметрии с устройства SensorTag отобразятся на панели мониторинга.

![Отображение телеметрии с устройства SensorTag][img-telemetry-display]

> [!WARNING]
> Если не завершить выполнение решения удаленного мониторинга в учетной записи Azure, вам будет выставлен счет. Дополнительные сведения о сокращении затрат во время выполнения решения для удаленного мониторинга см. в статье [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Настройка предварительно настроенных решений Azure IoT Suite для демонстрационных целей). Прекратив использовать предварительно настроенное решение, удалите его из учетной записи Azure.


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры и документацию по Azure IoT можно найти в [Центре разработчиков Azure IoT](https://azure.microsoft.com/develop/iot/).

[img-telemetry]: ./media/iot-suite-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started
