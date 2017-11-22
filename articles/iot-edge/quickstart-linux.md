---
title: "Краткое руководство по использованию Azure IoT Edge и Linux | Документация Майкрософт"
description: "Первое знакомство с Azure IoT Edge и запуск аналитики на имитированном пограничном устройстве"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb93efcf00cb7b165c497d7ef38685f80bce84c0
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-linux-device---preview"></a>Краткое руководство. Развертывание первого модуля IoT Edge на устройстве Linux с помощью портала Azure (предварительная версия)

Azure IoT Edge переносит мощь облака на ваши устройства Интернета вещей. Из этой статьи вы узнаете, как использовать облачный интерфейс для удаленного развертывания готового кода на устройстве IoT Edge.

Если у вас еще нет подписки Azure, перед началом работы [создайте бесплатную учетную запись Azure][lnk-account].

## <a name="prerequisites"></a>Предварительные требования

Для целей нашего руководства вам потребуется использовать свой компьютер или виртуальную машину для имитации работы устройства Интернета вещей. Для успешного развертывания устройства IoT Edge необходимы следующие службы:

- [Установите среду Docker для Linux][lnk-docker-ubuntu] и убедитесь, что она выполняется. 
- Большинство дистрибутивов Linux, включая Ubuntu, уже содержат установленный пакет Python 2.7. С помощью команды `sudo apt-get install python-pip` убедитесь, что у вас установлена система pip.

## <a name="create-an-iot-hub-with-azure-cli"></a>Создание Центра Интернета вещей с помощью Azure CLI

Создайте в своей подписке Azure Центр Интернета вещей. Для целей этого руководства можно использовать бесплатный уровень. Если вы уже использовали бесплатный Центр Интернета вещей и он у вас сохранился, можете пропустить этот раздел и перейти сразу к разделу [Регистрация устройства IoT Edge][anchor-register]. В подписке может быть только один бесплатный Центр Интернета вещей. 

1. Войдите на [портал Azure][lnk-portal]. 
1. Нажмите кнопку **Cloud Shell**. 

   ![Кнопка Cloud Shell][1]

1. Создайте группу ресурсов. Следующий пример кода создает группу ресурсов с именем **IoTEdge** в регионе **Западная часть США**.

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Создайте в новой группе ресурсов Центр Интернета вещей. Следующий код создает бесплатный центр **F1** с именем **MyIotHub** в группе ресурсов **IoTEdge**.

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Регистрация устройства IoT Edge

Создайте удостоверение для своего имитированного устройства, чтобы оно могло обмениваться данными с Центром Интернета вещей. Так как устройства IoT Edge отличаются от стандартных устройств Интернета вещей поведением и управлением, объявите свое устройство устройством IoT Edge с самого начала. 

1. Найдите нужный Центр Интернета вещей на портале Azure.
1. Выберите **IoT Edge (preview)** (IoT Edge (предварительная версия)).
1. Выберите **Add IoT Edge device** (Добавить устройство IoT Edge).
1. Присвойте имитированному устройству уникальный идентификатор.
1. Щелкните **Сохранить**, чтобы добавить свое устройство.
1. Выберите в списке свое новое устройство. 
1. Скопируйте значение параметра **Строка подключения — первичный ключ** и сохраните его. Это значение потребуется для настройки среды выполнения IoT Edge в следующем разделе. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Установка и запуск среды выполнения IoT Edge

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Она состоит из двух модулей. Первый из них, агент IoT Edge, упрощает развертывание и мониторинг модулей на устройстве IoT Edge. Второй, центр IoT Edge, управляет взаимодействием между модулями на устройстве IoT Edge и между устройством и Центром Интернета вещей. 

На компьютере, на котором вы планируете запустить устройство IoT Edge, скачайте скрипт управления IoT Edge.
```python
sudo pip install -U azure-iot-edge-runtime-ctl
```

Для среды выполнения укажите строку подключения устройства IoT Edge, о которой шла речь в предыдущем разделе.
```python
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Запустите среду выполнения.
```python
sudo iotedgectl start
```

В Docker убедитесь, что агент IoT Edge работает как модуль.
```python
sudo docker ps
```

## <a name="deploy-a-module"></a>Развертывание модуля

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом руководстве мы создали новое устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure мы отправили на устройство и запустили модуль IoT Edge, не изменяя на устройстве никаких настроек. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

Убедитесь, что сообщения отправляются из модуля tempSensor.

```cmd/sh
sudo docker logs -f tempSensor
```

Вы также можете просмотреть данные телеметрии, которые передает устройство, используя [обозреватель Центра Интернета вещей][lnk-iothub-explorer]. 

## <a name="clean-up-resources"></a>Удаление ресурсов

Если созданный Центр Интернета вещей вам больше не нужен, удалите этот ресурс и все связанные с ним устройства с помощью команды [az iot hub delete][lnk-delete].

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как развернуть модуль IoT Edge на устройстве IoT Edge. Теперь потренируйтесь развертывать как модули службы Azure других типов, чтобы получить возможность анализировать данные на пограничных устройствах. 

* [Развертывание собственного кода в качестве модуля](tutorial-csharp-module.md)
* [Развертывание функции Azure в качестве модуля](tutorial-deploy-function.md)
* [Развертывание Azure Stream Analytics в качестве модуля](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
