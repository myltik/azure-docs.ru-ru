---
title: "Имитация Azure IoT Edge в Linux | Документация Майкрософт"
description: "Установка среды выполнения Azure IoT Edge на имитированном устройстве в Linux и развертывание вашего первого модуля"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 041919fd729880d429e08d8942f8d1ee087ccf61
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux---preview"></a>Развертывание Azure IoT Edge на имитированном устройстве в Linux (предварительная версия)

Azure IoT Edge позволяет выполнять анализ и обработку данных на устройствах вместо того, чтобы принудительно отправлять все данные в облако. В руководствах по IoT Edge демонстрируется развертывание различных типов модулей, созданных на основе служб Azure или пользовательского кода. Однако сначала нам потребуется устройство для тестирования. 

В этом руководстве показано создание имитированного устройства IoT Edge и развертывание модуля, который генерирует данные датчика. Вы узнаете, как выполнять следующие задачи:

![Архитектура, используемая в руководстве][2]

Имитированное устройство, которое вы создадите в рамках этого руководства, представляет собой монитор, который генерирует данные температуры, влажности и давления. Другие руководства по Azure IoT Edge основаны на результатах работы с этим руководством. В них развертываются модули, которые анализируют данные для бизнес-аналитики. 

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве предполагается, что вы используете компьютер или виртуальную машину под управлением Linux для имитации устройства Интернета вещей. Для успешного развертывания устройства IoT Edge необходимы следующие службы:

- [Установите среду Docker для Linux][lnk-docker-ubuntu] и убедитесь, что она работает. 
- Большинство дистрибутивов Linux, включая Ubuntu, уже содержат установленный пакет Python 2.7. Проверить, установлена ли система pip, можно с помощью следующей команды: `sudo apt-get install python-pip`.

## <a name="create-an-iot-hub"></a>Создание центра IoT

Начните работу с этим руководством, создав Центр интернета вещей.
![Создание Центра Интернета вещей][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Регистрация устройства IoT Edge

Зарегистрируйте устройство IoT Edge в только что созданном Центре Интернета вещей.
![Регистрация устройства][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Установка и запуск среды выполнения IoT Edge

Установите и запустите среду выполнения Azure IoT Edge на устройстве. 
![Регистрация устройства][5]

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из двух модулей. Первый из них, агент IoT Edge, выполняет развертывание и мониторинг модулей на устройстве IoT Edge. Второй, центр IoT Edge, управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей. 

Для установки и запуска среды выполнения IoT Edge выполните следующие действия.

1. Cкачайте скрипт управления IoT Edge на компьютер, где будет запускаться устройство IoT Edge.

   ```
   sudo pip install -U azure-iot-edge-runtime-ctl
   ```

1. Для среды выполнения укажите строку подключения устройства IoT Edge, о которой шла речь в предыдущем разделе.

   ```
   sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Запустите среду выполнения.

   ```
   sudo iotedgectl start
   ```

1. Проверьте Docker и убедитесь, что агент IoT Edge выполняется как модуль.

   ```
   sudo docker ps
   ```

## <a name="deploy-a-module"></a>Развертывание модуля

Управляя устройством Azure IoT Edge из облака, разверните модуль, который будет передавать данные телеметрии в Центр Интернета вещей.
![Регистрация устройства][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом кратком руководстве вы создали новое устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure вы отправили модуль IoT Edge для выполнения на устройстве, не изменяя никаких настроек на самом устройстве. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

Просмотр сообщений, отправляемых из модуля tempSensor:

```cmd/sh
docker logs -f tempSensor
```

Вы также можете просмотреть данные телеметрии, которые передает устройство, используя [обозреватель Центра Интернета вещей][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали устройство IoT Edge и с помощью облачного интерфейса Azure IoT Edge развернули код на устройстве. Теперь у вас есть имитированное устройство, генерирующее необработанные данные о среде, в которой оно работает. 

Это руководство является предварительным условием для выполнения всех остальных руководств по IoT Edge. Вы можете перейти к любому из оставшихся руководств, чтобы узнать, как Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Разработка и развертывание кода C# в виде модуля](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
