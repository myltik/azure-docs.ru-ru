---
title: "Подключение шлюза к Azure IoT Suite с помощью Intel NUC | Документация Майкрософт"
description: "Использование набора для создания коммерческого шлюза Интернета вещей (Майкрософт) и предварительно настроенного решения для удаленного мониторинга. Подключайтесь к решению для удаленного мониторинга, отправляйте смоделированные данные телеметрии в облако, а также реагируйте на методы, вызываемые на панели мониторинга этого решения, с помощью шлюза Edge Интернета вещей Azure."
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
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 9ed57d3c23e2adbd42c054f33c8ed46e3d6c9792
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>Подключение шлюза Edge Интернета вещей Azure к предварительно настроенному решению для удаленного мониторинга и отправка смоделированной телеметрии

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

В этом учебнике показано, как использовать Azure IoT Edge для моделирования данных температуры и влажности, отправляемых в заранее настроенное удаленное решение для мониторинга. В руководстве используются следующие ресурсы:

- Azure IoT Edge для реализации примера шлюза.
- Предварительно настроенное решение для удаленного мониторинга IoT Suite в качестве облачного сервера.

## <a name="overview"></a>Обзор

В этом руководстве выполняются следующие шаги:

- Развертывание экземпляра предварительно настроенного решения удаленного мониторинга в подписке Azure. На этом шаге автоматически разворачивается и настраивается несколько служб Azure.
- Настройка устройства шлюза Intel NUC для взаимодействия с компьютером и решением для удаленного мониторинга.
- Настройка шлюза Edge Интернета вещей для отправки смоделированных данных телеметрии, которые можно просмотреть на панели мониторинга решений.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> Решение для удаленного мониторинга подготавливает набор служб Azure в подписке Azure. Развертывание отражает реальную корпоративную архитектуру. Чтобы избежать ненужных расходов на использование ресурсов Azure, удалите экземпляр предварительно настроенного решения на сайте azureiotsuite.com после завершения работы с ним. Если предварительно настроенное решение понадобится снова, его можно легко восстановить. Дополнительные сведения о сокращении затрат во время выполнения решения для удаленного мониторинга см. в статье [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Настройка предварительно настроенных решений Azure IoT Suite для демонстрационных целей).

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

Повторите предыдущие шаги для добавления второго устройства с использованием идентификатора устройства, такого как **device02**. Пример отправляет данные из двух виртуальных устройств в шлюзе решению для удаленного мониторинга.

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

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
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

Сценарий сборки помещает пользовательский модуль Edge Интернета вещей libsimulator.so в папку со сборкой.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Настройка шлюза Edge Интернета вещей и его выполнение

Теперь вы можете настраивать шлюз Edge Интернета вещей для отправки смоделированных данных телеметрии решению для удаленного мониторинга. Дополнительные сведения о настройке шлюза и модулей Edge Интернета вещей см. в статье [Приступая к работе с архитектурой Edge Интернета вещей в Linux][lnk-gateway-concepts].

> [!TIP]
> В этом руководстве используется стандартный текстовый редактор `vi` для Intel NUC. Если вы не использовали `vi` ранее, вам необходимо завершить вводное руководство, например [Unix — The vi Editor Tutorial][lnk-vi-tutorial] (Руководство по редактору vi в UNIX), для ознакомления с редактором. Кроме того, с помощью команды `smart install nano -y` можно установить более удобный редактор [nano](https://www.nano-editor.org/).

Откройте пример файла конфигурации в редакторе **vi**, используя следующую команду:

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
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
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Замените заполнители **deviceID** и **deviceKey** идентификаторами и ключами двух устройств, созданными ранее для решения удаленного мониторинга.

Сохраните изменения.

Теперь можно запускать шлюз Edge Интернета вещей с помощью следующих команд:

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

Шлюз запускается на Intel NUC и отправляет смоделированные данные телеметрии решению для удаленного мониторинга:

![Создание смоделированной телеметрии в шлюзе Edge Интернета вещей][img-simulated telemetry]

Вы можете в любое время нажать комбинацию клавиш **CTRL+C**, чтобы выйти из программы.

## <a name="view-the-telemetry"></a>Просмотр телеметрии

Теперь шлюз Edge Интернета вещей может отправлять смоделированные данные телеметрии в удаленное решение мониторинга. Эти данные можно просмотреть на панели мониторинга решения.

- Перейдите к панели мониторинга решения.
- В раскрывающемся списке **Device to View** (Устройство для просмотра) выберите одно из двух устройств, настроенных в шлюзе.
- Данные телеметрии из устройств шлюза отобразятся на панели мониторинга.

![Отображение данных телеметрии из устройств имитации шлюза][img-telemetry-display]

> [!WARNING]
> Если не завершить выполнение решения удаленного мониторинга в учетной записи Azure, вам будет выставлен счет. Дополнительные сведения о сокращении затрат во время выполнения решения для удаленного мониторинга см. в статье [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Настройка предварительно настроенных решений Azure IoT Suite для демонстрационных целей). Прекратив использовать предварительно настроенное решение, удалите его из учетной записи Azure.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры и документацию по Azure IoT можно найти в [Центре разработчиков Azure IoT](https://azure.microsoft.com/develop/iot/).

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started