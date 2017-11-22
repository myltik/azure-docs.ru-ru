---
title: "Развертывание службы \"Машинное обучение Azure\" для Azure IoT Edge | Документация Майкрософт"
description: "Развертывание службы \"Машинное обучение Azure\" в качестве модуля на пограничном устройстве"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9b8475dcc51fb24fadd1faa4a2008b25a4464080
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Развертывание службы "Машинное обучение Azure" в качестве модуля IoT Edge (предварительная версия)

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве описывается развертывание модуля машинного обучения Azure, который прогнозирует, когда произойдет отказ устройства, на основе данных датчиков на имитированном устройстве IoT Edge, созданном по инструкциям из руководств по развертыванию Azure IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin]. Вы узнаете, как выполнять следующие задачи: 

> [!div class="checklist"]
> * Развертывание модуля машинного обучения Azure на устройстве IoT Edge
> * Просмотр сформированных данных

Если вы хотите использовать в решении собственную модель [машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/preview/), следует [развернуть модель](https://aka.ms/aml-iot-edge-doc) для IoT Edge и разместить ее в реестре контейнеров, например в [реестре контейнеров Azure](../container-registry/index.yml) или Docker.

## <a name="prerequisites"></a>Предварительные требования

* Устройство Azure IoT Edge, которое вы создали при работе с кратким руководством или первым руководством.
* Строка подключения Центра Интернета вещей, к которому подключается ваше устройство IoT Edge.
* Контейнер машинного обучения Azure.

## <a name="create-the-azure-ml-container"></a>Создание контейнера машинного обучения Azure
Чтобы создать контейнер машинного обучения Azure, следуйте инструкциям из статьи, посвященной [набору средств для работы с искусственным интеллектом для Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).

## <a name="run-the-solution"></a>Запуск решения

1. Найдите нужный Центр Интернета вещей на [портале Azure](https://portal.azure.com).
1. Щелкните **IoT Edge (preview)** (IoT Edge (предварительная версия)) и выберите устройство IoT Edge.
1. Щелкните **Set modules** (Настроить модули).
1. Выберите **Add IoT Edge modulе** (Добавить модуль IoT Edge).
1. В поле **Имя** введите `tempSensor`.
1. В поле **URI образа** введите `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
1. Оставьте без изменений другие параметры и выберите **Сохранить**.
1. Оставаясь на шаге **Add Modules** (Добавление модулей), выберите **Add IoT Edge modulе** (Добавить модуль IoT Edge) еще раз.
1. В поле **Имя** введите имя контейнера, созданного в предыдущем разделе. Чтобы узнать, как найти это имя, см. статью о [наборе средств для работы с искусственным интеллектом для Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).
1. В поле **Образ** введите URI образа контейнера, созданного в предыдущем разделе. Чтобы узнать, как найти этот образ, см. статью о [наборе средств для работы с искусственным интеллектом для Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).
1. Щелкните **Сохранить**.
1. Вернитесь к шагу **Add Modules** (Добавление модулей) и щелкните **Next** (Далее).
1. Обновите маршруты для модуля:
1. На шаге **Specify Routes** (Указание маршрутов) скопируйте приведенный ниже код JSON в текстовое поле. Модули публикуют все сообщения в среду выполнения Edge. Декларативные правила в среде выполнения определяют, куда отправляются эти сообщения. Для работы с этим руководством необходимы два маршрута. Первый маршрут передает сообщения от датчика температуры в модуль машинного обучения через конечную точку mlInput, являющуюся конечной точкой, используемой всеми модулями машинного обучения Azure. Второй маршрут передает сообщения из модуля машинного обучения в Центр Интернета вещей. В этом маршруте mlOutput — конечная точка, используемая всеми модулями машинного обучения Azure для вывода данных, а upstream — специальное место назначения, которое указывает концентратору Edge отправлять сообщения в Центр Интернета вещей. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Щелкните **Далее**. 
1. На шаге Review Template (Проверка шаблона) щелкните Submit (Отправить). 
1. Вернитесь на страницу сведений об устройстве и щелкните Refresh (Обновить).  Должен отобразиться новый модуль machinelearningmodule, работающий вместе с модулем tempSensor и средой выполнения IoT Edge.

## <a name="view-generated-data"></a>Просмотр сформированных данных

 Чтобы отслеживать данные, поступающие в Центр Интернета вещей, в среде VS Code используйте команду меню  **Вид | Палитра команд | IoT: Start Monitoring D2C Messages** (Интернет вещей: начать мониторинг сообщений D2C). 

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы развернули модуль IoT Edge на базе службы "Машинное обучение Azure". Вы можете перейти к любому из оставшихся руководств, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Развертывание функции Azure в качестве модуля](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md