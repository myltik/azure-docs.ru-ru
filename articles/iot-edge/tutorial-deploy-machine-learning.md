---
title: "Развертывание службы \"Машинное обучение Azure\" для Azure IoT Edge | Документация Майкрософт"
description: "Развертывание службы \"Машинное обучение Azure\" в качестве модуля на пограничном устройстве"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/13/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 2afdb257421b1333f451eb9d0dd4c2af5a12e946
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Развертывание службы "Машинное обучение Azure" в качестве модуля IoT Edge (предварительная версия)

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. Этот учебник поможет выполнить развертывание модуль машинного обучения Azure, которая прогнозирует, если устройство не работает на основе данных датчика на имитацию IoT пограничного устройства, созданный в [развертывания Azure IoT Edge на имитацию устройство Windows] [ lnk-tutorial1-win] или [Linux] [ lnk-tutorial1-lin] учебники. 

Из этого руководства вы узнаете, как выполнять такие задачи: 

> [!div class="checklist"]
> * Создание модуля машинного обучения Azure
> * Принудительная контейнера модуля в реестре контейнера Azure
> * Развертывание модуля машинного обучения Azure на устройстве IoT Edge
> * Просмотр сформированных данных

Модуль машинного обучения Azure, вы создаете в этом учебнике считывает температуры данные, созданные вашим устройством и только отправка сообщений в восходящем направлении центр IoT Azure при Прогнозируется сбой (называемые аномалий). 


## <a name="prerequisites"></a>Технические условия

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством или первым руководством.
* Строка подключения Центра Интернета вещей, к которому подключается ваше устройство IoT Edge.
* Учетная запись машинного обучения Azure. Чтобы создать учетную запись, следуйте инструкциям в [машинного обучения Azure создайте учетные записи и установить Azure Machine Learning Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-accounts). Необходимо установить приложение рабочей среды для этого учебника. 
* Модуль управления для машинного Обучения Azure на вашем компьютере. Чтобы настроить среду и создать учетную запись, следуйте инструкциям в [настройку модели управления](https://docs.microsoft.com/en-us/azure/machine-learning/preview/deployment-setup-configuration).

## <a name="create-the-azure-ml-container"></a>Создание контейнера машинного обучения Azure
В этом разделе загрузки файлов обученной модели и преобразовать их в контейнер Azure ML.  

На компьютере под управлением модуля управления для машинного Обучения Azure, загрузите и сохраните [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) и [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) из набора средств Azure ML IoT на GitHub. Эти файлы определяют обученной модели машинного обучения, который будет развернут на ваше устройство Iot Edge. 

Позволяет создать контейнер, который можно развернуть на устройствах IoT Edge обученной модели.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```
Имя службы *machinelearningmodule* в этом примере становится именем образа контейнера docker.

### <a name="view-the-container-repository"></a>Просмотреть репозиторий контейнера

Убедитесь, что образ контейнера был успешно создан и сохраняются в хранилище Azure контейнер, связанный с вашей средой машины обучения.

1. На [портал Azure](https://portal.azure.com), перейдите в меню **все службы** и выберите **контейнера реестры**.
2. Установите в реестре. Имя должно начинаться с **mlcr** и он принадлежит группе ресурсов, расположение и подписку, которая используется для настройки модуля управления.
3. Выберите **ключи доступа**
4. Копировать **серверу входа в систему**, **Username**, и **пароль**.  Необходимые для доступа к реестру с устройств Edge.
5. Выберите **репозитории**
6. Выберите **machinelearningmodule**
7. Теперь у вас есть полный путь к образу контейнера. Запишите этот путь к изображению в следующем разделе. Он должен выглядеть следующим образом: **.azureacr.io/machinelearningmodule:1 < registry_name >**

## <a name="add-registry-credentials-to-your-edge-device"></a>Добавление учетных данных реестра на устройство Edge

Добавьте учетные данные для своего реестра в среду выполнения Edge на компьютере, где запущено устройство Edge. Эта команда предоставляет доступ среды выполнения для контейнера по запросу.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>Запуск решения

1. Найдите нужный Центр Интернета вещей на [портале Azure](https://portal.azure.com).
1. Щелкните **IoT Edge (preview)** (IoT Edge (предварительная версия)) и выберите устройство IoT Edge.
1. Щелкните **Set modules** (Настроить модули).
1. Если вы ранее развернули модуля tempSensor на ваше устройство пограничного IoT, это может быть автоматическое заполнение. Если она отсутствует в списке модулей, добавьте его.
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `tempSensor`.
    3. В поле **URI образа** введите `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Щелкните **Сохранить**.
1. Добавьте машинного обучения модуль, который вы создали.
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    1. В **имя** введите`machinelearningmodule`
    1. В поле **Образ** введите адрес образа, например `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Щелкните **Сохранить**.
1. В **Добавление модулей** шаг, выберите **Далее**.
1. На шаге **Specify Routes** (Указание маршрутов) скопируйте приведенный ниже код JSON в текстовое поле. Первый маршрут передает сообщения от датчика температуры в модуль машинного обучения через конечную точку amlInput, являющуюся конечной точкой, используемой всеми модулями службы "Машинное обучение Azure". Второй маршрут передает сообщения из модуля машинного обучения в Центр Интернета вещей. В этом маршруте '' amlOutput'' является конечной точкой, все модули машинного обучения Azure используется для вывода данных и '' выше $'' обозначает центр IoT. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Щелкните **Далее**. 
1. В окне **Review Template** (Проверка шаблона) выберите **Отправить**. 
1. Вернитесь на страницу сведений об устройстве и выберите **Обновить**.  Вы увидите новый **machinelearningmodule** под управлением вместе с **tempSensor** модуль и модули среды IoT Edge.

## <a name="view-generated-data"></a>Просмотр сформированных данных

Можно просматривать сообщения устройства в облако, которые отправляет IoT пограничного устройства с помощью набора средств Azure IoT расширения для Visual Studio Code. 

1. В коде Visual Studio, выберите **устройствами IoT Hub**. 
2. Выберите **...**  выберите **задать строку подключения концентратора IoT** в меню. 

   ![Устройствами IoT Hub дополнительные меню](./media/tutorial-deploy-machine-learning/set-connection.png)

3. В текстовом поле, которое открывается в верхней части страницы введите строку подключения iothubowner для вашего центра IoT. IoT Edge устройство должно появиться в списке устройств IoT Hub.
4. Выберите **...**  еще раз выберите **Запуск наблюдения за сообщения D2C**.
5. Просмотрите сообщения, поступающие от tempSensor каждые пять секунд, какие machinelearningmodule добавляет к его оценки работоспособности устройства. 

   ![Azure ML ответа в текст сообщения](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы развернули модуль IoT Edge на базе службы "Машинное обучение Azure". Вы можете перейти к любому из оставшихся руководств, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Развертывание функции Azure в качестве модуля](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
