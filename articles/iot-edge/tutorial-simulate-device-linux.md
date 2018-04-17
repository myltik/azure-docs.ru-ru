---
title: Имитация Azure IoT Edge в Linux | Документация Майкрософт
description: Установка среды выполнения Azure IoT Edge на имитированном устройстве в Linux и развертывание вашего первого модуля
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 65a3f6d71c0c0d92f703a5d48760dd348c726ba4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Развертывание Azure IoT Edge на имитированном устройстве в Linux или MacOS (предварительная версия)

Azure IoT Edge позволяет выполнять анализ и обработку данных на устройствах вместо того, чтобы принудительно отправлять все данные в облако. В руководствах по IoT Edge демонстрируется развертывание различных типов модулей, созданных на основе служб Azure или пользовательского кода. Однако сначала нам потребуется устройство для тестирования. 

Из этого руководства вы узнали, как выполнять такие задачи:

1. Создание центра Интернета вещей
2. Регистрация устройства IoT Edge
3. Запуск среды выполнения IoT Edge
4. Развертывание модуля

![Архитектура, используемая в руководстве][2]

Имитированное устройство, которое вы создадите в рамках этого руководства, представляет собой монитор, который генерирует данные температуры, влажности и давления. В других руководства по Azure IoT Edge используются наработки из этой статьи: развернутые модули, которые анализируют данные в целях бизнес-аналитики. 

## <a name="prerequisites"></a>предварительным требованиям

При работе с этим руководством ваш компьютер или виртуальная машина используется как устройство Интернета вещей. Чтобы преобразовать компьютер в устройство IoT Edge, требуются следующие службы:

* PIP на Python для установки среды выполнения IoT Edge.
   * Для Linux — `sudo apt-get install python-pip`.
     * _Обратите внимание, что для некоторых дистрибутивов (например, Raspbian), нужно также обновить определенные пакеты PIP и установить дополнительные зависимости:_
     ```
     sudo pip install --upgrade setuptools pip
     sudo apt-get install python2.7-dev libffi-dev libssl-dev
     ```
   * Для MacOS — `sudo easy_install pip`.
* Docker для запуска модулей IoT Edge.
   * [Установите среду Docker для Linux][lnk-docker-ubuntu] и убедитесь, что она работает. 
   * [Установите среду Docker для Mac][lnk-docker-mac] и убедитесь, что она работает. 

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

В первую очередь создайте Центр Интернета вещей.
![Создание Центра Интернета вещей][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Регистрация устройства IoT Edge

Зарегистрируйте устройство IoT Edge в только что созданном Центре Интернета вещей.
![Регистрация устройства][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Установка и запуск среды выполнения IoT Edge

Установите и запустите среду выполнения Azure IoT Edge на устройстве. 
![Регистрация устройства][5]

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из двух модулей. **Агент IoT Edge** упрощает развертывание и мониторинг модулей на устройстве IoT Edge. **Центр IoT Edge** управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей. При настройке среды выполнения на новом устройстве сначала будет запущен только агент IoT Edge. Центр IoT Edge будет запущен позже, после развертывания модуля. 

На компьютере, на котором вы планируете запустить устройство IoT Edge, скачайте скрипт управления IoT Edge.
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Для среды выполнения укажите строку подключения устройства IoT Edge, о которой шла речь в предыдущем разделе.
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Запустите среду выполнения.
```cmd
sudo iotedgectl start
```

В Docker убедитесь, что агент IoT Edge работает как модуль.
```cmd
sudo docker ps
```

![Просмотр информации об агенте IoT Edge в Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Развертывание модуля

Управляя устройством Azure IoT Edge из облака, разверните модуль, который будет передавать данные телеметрии в Центр Интернета вещей.
![Регистрация устройства][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом руководстве вы создали устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure мы отправили на устройство и запустили модуль IoT Edge, не изменяя на устройстве никаких настроек. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

Еще раз откройте командную строку на компьютере, на котором работает имитированное устройство. Убедитесь, что развернутый из облака модуль работает на вашем устройстве IoT Edge.

```cmd
sudo docker ps
```

![Просмотр трех модулей на устройстве](./media/tutorial-simulate-device-linux/docker-ps2.png)

Убедитесь, что сообщения отправляются из модуля tempSensor в облако.

```cmd
sudo docker logs -f tempSensor
```

![Просмотр получаемых с модуля данных](./media/tutorial-simulate-device-linux/docker-logs.png)

Вы также можете просмотреть данные телеметрии, которые передает устройство, используя [обозреватель Центра Интернета вещей][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве мы создали новое устройство IoT Edge и с помощью облачного интерфейса Azure IoT Edge развернули код на устройстве. В итоге мы получили имитированное устройство, генерирующее необработанные данные о своей среде. 

Результаты, полученные в ходе работы с этим руководством, будут использоваться во всех остальных руководствах по IoT Edge. Вы можете перейти к любому из оставшихся руководств, чтобы узнать, как Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Разработка кода C# и его развертывание в виде модуля](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
