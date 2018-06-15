---
title: Имитация Azure IoT Edge в Windows | Документация Майкрософт
description: Установка среды выполнения Azure IoT Edge на имитированном устройстве в ОС Windows и развертывание первого модуля
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7ad99a49a578de4997a2d76d48da33aba6847f3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631196"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Развертывание Azure IoT Edge на имитированном устройстве в ОС Windows (предварительная версия)

Azure IoT Edge позволяет выполнять анализ и обработку данных на устройствах вместо того, чтобы принудительно отправлять все данные в облако. В руководствах по IoT Edge демонстрируется развертывание различных типов модулей, созданных на основе служб Azure или пользовательского кода. Однако сначала нам потребуется устройство для тестирования. 

Из этого руководства вы узнали, как выполнять такие задачи:

1. Создание центра Интернета вещей
2. Регистрация устройства IoT Edge
3. Запуск среды выполнения IoT Edge
4. Развертывание модуля

![Архитектура, используемая в руководстве][2]

Имитированное устройство, которое мы создадим в этом руководстве, представляет собой монитор на ветряной турбине, который генерирует данные о температуре, влажности и давлении. Эти данные представляют интерес, так как турбины работают с различной эффективностью в зависимости от погодных условий. В других руководства по Azure IoT Edge используются наработки из этой статьи: развернутые модули, которые анализируют данные в целях бизнес-аналитики. 

## <a name="prerequisites"></a>предварительным требованиям

В этом руководстве предполагается, что вы используете компьютер или виртуальную машину под управлением Windows для имитации устройства Интернета вещей. 

>[!TIP]
>Если вы работаете на виртуальной машине с Windows, включите [вложенную виртуализацию][lnk-nested] и выделите не менее 2 ГБ памяти. 

1. Убедитесь, что используется поддерживаемая версия Windows:
   * Windows 10 
   * Windows Server
2. Установите среду [Docker для Windows][lnk-docker] и убедитесь, что она выполняется.
3. Установите [Python для Windows][lnk-python] и убедитесь, что команда pip работает. Это руководство протестировано с использованием Python версий не ниже 2.7.9 и не ниже 3.5.4.  
4. Выполните приведенную ниже команду, чтобы скачать файл управления IoT Edge.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> В Azure IoT Edge могут работать контейнеры Windows или Linux. Контейнеры Windows можно использовать с такими версиями Windows:
>    * Windows 10 Fall Creators Update;
>    * Windows Server 1709 (сборка 16299);
>    * Windows IoT Базовая (сборка 16299) на устройстве x64.
>
> Если вы используете Windows IoT Базовая, выполните инструкции из статьи [Install the IoT Edge runtime on Windows IoT Core][lnk-install-iotcore] (Установка среды выполнения IoT Edge на Windows IoT Базовая). В противном случае просто [настройте в Docker использование контейнеров Windows][lnk-docker-containers]. Чтобы проверить, все ли требования этого руководства выполнены, выполните следующую команду:
>    ```powershell
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

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из двух модулей. **Агент IoT Edge** упрощает развертывание и мониторинг модулей на устройстве IoT Edge. **Центр IoT Edge** управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей. При настройке среды выполнения на новом устройстве сначала будет запущен только агент IoT Edge. Центр IoT Edge будет запущен позже, после развертывания модуля. 


Для среды выполнения укажите строку подключения устройства IoT Edge, о которой шла речь в предыдущем разделе.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Запустите среду выполнения.

```cmd
iotedgectl start
```

В Docker убедитесь, что агент IoT Edge работает как модуль.

```cmd
docker ps
```

![Просмотр информации об агенте IoT Edge в Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Развертывание модуля

Управляя устройством Azure IoT Edge из облака, разверните модуль, который будет передавать данные телеметрии в Центр Интернета вещей.
![Регистрация устройства][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом руководстве вы создали устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure мы отправили на устройство и запустили модуль IoT Edge, не изменяя на устройстве никаких настроек. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

Еще раз откройте командную строку на компьютере, на котором работает имитированное устройство. Убедитесь, что развернутый из облака модуль работает на вашем устройстве IoT Edge. 

```cmd
docker ps
```

![Просмотр трех модулей на устройстве](./media/tutorial-simulate-device-windows/docker-ps2.png)

Убедитесь, что сообщения отправляются из модуля tempSensor в облако. 

```cmd
docker logs -f tempSensor
```

![Просмотр получаемых с модуля данных](./media/tutorial-simulate-device-windows/docker-logs.png)

Вы также можете просмотреть данные телеметрии, которые передает устройство, используя [обозреватель Центра Интернета вещей][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве мы создали новое устройство IoT Edge и с помощью облачного интерфейса Azure IoT Edge развернули код на устройстве. В итоге мы получили имитированное устройство, генерирующее необработанные данные о своей среде. 

Результаты, полученные в ходе работы с этим руководством, будут использоваться во всех остальных руководствах по IoT Edge. Вы можете перейти к любому из оставшихся руководств, чтобы узнать, как Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

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