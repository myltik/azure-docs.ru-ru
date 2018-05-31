---
title: Развертывание службы "Машинное обучение Azure" для Azure IoT Edge | Документация Майкрософт
description: Развертывание службы "Машинное обучение Azure" в качестве модуля на пограничном устройстве
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6062d8193ce8cf7edaff3187f5c0f7dd9968658b
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165746"
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Развертывание службы "Машинное обучение Azure" в качестве модуля IoT Edge (предварительная версия)

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве описывается развертывание модуля машинного обучения Azure, который прогнозирует, когда произойдет отказ устройства, на основе данных датчиков на имитированном устройстве IoT Edge, созданном в ходе [развертывания Azure IoT Edge на имитированном устройстве в Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin].

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание модуля машинного обучения Azure
> * Отправка контейнера модуля в реестр контейнеров Azure
> * Развертывание модуля машинного обучения Azure на устройстве IoT Edge
> * Просмотр сформированных данных

Модуль машинного обучения Azure, который вы создадите в этом руководстве, считывает сформированные устройством данные об окружающей среде и отмечает сообщения как аномальные или нет.

## <a name="prerequisites"></a>предварительным требованиям

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством или первым руководством.
* Строка подключения Центра Интернета вещей, к которому подключается ваше устройство IoT Edge.
* Учетная запись Студии машинного обучения Azure. Чтобы создать учетную запись, выполните инструкции из статьи [Create Azure Machine Learning accounts and install Azure Machine Learning Workbench](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts) (Создание учетной записи для службы "Машинное обучение Azure" и установка Azure Machine Learning Workbench). Для этого руководства не нужно устанавливать приложение рабочего места (Workbench). 
* Служба управления модулями для Azure ML на вашем компьютере. Чтобы настроить среду и создать учетную запись, выполните инструкции из раздела о [настройке управления моделью](../machine-learning/desktop-workbench/deployment-setup-configuration.md).

Модуль машинного обучения Azure не поддерживает процессоры ARM.

## <a name="create-the-azure-ml-container"></a>Создание контейнера машинного обучения Azure
В этом разделе выполняется скачивание файлов обученной модели и их преобразование в контейнер Azure ML.

На компьютере с запущенной службой управления модулями для Azure ML скачайте и сохраните файлы [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) и [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) из набора средств Azure ML IoT на GitHub. Эти файлы определяют обученную модель машинного обучения, которую вы развернете на своем устройстве Iot Edge.

С помощью обученной модели создайте контейнер, который можно развернуть на устройствах IoT Edge. Используйте эту команду для следующих задач:

   * регистрация модели;
   * Создайте манифест.
   * создание образа контейнера Docker с именем *machinelearningmodule*;
   * развертывание образа в кластере службы Azure Kubernetes (AKS).

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Просмотр репозитория контейнеров

Убедитесь, что образ контейнера создан и сохранен в репозитории контейнеров Azure, связанном с вашей средой машинного обучения.

1. На [портале Azure](https://portal.azure.com), перейдите в меню **Все службы** и выберите **Реестры контейнеров**.
2. Выберите ваш реестр. Имя должно начинаться с **mlcr** и относиться к группе ресурсов, расположению и подписке, которые использовались для настройки Module Management.
3. Выберите **Ключи доступа**.
4. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**.  Они потребуются для доступа к реестру с устройств Edge.
5. Выберите **Репозитории**
6. Выберите **machinelearningmodule**
7. Теперь у вас есть полный путь к образу контейнера. Запишите этот путь для использования в следующем разделе. Он должен выглядеть следующим образом: **<имя_реестра>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Добавление учетных данных реестра на устройство Edge

Добавьте учетные данные для своего реестра в среду выполнения Edge на компьютере, где запущено устройство Edge. Эта команда предоставляет среде выполнения доступ к контейнеру.

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
1. Если модуль tempSensor на вашем устройстве IoT Edge уже развернут, эти данные могут быть заполнены автоматически. Если его еще нет в списке модулей, добавьте его.
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    2. В поле **Имя** введите `tempSensor`.
    3. В поле **URI образа** введите `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Щелкните **Сохранить**.
1. Добавьте созданный модуль машинного обучения.
    1. Выберите **Add IoT Edge Modulе** (Добавить модуль IoT Edge).
    1. В поле **Имя** введите `machinelearningmodule`.
    1. В поле **Образ** введите адрес образа, например `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Щелкните **Сохранить**.
1. Вернитесь к окну **Добавление модулей** и нажмите кнопку **Далее**.
1. На шаге **Specify Routes** (Указание маршрутов) скопируйте приведенный ниже код JSON в текстовое поле. Первый маршрут передает сообщения от датчика температуры в модуль машинного обучения через конечную точку amlInput, являющуюся конечной точкой, используемой всеми модулями службы "Машинное обучение Azure". Второй маршрут передает сообщения из модуля машинного обучения в Центр Интернета вещей. В этом маршруте amlOutput — это конечная точка, используемая всеми модулями службы "Машинное обучение Azure" для вывода данных, а $upstream обозначает Центр Интернета вещей.

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
1. Вернитесь на страницу сведений об устройстве и выберите **Обновить**.  Должен отобразиться новый модуль **machinelearningmodule**, запущенный вместе с модулем **tempSensor** и модулями среды выполнения IoT Edge.

## <a name="view-generated-data"></a>Просмотр сформированных данных

Можно просматривать сообщения, которые отправляет устройство IoT Edge с устройства в облако, используя [Обозреватель Центра Интернета вещей](https://github.com/azure/iothub-explorer) или расширение Azure IoT Toolkit для Visual Studio Code.

1. В Visual Studio Code выберите **IoT Hub Devices** (Устройства Центра Интернета вещей).
2. Выберите меню **...**, затем — **Set IoT Hub Connection String** (Задать строку подключения Центра Интернета вещей).

   ![Дополнительное меню "Устройства Центра Интернета вещей"](./media/tutorial-deploy-machine-learning/set-connection.png)

3. В текстовое поле, которое откроется в верхней части страницы, введите строку подключения для вашего Центра Интернета вещей — iothubowner. Устройство IoT Edge должно появиться в списке устройств Центра Интернета вещей.
4. Выберите **...** еще раз, а затем выберите **Start monitoring D2C message** (Начать мониторинг сообщения D2C).
5. Просмотрите сообщения, поступающие от tempSensor каждые пять секунд. Текст сообщения содержит свойство с именем **anomaly**, которое модуль machinelearningmodule отображает со значением true или false. Свойство **AzureMLResponse** свойство содержит значение "OK", если модель выполнен успешно.

   ![Ответ Azure ML в теле сообщения](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы развернули модуль IoT Edge на базе службы "Машинное обучение Azure". Вы можете перейти к любому из оставшихся руководств, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Развертывание функции Azure в качестве модуля](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
