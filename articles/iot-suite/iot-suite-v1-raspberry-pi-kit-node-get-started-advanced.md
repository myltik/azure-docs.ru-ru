---
title: Подключение устройства Raspberry Pi к Azure IoT Suite с помощью Node.js для поддержки обновлений встроенного ПО | Документация Майкрософт
description: Используйте начальный набор Microsoft Azure IoT для Raspberry Pi 3, а также Azure IoT Suite. Используйте Node.js для подключения устройства Raspberry Pi к решению для удаленного мониторинга, отправляйте данные телеметрии с датчиков в облако, а также выполняйте удаленное обновление встроенного ПО.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 31bbeff8049c6005671b991f965fae7316e3adf6
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
ms.locfileid: "24010877"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-nodejs"></a>Подключение устройства Raspberry Pi 3 к решению для удаленного мониторинга и включение удаленных обновлений встроенного ПО с помощью Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

В этом руководстве показано, как можно получить следующие возможности, используя начальный набор Интернета вещей Microsoft Azure для Raspberry Pi 3:

* Разработка средства чтения данных о температуре и влажности, которое может взаимодействовать с облаком.
* Включение и выполнение удаленного обновления встроенного ПО для обновления клиентского приложения на устройстве Raspberry Pi.

В руководстве используются следующие ресурсы:

- Операционная система Raspbian, язык программирования Node.js и пакет SDK Microsoft Azure IoT для Node.js для реализации примера устройства.
- Предварительно настроенное решение для удаленного мониторинга IoT Suite в качестве облачного сервера.

## <a name="overview"></a>Обзор

В этом руководстве выполняются следующие шаги:

- Развертывание экземпляра предварительно настроенного решения удаленного мониторинга в подписке Azure. На этом шаге автоматически разворачивается и настраивается несколько служб Azure.
- Настройка устройства и датчиков для взаимодействия с компьютером и решением для удаленного мониторинга.
- Обновление кода примера устройства для подключения к решению для удаленного мониторинга и отправка данных телеметрии, которые можно просмотреть на панели мониторинга решения.
- Обновление клиентского приложения с помощью примера кода устройства.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Решение для удаленного мониторинга подготавливает набор служб Azure в подписке Azure. Развертывание отражает реальную корпоративную архитектуру. Чтобы избежать ненужных расходов на использование ресурсов Azure, удалите экземпляр предварительно настроенного решения на сайте azureiotsuite.com после завершения работы с ним. Если предварительно настроенное решение понадобится снова, его можно легко восстановить. Дополнительные сведения о сокращении затрат во время выполнения решения для удаленного мониторинга см. в статье [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Настройка предварительно настроенных решений Azure IoT Suite для демонстрационных целей).

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Скачивание и настройка примера

Теперь можно скачать и настроить клиентское приложение для удаленного мониторинга на устройстве Raspberry Pi.

### <a name="install-nodejs"></a>Установка Node.js

Установите Node.js на устройстве Raspberry Pi, если вы это еще не сделали. Для пакета SDK IoT для Node.js требуется версия Node.js 0.11.5 или более поздняя. Ниже описывается установка Node.js версии 6.10.2 на устройстве Raspberry Pi.

1. Чтобы обновить устройство Raspberry Pi, используйте следующую команду:

    ```sh
    sudo apt-get update
    ```

1. Чтобы скачать двоичные файлы Node.js на устройство Raspberry Pi, используйте следующую команду:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Чтобы установить эти двоичные файлы, используйте следующую команду:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Чтобы убедиться в успешной установке Node.js версии 6.10.2, используйте следующую команду:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Клонирование репозиториев

Клонируйте необходимые репозитории (если вы еще не сделали этого), выполнив следующие команды на устройстве Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Обновление строки подключения устройства

Откройте пример файла конфигурации в редакторе **nano**, используя следующую команду:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Замените значения заполнителей идентификатором устройства и сведениями о Центре Интернета вещей, созданном и сохраненном в начале этого руководства.

По завершении содержимое файла deviceinfo должно выглядеть следующим образом:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Сохраните изменения (клавиши **CTRL+O**, **ВВОД**) и закройте редактор (клавиши **CTRL+X**).

## <a name="run-the-sample"></a>Запуск примера

Чтобы установить пакеты необходимых компонентов для примера, выполните следующие команды:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advance/1.0
npm install
```

Теперь вы можете запустить пример программы на устройстве Raspberry Pi. Введите команду:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/1.0/remote_monitoring.js
```

Следующий пример с выходными данными отобразится в командной строке на устройстве Raspberry Pi:

![Выходные данные приложения Raspberry Pi][img-raspberry-output]

Вы можете в любое время нажать комбинацию клавиш **CTRL+C**, чтобы выйти из программы.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. На панели мониторинга решения щелкните **Устройства**, чтобы перейти на страницу **устройств**. Выберите Raspberry Pi в **списке устройств**. Затем выберите **Методы**:

    ![Список устройств на панели мониторинга][img-list-devices]

1. На странице **Вызвать метод** в раскрывающемся списке **методов** выберите **InitiateFirmwareUpdate**.

1. В поле **FWPackageURI** введите **https://raw.githubusercontent.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit/master/advanced/2.0/raspberry.js**. Этот файл содержит реализацию встроенного ПО версии 2.0.

1. Выберите **InvokeMethod**. Приложение на устройстве Raspberry Pi отправляет подтверждение обратно на панель мониторинга решения. Затем запускается процесс обновления встроенного ПО путем скачивания новой версии встроенного ПО:

    ![Просмотр журнала методов][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Просмотр процесса обновления встроенного ПО

Вы можете наблюдать за выполнением процесса обновления встроенного ПО на устройстве и просматривая сообщенные свойства на панели мониторинга решения:

1. Ход выполнения процесса обновления можно просмотреть на устройстве Raspberry Pi:

    ![Отображение хода выполнения обновления][img-update-progress]

    > [!NOTE]
    > После обновления приложение для удаленного мониторинга автоматически перезапускается. Используйте команду `ps -ef`, чтобы проверить, выполняется ли процесс. Если вы хотите завершить процесс, используйте команду `kill` с идентификатором процесса.

1. Данные о состоянии обновления встроенного ПО, сообщаемые устройством, можно просмотреть на портале решения. На следующем снимке экрана показаны сведения о состоянии и продолжительности каждого этапа процесса обновления, а также сведения о новой версии встроенного ПО:

    ![Отображение состояния задания][img-job-status]

    При переходе к панели мониторинга можно проверить, отправляет ли устройство данные телеметрии после обновления встроенного ПО.

> [!WARNING]
> Если не завершить выполнение решения удаленного мониторинга в учетной записи Azure, вам будет выставлен счет. Дополнительные сведения о сокращении затрат во время выполнения решения для удаленного мониторинга см. в статье [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Настройка предварительно настроенных решений Azure IoT Suite для демонстрационных целей). Прекратив использовать предварительно настроенное решение, удалите его из учетной записи Azure.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры и документацию по Azure IoT можно найти в [Центре разработчиков Azure IoT](https://azure.microsoft.com/develop/iot/).


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
