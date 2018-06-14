---
title: Подключение устройства Raspberry Pi с физическими датчиками к Azure IoT Suite с помощью C | Документация Майкрософт
description: Используйте начальный набор Microsoft Azure IoT для Raspberry Pi 3, а также Azure IoT Suite. С помощью C вы можете подключать устройства Raspberry Pi к решению для удаленного мониторинга, отправлять данные телеметрии с датчиков в облако, а также реагировать на методы, вызываемые на панели мониторинга этого решения.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 635eb9d4e85eaf43dc83f2bd5a0d0f7c32620d98
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
ms.locfileid: "24010727"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-c"></a>Подключение устройства Raspberry Pi 3 к решению для удаленного мониторинга и отправка данных телеметрии с датчика с помощью C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

В этом руководстве показано, как использовать начальный набор Microsoft Azure IoT для Raspberry Pi 3 при разработке средства для считывания температуры и влажности, которое может обмениваться данными с облаком. В руководстве используются следующие ресурсы:

- ОС Raspbian, язык программирования C и пакет SDK для Microsoft Azure IoT для C для реализации примера устройства.
- Предварительно настроенное решение для удаленного мониторинга IoT Suite в качестве облачного сервера.

## <a name="overview"></a>Обзор

В этом руководстве выполняются следующие шаги:

- Развертывание экземпляра предварительно настроенного решения удаленного мониторинга в подписке Azure. На этом шаге автоматически разворачивается и настраивается несколько служб Azure.
- Настройка устройства и датчиков для взаимодействия с компьютером и решением для удаленного мониторинга.
- Обновление кода примера устройства для подключения к решению для удаленного мониторинга и отправка данных телеметрии, которые можно просмотреть на панели мониторинга решения.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Решение для удаленного мониторинга подготавливает набор служб Azure в подписке Azure. Развертывание отражает реальную корпоративную архитектуру. Чтобы избежать ненужных расходов на использование ресурсов Azure, удалите экземпляр предварительно настроенного решения на сайте azureiotsuite.com после завершения работы с ним. Если предварительно настроенное решение понадобится снова, его можно легко восстановить. Дополнительные сведения о сокращении затрат во время выполнения решения для удаленного мониторинга см. в статье [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Настройка предварительно настроенных решений Azure IoT Suite для демонстрационных целей).

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Скачивание и настройка примера

Теперь можно скачать и настроить клиентское приложение для удаленного мониторинга на устройстве Raspberry Pi.

### <a name="clone-the-repositories"></a>Клонирование репозиториев

Клонируйте необходимые репозитории (если вы еще не сделали это), выполнив следующие команды в терминале на устройстве Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
git clone --recursive https://github.com/WiringPi/WiringPi.git
```

### <a name="update-the-device-connection-string"></a>Обновление строки подключения устройства

Откройте исходный файл примера в редакторе **nano**, используя следующую команду:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/remote_monitoring/remote_monitoring.c
```

Найдите следующие строки:

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

Замените значения заполнителей сведениями об устройстве и Центре Интернета вещей, созданными и сохраненными в начале этого руководства. Сохраните изменения (клавиши **CTRL+O**, **ВВОД**) и закройте редактор (клавиши **CTRL+X**).

## <a name="build-the-sample"></a>Сборка примера

Установите пакеты необходимых компонентов пакет SDK для устройств Microsoft Azure IoT для C, выполнив следующие команды в окне терминала на устройстве Raspberry Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Теперь вы можете выполнить сборку обновленного примера решения на устройстве Raspberry Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
```

Теперь вы можете запустить пример программы на устройстве Raspberry Pi. Введите команду:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

Следующий пример с выходными данными отобразится в командной строке на устройстве Raspberry Pi:

![Выходные данные приложения Raspberry Pi][img-raspberry-output]

Вы можете в любое время нажать комбинацию клавиш **CTRL+C**, чтобы выйти из программы.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры и документацию по Azure IoT можно найти в [Центре разработчиков Azure IoT](https://azure.microsoft.com/develop/iot/).

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-basic/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
