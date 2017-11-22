---
title: "Имитация Azure IoT Edge в Windows | Документация Майкрософт"
description: "Установка среды выполнения Azure IoT Edge на имитированном устройстве в ОС Windows и развертывание первого модуля"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 08c501b9132bb21f47f099725d1fad5556befb4c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Развертывание Azure IoT Edge на имитированном устройстве в ОС Windows (предварительная версия)

Azure IoT Edge переносит мощь облака на ваши устройства Интернета вещей (IoT). В этом руководстве мы рассмотрим процесс создания имитированного устройства IoT Edge, которое генерирует данные датчика. Вы узнаете, как выполнять следующие задачи:

![Архитектура руководства][2]

Имитированное устройство, которое мы создадим в этом руководстве, представляет собой монитор на ветряной турбине, который генерирует данные о температуре, влажности и давлении. Эти данные представляют интерес, так как турбины работают с различной эффективностью в зависимости от погодных условий. В других руководства по Azure IoT Edge используются наработки из этой статьи: развернутые модули, которые анализируют данные в целях бизнес-аналитики. 

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве предполагается, что вы используете компьютер или виртуальную машину под управлением Windows для имитации устройства Интернета вещей. 

>[!TIP]
>Если вы работаете на виртуальной машине Windows, включите [вложенную виртуализацию][lnk-nested] и выделите не менее 2 ГБ памяти. 

1. Убедитесь, что используется поддерживаемая версия Windows:
   * Windows 10 
   * Windows Server
2. Установите среду [Docker для Windows][lnk-docker] и убедитесь, что она выполняется.
3. Установите [Python 2.7 для Windows][lnk-python] и убедитесь, что команда pip работает.
4. Выполните следующую команду, чтобы скачать скрипт управления IoT Edge.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> В Azure IoT Edge могут работать контейнеры Windows или Linux. Чтобы использовать контейнеры Windows, требуется любая из следующих версий ОС:
>    * Windows 10 Fall Creators Update;
>    * Windows Server 1709 (сборка 16299);
>    * Windows IoT Базовая (сборка 16299) на устройстве x64.
>
> Если вы используете Windows IoT Базовая, выполните инструкции из статьи [Install the IoT Edge runtime on Windows IoT Core][lnk-install-iotcore] (Установка среды выполнения IoT Edge на Windows IoT Базовая). Для других систем просто [настройте в Docker работу с контейнерами Windows][lnk-docker-containers]. При желании вы можете проверить наличие необходимых компонентов с помощью следующей команды PowerShell:
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

В первую очередь создайте Центр Интернета вещей.
![Создание Центра Интернета вещей][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Регистрация устройства IoT Edge

Зарегистрируйте устройство IoT Edge в только что созданном Центре Интернета вещей.
![Регистрация устройства][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Настройка среды выполнения IoT Edge

Установите и запустите среду выполнения Azure IoT Edge на устройстве. 
![Регистрация устройства][5]

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из двух модулей. Первый из них, агент IoT Edge, выполняет развертывание и мониторинг модулей на устройстве IoT Edge. Второй, центр IoT Edge, управляет взаимодействием между модулями на устройстве IoT Edge и между устройством и Центром Интернета вещей. 


Для установки и запуска среды выполнения IoT Edge выполните следующие действия:

1. Для среды выполнения укажите строку подключения устройства IoT Edge, о которой шла речь в предыдущем разделе.

   ```
   iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
   ```

1. Запустите среду выполнения.

   ```
   iotedgectl start
   ```

1. В Docker убедитесь, что агент IoT Edge работает как модуль.

   ```
   docker ps
   ```

## <a name="deploy-a-module"></a>Развертывание модуля

Управляя устройством Azure IoT Edge из облака, разверните модуль, который будет передавать данные телеметрии в Центр Интернета вещей.
![Регистрация устройства][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом руководстве мы создали новое устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure мы отправили на устройство и запустили модуль IoT Edge, не изменяя на устройстве никаких настроек. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

Убедитесь, что сообщения отправляются из модуля tempSensor.

```cmd/sh
sudo docker logs -f tempSensor
```

Вы также можете просмотреть данные телеметрии, которые передает устройство, используя [обозреватель Центра Интернета вещей][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве мы создали новое устройство IoT Edge и с помощью облачного интерфейса Azure IoT Edge развернули код на устройстве. В итоге мы получили имитированное устройство, генерирующее необработанные данные о своей среде. 

Результаты, полученные в ходе работы с этим руководством, будут использоваться во всех остальных руководствах по IoT Edge. Вы можете перейти к любому из оставшихся руководств, чтобы узнать, как Azure IoT Edge может помочь превратить получаемые данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Разработка кода C# и его развертывание в виде модуля](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md