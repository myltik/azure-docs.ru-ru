---
title: Краткое руководство по использованию Azure IoT Edge в ОС Windows | Документация Майкрософт
description: Первое знакомство с Azure IoT Edge и выполнение анализа на имитированном пограничном устройстве
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/03/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 888f74d215956f4ad38605ca247f681da700a787
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166222"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Краткое руководство по развертыванию первого простого модуля IoT Edge на устройстве с Windows при помощи портала Azure (предварительная версия)

В этом кратком руководстве вы примените облачный интерфейс Azure IoT Edge для удаленного развертывания готового кода на устройстве IoT Edge. Чтобы выполнить эту задачу, сначала создайте виртуальное устройство IoT Edge на устройстве с Windows,а затем разверните на нем нужный модуль.

Если у вас еще нет подписки Azure, перед началом работы [создайте бесплатную учетную запись Azure][lnk-account].

## <a name="prerequisites"></a>предварительным требованиям

В этом руководстве предполагается, что вы используете компьютер или виртуальную машину под управлением Windows для имитации устройства Интернета вещей. Если вы работаете на виртуальной машине с Windows, включите [вложенную виртуализацию][lnk-nested] и выделите не менее 2 ГБ памяти. 

1. Убедитесь, что используется поддерживаемая версия Windows:
   * Windows 10 
   * Windows Server
2. Установите среду [Docker для Windows][lnk-docker] и убедитесь, что она выполняется.
3. Установите [Python для Windows][lnk-python] и убедитесь, что команда pip работает. Это краткое руководство протестировано с использованием версий Python >=2.7.9 и >=3.5.4.  
4. Выполните приведенную ниже команду, чтобы скачать файл управления IoT Edge.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> В Azure IoT Edge могут работать контейнеры Windows или Linux. Чтобы использовать контейнеры Windows, требуется любая из следующих версий ОС:
>    * Windows 10 Fall Creators Update;
>    * Windows Server 1709 (сборка 16299);
>    * Windows IoT Базовая (сборка 16299) на устройстве x64.
>
> Если вы используете Windows IoT Базовая, выполните инструкции из статьи [Install the IoT Edge runtime on Windows IoT Core][lnk-install-iotcore] (Установка среды выполнения IoT Edge на Windows IoT Базовая). Для других систем просто [настройте в Docker работу с контейнерами Windows][lnk-docker-containers]. При желании вы можете проверить наличие необходимых компонентов с помощью следующей команды PowerShell:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>Создание Центра Интернета вещей с помощью интерфейса командной строки Azure

Создайте в своей подписке Azure Центр Интернета вещей. Для целей этого руководства можно использовать бесплатный уровень. Если вы уже использовали бесплатный Центр Интернета вещей и он у вас сохранился, можете пропустить этот раздел и сразу переходите к [регистрации устройства IoT Edge][anchor-register]. В подписке может быть только один бесплатный Центр Интернета вещей. 

1. Войдите на [портал Azure][lnk-portal]. 
1. Нажмите кнопку **Cloud Shell**. 

   ![Кнопка Cloud Shell][1]

1. Создайте группу ресурсов. В следующем примере кода создается группа ресурсов с именем **IoTEdge** в регионе **Западная часть США**:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Создайте в новой группе ресурсов Центр Интернета вещей. При помощи следующего кода создается бесплатный центр **F1** с именем **MyIotHub** в группе ресурсов **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Регистрация устройства IoT Edge

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Настройка среды выполнения IoT Edge

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из двух модулей. Первый из них, агент IoT Edge, упрощает развертывание и мониторинг модулей на устройстве IoT Edge. Второй, центр IoT Edge, управляет взаимодействием между модулями на устройстве IoT Edge, а также между устройством и Центром Интернета вещей. 

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

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом руководстве мы создали новое устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure мы отправили на устройство и запустили модуль IoT Edge, не изменяя на устройстве никаких настроек. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

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
## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить имитированное устройство, которое вы создали, а также контейнеры Docker, запущенные для каждого модуля, используйте следующую команду: 

```cmd
iotedgectl uninstall
```

Если созданный Центр Интернета вещей вам больше не нужен, используйте команду [az iot hub delete][lnk-delete], чтобы удалить этот ресурс и связанные с ним устройства.

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как развернуть модуль IoT Edge на устройстве IoT Edge. Теперь потренируйтесь развертывать службы Azure разных типов как модули, чтобы анализировать данные пограничного устройства: 

* [Разверните функцию Azure в качестве модуля](tutorial-deploy-function.md).
* [Разверните Azure Stream Analytics в качестве модуля](tutorial-deploy-stream-analytics.md).
* [Разверните собственный код в качестве модуля](tutorial-csharp-module.md).


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
