---
title: Краткое руководство по использованию Azure IoT Edge и Linux | Документация Майкрософт
description: Первое знакомство с Azure IoT Edge и запуск аналитики на имитированном пограничном устройстве
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a9cb627c4d8eff2226717dd675d24349730e90d5
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068729"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-or-mac-device---preview"></a>Краткое руководство. Развертывание первого модуля IoT Edge на устройстве Linux или Mac (предварительная версия)

Azure IoT Edge переносит мощь облака на ваши устройства Интернета вещей. Из этой статьи вы узнаете, как использовать облачный интерфейс для удаленного развертывания готового кода на устройстве IoT Edge.

Если у вас еще нет подписки Azure, перед началом работы [создайте бесплатную учетную запись Azure][lnk-account].

## <a name="prerequisites"></a>предварительным требованиям

При работе с этим кратким руководством ваш компьютер или виртуальная машина используется как устройство Интернета вещей. Чтобы преобразовать компьютер в устройство IoT Edge, требуются следующие службы:

* PIP на Python для установки среды выполнения IoT Edge.
   * Для Linux — `sudo apt-get install python-pip`.
      
      > [!Note]
      > Для некоторых дистрибутивов (например, Raspbian) нужно также обновить определенные пакеты PIP и установить дополнительные зависимости:
      
        ```
        sudo pip install --upgrade setuptools pip
        sudo apt-get install python2.7-dev libffi-dev libssl-dev
        ```
        
   * Для MacOS — `sudo easy_install pip`.
* Docker для запуска модулей IoT Edge.
   * [Установите среду Docker для Linux][lnk-docker-ubuntu] и убедитесь, что она работает. 
   * [Установите среду Docker для Mac][lnk-docker-mac] и убедитесь, что она работает. 

## <a name="create-an-iot-hub-with-azure-cli"></a>Создание Центра Интернета вещей с помощью Azure CLI

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
```bash
sudo pip install -U azure-iot-edge-runtime-ctl
```

Для среды выполнения укажите строку подключения устройства IoT Edge, о которой шла речь в предыдущем разделе.
```bash
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Запустите среду выполнения.
```bash
sudo iotedgectl start
```

В Docker убедитесь, что агент IoT Edge работает как модуль.
```bash
sudo docker ps
```

![Просмотр информации об агенте IoT Edge в Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Развертывание модуля

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Просмотр сформированных данных

В этом руководстве мы создали новое устройство IoT Edge и установили на нем среду выполнения IoT Edge. Затем с помощью портала Azure мы отправили на устройство и запустили модуль IoT Edge, не изменяя на устройстве никаких настроек. В этом примере отправленный модуль создает данные среды, которые можно использовать для работы с руководствами. 

Еще раз откройте командную строку на компьютере, на котором работает имитированное устройство. Убедитесь, что развернутый из облака модуль работает на вашем устройстве IoT Edge.

```bash
sudo docker ps
```

![Просмотр трех модулей на устройстве](./media/tutorial-simulate-device-linux/docker-ps2.png)

Убедитесь, что сообщения отправляются из модуля tempSensor в облако.

```bash
sudo docker logs -f tempSensor
```

![Просмотр получаемых с модуля данных](./media/tutorial-simulate-device-linux/docker-logs.png)

Вы также можете просмотреть данные телеметрии, которые передает устройство, используя [обозреватель Центра Интернета вещей][lnk-iothub-explorer]. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить имитированное устройство, которое вы создали, а также контейнеры Docker, запущенные для каждого модуля, используйте следующую команду: 

```bash
sudo iotedgectl uninstall
```

Если созданный Центр Интернета вещей вам больше не нужен, используйте команду [az iot hub delete][lnk-delete], чтобы удалить этот ресурс и связанные с ним устройства.

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как развернуть модуль IoT Edge на устройстве IoT Edge. Теперь потренируйтесь развертывать службы Azure разных типов как модули, чтобы анализировать данные пограничного устройства: 

* [Развертывание собственного кода в качестве модуля](tutorial-csharp-module.md)
* [Развертывание функции Azure в качестве модуля](tutorial-deploy-function.md)
* [Развертывание Azure Stream Analytics в качестве модуля](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
